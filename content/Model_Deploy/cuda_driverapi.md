---
title: "CUDA篇-Cuda Driver API"
date: 2024-02-10T10:17:43Z
draft: false
author: 李双双
tags:
image: /images/img2.png
description:
toc:
---

### 一、Driver API
CUDA Driver是GPU驱动级底层API随显卡驱动发布，CUDA Driver的头文件与动态库对应于：cuda.h 和 libcuda.so
Driver API 主要知识点是 Context 的管理机制 以及 CUDA 系列接口的开发习惯(错误检查方法), 还有内存模型。
    
参考连接：[nvcc, cuda driver,cudatoolkit,cudnn](https://www.cnblogs.com/marsggbo/p/11838823.html "Visit nvcc，cuda driver,cudatoolkit,cudnn")

<center class ='driver_img1'>
<img title="driver_img1" src="/images/modeldeploy/img_global.png" width="45%" height="45%">
</center>

Driver API是与显卡沟通的底层API，但是人们发现Driver API太过底层，由此引入了Runtime API。从图中可以看出 Runtime API 是基于 Driver API 开发的，我们日常中见到的 cudaMalloc()、cudaMemset()、cudaMemcpy() 都属于 Runtime API。

值得注意的是，cuda.h 是 NVIDIA CUDA Toolkit 中的一部分。CUDA Toolkit 是 NVIDIA 提供的用于开发 GPU 加速应用程序的软件开发工具包，其中包含了用于编译和执行 CUDA 程序的各种库和头文件。而 libcuda.so 是 NVIDIA 显卡驱动安装到系统中时随之安装的一个共享库文件，提供了 CUDA 运行时所需的底层功能和支持。


#### 1.1 context与内存
- 手动管理的 context，cuCtxCreate()（手动管理，以堆栈方式 push/pop）
- 自动管理的 context，cuDevicePrimaryCtxRetain（自动管理，runtime api 以此为基础）
- CPU 内存，称之为 Host Memory。又可以分为 Pageable Memory：可分页内存 + Page-Locked Memory：页锁定内存
- GPU 内存，称之为 Device Memory。又可以分为 Global Memory：全局内存 + Shared Memory：共享内存 + 其它多种内存

<center class ='driver_img1'>
<img title="driver_img1" src="/images/modeldeploy/img1.png" width="45%" height="45%">
</center>

#### 1.2 初始化和检查，CUDA错误检查
- 初始化  
    - cuInit 的意义是初始化驱动 API，如果不执行，则所有 API 都将返回错误，全局执行一次即可没有对应的 cuDestroy，不需要释放，程序销毁自动释放
    - cuInit 函数的参数 flags 目前必须为 0，在使用 CUDA 的其它函数之前，必须先调用 cuInit 函数进行初始化，否则其它 CUDA API 函数会返回 CUDA_ERROR_NOT_INITIALIZED 错误。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++

// CUDA驱动头文件cuda.h
#include <cuda.h>
#include <stdio.h> // 因为要使用printf
#include <string.h>
int main(){
    /* 
    cuInit(int flags), 这里的flags目前必须给0;
        对于cuda的所有函数，必须先调用cuInit，否则其他API都会返回CUDA_ERROR_NOT_INITIALIZED
        https://docs.nvidia.com/cuda/archive/11.2.0/cuda-driver-api/group__CUDA__INITIALIZE.html*/
    CUresult code=cuInit(0);  //CUresult 类型：用于接收一些可能的错误代码
    if(code != CUresult::CUDA_SUCCESS){
        const char* err_message = nullptr;
        cuGetErrorString(code, &err_message);    // 获取错误代码的字符串描述
        // cuGetErrorName (code, &err_message);  // 也可以直接获取错误代码的字符串
        printf("Initialize failed. code = %d, message = %s\n", code, err_message);
        return -1;
    }
    /* 
    测试获取当前cuda驱动的版本
    显卡、CUDA、CUDA Toolkit
        1. 显卡驱动版本，比如：Driver Version: 460.84
        2. CUDA驱动版本：比如：CUDA Version: 11.2
        3. CUDA Toolkit版本：比如自行下载时选择的10.2、11.2等；这与前两个不是一回事, CUDA Toolkit的每个版本都需要最低版本的CUDA驱动程序
        三者版本之间有依赖关系, 可参照https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html
        nvidia-smi显示的是显卡驱动版本和此驱动最高支持的CUDA驱动版本 */
    int driver_version = 0;
    code = cuDriverGetVersion(&driver_version);  // 获取驱动版本   返回CUresult的类型
    printf("CUDA Driver version is %d\n", driver_version); // 若driver_version为11020指的是11.2

    // 测试获取当前设备信息
    char device_name[100]; // char 数组
    CUdevice device = 0;
    code = cuDeviceGetName(device_name, sizeof(device_name), device);  // 获取设备名称、型号如：Tesla V100-SXM2-32GB // 数组名device_name当作指针 //返回CUresult的类型
    printf("Device %d name is %s\n", device, device_name);
    return 0;
}

```
</details>

- CUDA错误检查
    - 通过封装宏定义函数，cuGetErrorName与cuGetErrorString实现错误代码的解析与打印。
    - 这段代码对检查 CUDA 驱动的功能进行了进一步的完善。使用了函数封装的方式替代了宏定义，提供了代码的可读性和可维护性。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
// CUDA驱动头文件cuda.h
#include <cuda.h>
#include <stdio.h>
#include <string.h>
// 很明显，这种代码封装方式，更加的便于使用
// 宏定义 #define <宏名>（<参数表>） <宏体>
#define checkDriver(op)  __check_cuda_driver((op), #op, __FILE__, __LINE__)

bool __check_cuda_driver(CUresult code, const char* op, const char* file, int line){
    if(code != CUresult::CUDA_SUCCESS){    
        const char* err_name = nullptr;    
        const char* err_message = nullptr;  
        cuGetErrorName(code, &err_name);    
        cuGetErrorString(code, &err_message);   
        printf("%s:%d  %s failed. \n  code = %s, message = %s\n", file, line, op, err_name, err_message);   
        return false;
    }
    return true;
}

int main(){
    // 检查cuda driver的初始化
    // 实际调用的是__check_cuda_driver这个函数
    checkDriver(cuInit(0));

    // 测试获取当前cuda驱动的版本
    int driver_version = 0;
    if(!checkDriver(cuDriverGetVersion(&driver_version))){
        return -1;
    }
    printf("Driver version is %d\n", driver_version);

    // 测试获取当前设备信息
    char device_name[100];
    CUdevice device = 0;
    checkDriver(cuDeviceGetName(device_name, sizeof(device_name), device));
    printf("Device %d name is %s\n", device, device_name);
    return 0;
}
```
</details>


#### 1.3 CUcontext 
- context是一种上下文，关联对GPU的所有操作
- context与一块显卡关联，一个显卡可以被多个context关联
- 每个线程都有一个栈结构储存context，栈顶是当前使用的context，对应有push、pop函数操作context的栈，所有api都当前context为操作目标
- 试想一下，如果执行任何操作你都需要传递一个device决定送到哪个设备执行，得多麻烦

<center class ='driver_img2'>
<img title="driver_img2" src="/images/modeldeploy/img3.png" width="65%" height="65%">
</center>

    - 由于高频操作，是一个线程基本固定访问一个显卡不变，且只使用一个context，很少会用到多context
    - CreateContext、PushCurrent、PopCurrent这种多context管理就显得麻烦，还得再简单
    - 因此推出了cuDevicePrimaryCtxRetain，为设备关联主context，分配、释放、设置、栈都不用你管
    - primaryContext：给我设备id，给你context并设置好，此时一个显卡对应一个primary context
    - 不同线程，只要设备id一样，primary context就一样。context是线程安全的

<center class ='driver_img3'>
<img title="driver_img3" src="/images/modeldeploy/img3.png" width="65%" height="65%">
</center>
cuDevicePrimaryCtxRetain 函数将设备的主要上下文（primary context）与特定设备关联起来。接下来的内存分配、释放和数据拷贝函数调用将使用该主要上下文进行操作，而无需显式地设置当前上下文。这种情况下，不需要显式地管理上下文栈，代码更加简洁。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
// CUDA驱动头文件cuda.h
#include <cuda.h>   // include <> 和 "" 的区别    
#include <stdio.h>  // include <> : 标准库文件 
#include <string.h> // include "" : 自定义文件  详细情况请查看 readme.md -> 5
#define checkDriver(op)  __check_cuda_driver((op), #op, __FILE__, __LINE__)

bool __check_cuda_driver(CUresult code, const char* op, const char* file, int line){
    if(code != CUresult::CUDA_SUCCESS){    // 如果 成功获取CUDA情况下的返回值 与我们给定的值(0)不相等， 即条件成立， 返回值为flase
        const char* err_name = nullptr;    // 定义了一个字符串常量的空指针
        const char* err_message = nullptr;  
        cuGetErrorName(code, &err_name);    
        cuGetErrorString(code, &err_message);   
        printf("%s:%d  %s failed. \n  code = %s, message = %s\n", file, line, op, err_name, err_message); //打印错误信息 
        return false;
    }
    return true;
}

int main(){
    // 检查cuda driver的初始化
    checkDriver(cuInit(0));
    // 为设备创建上下文
    CUcontext ctxA = nullptr;                                   // CUcontext 其实是 struct CUctx_st*（是一个指向结构体CUctx_st的指针）
    CUcontext ctxB = nullptr;
    CUdevice device = 0;
    checkDriver(cuCtxCreate(&ctxA, CU_CTX_SCHED_AUTO, device)); // 这一步相当于告知要某一块设备上的某块地方创建 ctxA 管理数据。输入参数 参考 https://www.cs.cmu.edu/afs/cs/academic/class/15668-s11/www/cuda-doc/html/group__CUDA__CTX_g65dc0012348bc84810e2103a40d8e2cf.html
    checkDriver(cuCtxCreate(&ctxB, CU_CTX_SCHED_AUTO, device)); // 参考 1.ctx-stack.jpg
    printf("ctxA = %p\n", ctxA);
    printf("ctxB = %p\n", ctxB);
    /*  contexts 栈：
        ctxB -- top <--- current_context
        ctxA 
        ...
     */
    // 获取当前上下文信息
    CUcontext current_context = nullptr;
    checkDriver(cuCtxGetCurrent(&current_context));             // 这个时候current_context 就是上面创建的context
    printf("current_context = %p\n", current_context);
    // 可以使用上下文堆栈对设备管理多个上下文
    // 压入当前context
    checkDriver(cuCtxPushCurrent(ctxA));                        // 将这个 ctxA 压入CPU调用的thread上。专门用一个thread以栈的方式来管理多个contexts的切换
    checkDriver(cuCtxGetCurrent(&current_context));             // 获取current_context (即栈顶的context)
    printf("after pushing, current_context = %p\n", current_context);
    /*  contexts 栈：
        ctxA -- top <--- current_context
        ctxB
        ...
    */
    // 弹出当前context
    CUcontext popped_ctx = nullptr;
    checkDriver(cuCtxPopCurrent(&popped_ctx));                   // 将当前的context pop掉，并用popped_ctx承接它pop出来的context
    checkDriver(cuCtxGetCurrent(&current_context));              // 获取current_context(栈顶的)
    printf("after poping, popped_ctx = %p\n", popped_ctx);       // 弹出的是ctxA
    printf("after poping, current_context = %p\n", current_context); // current_context是ctxB

    checkDriver(cuCtxDestroy(ctxA));
    checkDriver(cuCtxDestroy(ctxB));

    // 更推荐使用cuDevicePrimaryCtxRetain获取与设备关联的context
    // 注意这个重点，以后的runtime也是基于此, 自动为设备只关联一个context
    checkDriver(cuDevicePrimaryCtxRetain(&ctxA, device));       // 在 device 上指定一个新地址对ctxA进行管理
    printf("ctxA = %p\n", ctxA);
    checkDriver(cuDevicePrimaryCtxRelease(device));
    return 0;
}
```

</details>
代码开始创建了两个上下文 ctxA 和 ctxB。通过调用 cuCtexCreate 函数来为特定设备（使用设备标识符 device）创建上下文。然后，代码使用 cuCtxGetCurrent 函数获取当前上下文，并打印其地址。可以看到，在刚创建上下文后，当前上下文与 ctxB 的地址相同。

接下来，代码通过 cuCtxPushCurrent 函数将 ctxA 压入上下文栈，成为当前上下文。然后使用 cuCtxPopCurrent 函数将当前上下文弹出，并用 popped_ctx 变量接收被弹出的上下文。再次调用 cuCtxGetCurrent 函数可以看到当前上下文变成了 ctxB，而 popped_ctx 中保存了被弹出的 ctxA。

最后，代码也演示了使用 cuDevicePrimaryCtxRetain 函数来自动管理 context。


#### 1.4 使用驱动API进行内存分配
    1.统一内存(Unified addressing) 
    2.分配线性内存cuMemAlloc():
        2.1. 线性内存：线性内存被组织在单个连续的地址空间中，可以直接以及线性地访问这些内存位置。
        2.2. 内存分配空间以字节为大小，并返回所分配的内存地址
    3.分配主机锁页内存cuMemAllocHost():
        强力推荐：https://leimao.github.io/blog/Page-Locked-Host-Memory-Data-Transfer/
        强力推荐：https://www.youtube.com/watch?v=p9yZNLeOj4s
        3.1. 锁页内存：
            定义：页面不允许被调入调出的叫锁页内存，反之叫可分页内存。
            有啥好处：快。
            a. 设备可以直接访问内存，与可分页内存相比，它的读写带宽要高得多
            b. 驱动程序会跟踪使用cuMemAllocHost()分配的虚拟内存范围，并自动加速对cuMemcpy（）等函数的调用。
            使用注意：分配过多锁业内存会减少系统可用于分页的内存量，可能会降低系统性能。因此，在主机和设备之间为数据交换分配临时区域时，最好少用此功能。
        3.2. 这里是从主机分配内存，因此不是输入device prt的地址，而是一个主机的二级地址。
            内存的初始化cuMemsetD32(CUdeviceptr dstDevice, unsigned int ui, size_t N), 将N个32位值的内存范围设置为指定的值ui内存的释放cuMemFreeHost(): 有借有还 再借不难~

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
// CUDA驱动头文件cuda.h
#include <cuda.h>
#include <typeinfo>
#include <stdio.h>
#include <string.h>

#define checkDriver(op)  __check_cuda_driver((op), #op, __FILE__, __LINE__)

bool __check_cuda_driver(CUresult code, const char* op, const char* file, int line){
    if(code != CUresult::CUDA_SUCCESS){    
        const char* err_name = nullptr;    
        const char* err_message = nullptr;  
        cuGetErrorName(code, &err_name);    
        cuGetErrorString(code, &err_message);   
        printf("%s:%d  %s failed. \n  code = %s, message = %s\n", file, line, op, err_name, err_message);   
        return false;
    }
    return true;
}

int main(){
    // 检查cuda driver的初始化
    checkDriver(cuInit(0));
    // 创建上下文
    CUcontext context = nullptr;
    CUdevice device = 0;
    checkDriver(cuCtxCreate(&context, CU_CTX_SCHED_AUTO, device));
    printf("context = %p\n", context);
    // 输入device prt向设备要一个100 byte的线性内存，并返回地址
    CUdeviceptr device_memory_pointer = 0;  // CUdeviceptr是unsigned integer(typedef unsigned int CUdeviceptr_v2)(typedef CUdeviceptr_v2 CUdeviceptr)
    checkDriver(cuMemAlloc(&device_memory_pointer, 100)); // 注意这是指向device的pointer, 
    printf("device_memory_pointer = %p\n", device_memory_pointer);
    // 输入二级指针向host要一个100 byte的锁页内存，专供设备访问。参考 2.cuMemAllocHost.jpg 讲解视频：https://v.douyin.com/NrYL5KB/
    float* host_page_locked_memory = nullptr;
    checkDriver(cuMemAllocHost((void**)&host_page_locked_memory, 100));  //(void **)是因为host_page_locked_memory是float *，需要转化为同类型指针。
    printf("host_page_locked_memory = %p\n", host_page_locked_memory);
    // 向page-locked memory 里放数据（仍在CPU上），可以让GPU可快速读取
    host_page_locked_memory[0] = 123;
    printf("host_page_locked_memory[0] = %f\n", host_page_locked_memory[0]);
    /*  记住这一点
        host page locked memory 声明的时候为float*型，可以直接转换为device ptr，这才可以送给cuda核函数（利用DMA(Direct Memory Access)技术）
        初始化内存的值: cuMemsetD32 ( CUdeviceptr dstDevice, unsigned int  ui, size_t N )
        初始化值必须是无符号整型，因此需要将new_value进行数据转换：
        但不能直接写为:(int)value，必须写为*(int*)&new_value, 我们来分解一下这条语句的作用：
        1. &new_value获取float new_value的地址
        (int*)将地址从float * 转换为int*以避免64位架构上的精度损失
        *(int*)取消引用地址，最后获取引用的int值 */
    float new_value = 555;
    checkDriver(cuMemsetD32((CUdeviceptr)host_page_locked_memory, *(int*)&new_value, 1)); //??? cuMemset用来干嘛？
    printf("host_page_locked_memory[0] = %f\n", host_page_locked_memory[0]);
    // 释放内存
    checkDriver(cuMemFreeHost(host_page_locked_memory));
    return 0;
}
```

</details>

CUDA文档：
- [cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive "Visit cuda-toolkit-archive")
- [cuda-docs](https://docs.nvidia.com/cuda/archive/11.2.0/ "Visit cuda-docs")
- [unified addressing](https://developer.nvidia.com/blog/unified-memory-cuda-beginners/ "Visit unified addressing")