---
title: "C++基础知识"
date: 2024-01-23T10:28:54Z
draft: false
---

## 1.1 命名空间
#### 一、命名空间概念
命名空间为了防止名字冲突而引入的一种机制。系统中可以定义多个命名空间，每个命名空间都有自己的名字，名字可以不同。大家可以把命名空间看成一个作用域，我们在这个命名空间里定义函数，和另一个命名空间中函数即便同名，也不影响。
    
    (1) 命名空间定义：
    namespace 命名空间名{
        ......
    }
    (2) 命名空间的定义可以不连续，甚至可以写在多个文件中。
    (3) 外界如何访问命名空间中的函数?
    格式：命名空间名::实体名          ----其中这::叫 ‘作用于运算符’
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
namespace zhangsan
{
    void radius()
    {
        printf("shangsan 的radius()函数");
    }
}
int main()
{
    zhangsan::radius();
    lisi::radius(); 
    return 0;  // main()中返回值一般表示状态，返回0表示成功，非0一般不表示错误
}
```
</details>


## 1.2 输入输出流
#### 二、基本的输入输出流
c++中我们不是用printf，使用cout,c++的标准库
(1) std:: 命名空间，标准库的命名空间
(2) std::endl 作用：
    a) 输出换行符 \n
    b) 强制刷新输出缓冲区，缓冲区的所有数据都被系统清除。
(3) std::cin 基本输入  >>输入运算符   <<输出运算符
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
int main()
{
    std::cout<<"testing"<< std::endl;
    return 0;  // main()中返回值一般表示状态，返回0表示成功，非0一般不表示错误
}
```
</code></pre>
</details>
    
    
## 1.3 auto、头文件防卫、引用与常量
#### 一、局部变量初始化
    随时使用随时定义，且变量初始化可以使用{}，也可以不用=
#### 二、auto 变量的自动类型推断
    auto可以在声明变量的时候根据变量的初始值类型自动推断变量的类型（声明时要赋初始值）
    auto自动类型推断发生在编译期间，所以使用auto不会造成程序的效率较低。 
#### 三、头文件防卫使声明
    #ifdef, ifndef 条件编译
    #ifndef 标识符  //当标识符没有定义过（没有用define定义过），则对程序段进行编译
        //程序段
    #endif
#### 四、引用
    (1) 引用理解成：为变量起了一个名字，一般用符号&表示，别名和变量名看成同一变量
    (2) 定义引用时候必须初始化
    (3) 定义引用并不占用内存，或理解成，引用和变量占用同一块内存。
    (4) 引用必须绑定到变量或对象上，不能绑定常量上
    (5) 引用被引用的变量之间要类型相同。
    (6) 引用作为函数的形参时，类似指针，可以改变外部的变量值。
#### 五、常量
    const关键字：表示不变的意思。
#### 六、constexpr 关键字 c++11引入，是个常量的概念， 在编译时求值
    (1) 函数体中必须有且只有一条return语句, 除了return语句之外，可以使用字面量、constexpr函数。不能调用 non-constexpr函数！
    (2) constexpr函数的形参可以是非常量，但是实参必须是常量
    (3) constexpr函数的返回值类型必须是字面值类型
    (4) 声明为constexpr的变量一定是一个const变量，而且必须用常量表达式初始化
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
using namespace std;
void fun(int &a, int &b){ //形参是引用
    a = 10;
    b = 20;
}
constexpr int function(){
    int abc = 6;
    int a4 = 6; //这里的变量必须初始化，不然报错
    for(int i = 0; i < 10; i++){
        //cout<< i << endl; //有变量会报错
    }
    return 5;
}
constexpr int function_l(){
    int abc = 6;
    int a4 = 6; //这里的变量必须初始化，不然报错
    for(int i = 0; i < 10; i++){
        cout<< i << endl; //有变量会报错
    }
    return 5;
}
int main()
{
   //一、局部变量初始化
   for(int i = 0; i < 10; i++){  // i的作用域仅限于for内部
       cout<< i << endl;
   }
   int a = 5; //定义的时候初始化
   int b = {5}; //可以不要等号
   cout<<"b= "<< b << endl;
   int c[] {1,2,3,4}; //可以不要等号去定义数组
   cout<< c[1] << endl;
   //int d {3.5f};// 无法编译成功，因为相当于浮点数向整数的转换
   //二、auto
   auto value = false;
   auto ch = "a";
   //四、引用
   int value_l = 10;
   int & refval = value_l; //<==> int* const refval = &value_l地址不变，值可以变 
   /*
   int & refval;
   int &refval = 10;
   float &refval = value_l; 都是错误的
   */
   //五、常量
   const int var = 7;
   int &var2 = (int &)var; 
   var2 = 18;
   cout<<"var= "<< var << endl;
   //六、constexpr
   constexpr int var_l = 1;
   int val_b = 20;
   constexpr int var_l1 = function();
   int var_k = function_l(); //常规调用，不会出错。
   return 0;  // main()中返回值一般表示状态，返回0表示成功，非0一般不表示错误
}
```
</code></pre>
</details>


## 1.4 范围for、new内存动态分配与nullptr
#### 一、范围for语句：用于遍历一个序列
    缺点：有拷贝动作，降低程序效率
#### 二、动态分配问题
    a) 供程序使用的存储空间，程序区、静态存储区、动态存储区
        (1) 栈区：一般函数内的局部变量与形参都会放在这里，由编译器自动分配与释放。
        (2) 堆区：程序员malloc/new分配，用free/delete释放。忘记释放后，系统会回收。
        (3) 全局/静态存储区: 放程序的全局变量或静态变量，程序结束后系统释放。
        (4) 常量区: 字符等。
        (5) 程序代码区。
    b) 堆和栈的区别
    	(1) 栈 空间有限，分配速度快
   	(2) 堆 只要不超出实际的物理内存，也在操作系统允许分配的内存大小范围内，都可以分配。
         分配速度比较慢，可以随时使用malloc/new分配，用free/delete释放，非常灵活。
    	(3) malloc和free，是函数。
    一般形式：
        void* malloc(int NumBytes): //NumBytes:要分配的字节数，分配成功则返回被分配内存的指针，分配失败，
        则返回NULL。
        当分配的内存不使用时，应该使用free()函数将内存释放掉。
        void free(void* FirstByte) 将之前用malloc分配的内存空间释放掉。
    (4) new和delete 是运算符, c++中使用new/delete分配与释放内存。
    一般形式：
        - 指针变量名 = new 类型标识符；
        - 指针类型名 = new 类型标识符(初始值) 
        - 指针类型名 = new 类型标识符[内存单元个数] //这里是数组
#### 三、nullptr   c++11中引入的关键字
    nullptr代表的是空指针。 NULL代表0，两者之间是不同的类型
    作用：用于判断空指针与整数之间的混淆
    (1) 在if判断的时候, NULL==nullptr
    (2) 对于指针的初始化，用nullptr取代NULL。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
using namespace std;
int main()
{
    //一、范围for语句
    int v[] {1,2,3,4,5}; //数组v中每个元素，依次放入x中并打印，打v中每个元素拷贝到x中，并打印x的值。
    for(auto x : v){
        cout<< x << endl;
    }
    for(auto x : {6,7,8,9,10}){
        cout<< x << endl;
    }
    for(auto &x : v){ //改进，省了拷贝动作，提高程序效率
        cout<< x << endl;
    }
    //二、动态分配问题-malloc/free
    int *p = NULL;
    p = (int *)malloc(sizeof(int));  //在堆中分配4个字节的内存
    if(p != NULL){
        //分配成功
        // *p = 50;
        // cout<<*p<< endl;
        // free(p); //释放掉，千万不要忘记。
        int*q = p;  // p==q   0x561216fef280
        *q++ = 1;   // 0x561216fef284 ==>*(q++): --->*q=1: q++
        *q++ = 5;
        cout<<*p<< endl;
        cout<< *(p+1)<< endl;
        free(p);
    }
    // 二、动态分配问题-new/delete
    // int *myint = new int; //整型指针变量
    int *myint = new int(18);
    if(myint != NULL){
        *myint = 8;
        cout<<"*myint: "<<*myint<< endl;
        delete myint;
    }
    int *myint_1 = new int[100]; // 开辟一个大小为100的整型数组空间
    if(myint_1 != NULL){
        int *q = myint_1;
        *q++ = 12;   //[0]=12, 执行完后，这个q其实已经指向了[1]
        *q++ = 18;   //[1]=18, 执行完后，这个q其实已经指向了[2]
        cout<< *myint_1<< endl;
        cout<<*(myint_1+1)<< endl;
    }
    delete [] myint_1; //释放数组的空间
    //三、nullptr
    char *p_l = NULL;
    char *q_l = nullptr;
    // int a = nullptr //错误，因为nullptr表示空指针。
    int b_l = NULL;
    if(p_l == nullptr){ //  在if判断的时候,NULL==nullptr
        cout<<"NULL == nullptr"<< endl;
    }
    return 0;  // main()中返回值一般表示状态，返回0表示成功，非0一般不表示错误
}
```
</code></pre>
</details>

## 1.5 结构、权限修饰符与类
#### 一、结构回顾
    结构：自定义的数据类型
    (1) 定义结构变量可以省略struct
    (2) 结构体作为函数参数时，值传递，拷贝动作，效率低。fuction(student tmpstu)
        改进的方式一引用：传递地址，引用传递，效率高。function_l(student &tmpstu)
        改进的方式二指针：传递指针，效率高。function_s(student* tmpstu)
    (3) C++中的结构与c中的区别
        C++中结构体除了可以定义成员变量外，还可以定义成员函数  struct student
    (4) public和private修饰符：public(共有)、private(私有)、protected（保护）
    public: 用这个修饰符修饰结构/类中的成员变量/成员函数，就可以被外界访问。
    private: 用这个修饰符修饰的结构/类中的成员变量/成员函数，只有被内部定义的成员函数才能使用。
#### 二、类
    (1) 不管在c++中还是c中，结构都使用struct定义
    (2) c语言中定义一个结构的变量，叫结构变量。c++中定义类的变量，叫对象。
    (3) 结构变量与对象，皆是一块能够储存数据并且具有某种类型的内存空间。
    (4) C++中结构体与类及其相似，区别有两点：
        a) c++结构内部的成员变量/成员函数 默认访问的级别都是public
        b) 类内部的成员变量/成员函数 默认的访问级别都是private
        c) c++结构体继承默认的是public，而类继承默认的是private
    (5) 可以在类中声明并定义成员函数，也可以在类内声明函数，类外定义函数。
#### 三、类的组织 - 书写规范
    (1) 类的声明代码会放在一个.h头文件中，头文件可以和类名相同。 eg:student.h
    (2) 类的具体实现代码，放在一个.cpp文件中       eg:student.cpp
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
using namespace std;
struct student{
public:
// private:    // 私有情况下，number与name外部无法访问，内部的成员函数func可以调用。
    int number;
    char name[100];
    void func(){
        number++;
        cout<<"调用成功"<< endl;
    }
};
void function(student tmpstu){ //这里结构体形参只是值传递，拷贝动作，效率低。
    tmpstu.number = 2000;
}
void function_l(student &tmpstu){
    tmpstu.number = 3000;
}
void function_s(student* tmpstu){
    tmpstu->number = 4000;
}
class student_l{
// private:    // 私有情况下，number与name外部无法访问，内部的成员函数func可以调用。
    int number;
    char name[100];
public:
    void func(){
        number++;
        cout<<"调用成功"<< endl;
    }
};
int main()
{
    //一、结构体
    student student1;
    student1.number = 1001;
    student1.func();
    cout<< student1.number << endl;
    function(student1);
    cout<< student1.number << endl;
    //二、类
    student_l stu;
    stu.func();
    // stu.number = 200; // 错误，因为默认类中的成员变量/成员函数是private
}
```
</code></pre>
</details>

## 1.6 函数新特性、inline内联函数与const详解
#### 一、函数回顾与后置返回类型
    (1) 函数定义中，形参如果在函数体内用不到的话，则可以不给形参变量名字，只给其类型。   func(int a, int)
    (2) 函数声明时，可以只有形参类型，没有形参名。  func(int, int)
    (3) 前置与后置返回类型
        a) 前置返回类型：把函数返回类型放在函数名之前。  auto func_l(int, int) -> void; 
        b) 后置返回类型：c++11中，后置返回类型就是在函数声明和定义中，把返回类型放在参数列表之后 
        c) 前面放auto, 表示函数返回类型放在参数列表之后，而放在参数列表之后的返回类型是通过 -> 开始的。  
            auto func_l(int a, int) -> void {}
#### 二、内联函数
    在函数定义前增加了关键字inline，导致该函数就变成了内联函数。
    如果调用函数体很小的函数，系统将形参压栈，保存函数信息，分配内存，出栈等内存操作行为。这时函数体很 小但是频繁调用会造成内存资源浪费。
    (1) inline影响编译器，在编译阶段对inline这种函数进行处理，系统尝试将调用该函数的动作替换为函数本体。通过这种方式，来提升性能。
        int abc = myfunc(); 替换为 int abc = 1;
    (2) inline只是开发者对编译器的建议，编译器可以尝试去做，也可以不做，这取决于编译器的诊断功能，我们控制不了。
    (3) 内联函数的定义放在头文件中。这样需要用到这个内联函数的.cpp文件都能够通过#include把这个内联函数的源代码#include。
        因为编译器要在编译阶段找到内联函数的本体，并尝试将函数的调用替换为函数体内的语句。
        优缺点：
            a) 代码膨胀问题
        因此函数体应尽量的小。
        注意：各种编译器对inline函数的处理各不相同，inline函数尽量简单，代码尽可能少。循环、分支、递归调用尽量不要出现。
        在inline函数中。 否则编译器很可能会因为你写的这些代码的原因拒绝让这个函数成为一个inline函数。
    (4) constexpr函数，可以看成更严格的一种内联函数。
    (5) #define也类似内联函数。
#### 三、函数杂合用法总结
    (1) void表示函数不返回任何类型。但是我们可以调用一个返回类型是void的函数，让它作为另一个 返回类型是void的函数的返回值。  funcb()
    (2) 函数返回指针，返回引用。（对于函数局部变量，函数不能返回局部变量的地址，因为函数调用结束后，系统回收局部变量。 int* myfunc_l()  int & myfunc_s()
    (3) 函数形参如果没有，可以写void --> int myfu(void) {return 1;}
    (4) 如果一个函数不调用，这个函数可以只有声明部分，没有定义部分。
    (5) 普通函数只能定义一次，声明可以声明多次。
    (6) 引用作函数形参，可以在函数内部直接改变外部传入的值, 并且c++中更习惯用引用类型的形参代替指针类型的形参（c语言中用指针类型形参）。
    (7) c++中函数允许同名，但是形参列表的参数类型或在数量应该不同。
#### 四、const char*、 char const *、 char * const的区别
    (1) const char* 表示常量指针，值不能改变 ,但是地址可以变。
    (2) char const* 等价于 const char*
    (3) char * const 表示指针常量，地址不能变，地址对应的值可以变。
#### 五、函数形参中带有const
    (1) 函数形参中写const，可以防止无意中修改了形参值导致实参值被修改。
    (2) 实参类型可以更灵活。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include "project3.hpp"
#include "project2.h"
using namespace std;
//一、函数回顾与后置返回类型
void func(int, int); //前置声明
void func(int a, int){  // 前置定义
    return;
}
auto func_l(int, int) -> void;  //后置声明
auto func_l(int a, int) -> void //后置定义
{
    return ;
}
// // 二、内联函数
// inline int myfunc(int testv){  //函数定义前加inline，这个普通函数就变成了内联函数。
//     return 1;
// }
//三、杂合函数
void funca(){}
void funcb(){
    return funca();  //可以
    // return ;  //可以
    // return void //错误
}
int* myfunc_l()
{
    int tempvalue_l = 20;
    return &tempvalue_l;  // 程序有巨大隐患，因为tempvalue是临时变量函数调用完后就释放内存。
}
int & myfunc_s(){
    int tempvalue = 20;
    return tempvalue;  // 程序有巨大隐患，因为tempvalue是临时变量函数调用完后就释放内存。
}
//五、函数形参中带有const
struct student{
    int num;
};
void fsa(student &stu){
    stu.num = 100;
}
void fsa_l(const student &stu){
    // stu.num = 100;
}
int main()
{
    //二、内联函数
    myfunc(2);
    return 0;
    //三、函数杂合用法总结
    int *p = myfunc_l();
    // *p = 6; // 你向一个不属于你的地址写入了数据。
    int &k = myfunc_s();
    // k = 10; // 你向一个不属于你的地址写入了数据。
    int s = myfunc_s(); 
    s = 20; //成功，因为&s的值不等于&tempvalue,所以s的内存是安全的。 
    //四、const char*、 char const *、 char * const的区别
    char str[] = "l love you";
    const char *p_l = str;
    // *p_l = "Y"; //错误
    p_l++;
    //五、函数形参中带有const
    student abc;
    abc.num = 101;
    fsa(abc);
    cout<< abc.num << endl; //100
    fsa_l(abc);
    cout<< abc.num << endl; //101
}
```
</code></pre>
</details>

## 1.7 string类型
#### 一、前言 int, float, char,c++标准库:string,vector
    string:可变长字符串。vector:一种集合或容器的概念。
#### 二、定义和初始化string对象
        - 默认初始化：string s1;  s1 = ""空字符串。
        - string s2 = "l love you" 等价于 string s2("l love you") 含义：将字符串内容拷贝到s2代表的一段内存中，拷贝时不包括末尾的\0
        - string s3 = s2; //表示把s2的内容拷贝到s3代表的一段内存中。
        - 用c语言的字符数组初始化string类对象。
#### 三、string对象的操作
    (1) 判断是否为空empty(), 返回布尔类型
    (2) size()/length(): 返回字节/字符数量 代表该字符串的长度。 unsigned int
    (3) s[n]: 返回s中第n个字符，n代表的是一个位置，0-size()-1
    (4) s1+s2:字符串的连接，返回连接后的结果，其实得到一个新的string对象。
    (5) s1 = s2; 字符串对象赋值，用s2内部的内容取代原来的内容。
    (6) s1 == s2;  判断两个字符串是否相等。大小写敏感，大小写字符必须也相同。
    (7) s1 != s2;  判断两个字符串是否不相等。大小写敏感。
    (8) s.c_str(): 返回一个字符串s中的内容指针。返回一个指向正规的c字符串的指针常量，也就是以\0结尾。
        这个函数的引入是为了与c语言兼容，在c语言中没有string类型，所以我们的通过string类对象的c_str()成员函数把string对象转换成c语言中字符串的形式。
    (9) 字面值和string相加   string s12 = "abc";  string s13 = s12 + " and";
        不容许直接字面值相加：string s14 = "abc" + "def"; //错误的，错误因为右边不知道是什么类型。必须有sting类型存在两者中间才行。
        string s15 = "abc" + "def" + s12; //错误，因为"abc" + "def"不行，类型不能转换。
    (10) 范围for针对string的使用：c++11中提供了范围for: 能够遍历一个序列中的每一个元素。
        string可以看成一个字符序列。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstring>
#include "project3.hpp"
#include "project2.h"
using namespace std;
int main()
{
    //二、定义和初始化string对象
    int num = 6;
    string s0(num, 'a'); //不推荐这种方式，因为系统会创建临时变量。浪费内存资源。
    //三、string对象的操作
    //三-(1)
    string s1;
    if(s1.empty()){
        cout<<" s1为空"<< endl;
    }
    //三-(2)size()/length()
    string s2;
    cout<< s2.size()<< endl;  // 0
    cout<< s2.length()<< endl; // 0
    string s3 = "我爱中国";
    cout<< s3.size()<< endl;  // 12
    cout<< s3.length()<< endl; // 12
    string s4 = "I Love China!";
    cout<< s4.size() << endl;  // 13              
    cout<< s4.length() << endl; // 13
    char str[100];
    strcpy(str, s4.c_str());
    string s10(str);
     cout<< s10 << endl; // 
    //三-(3)s[n]:
    string s5 = "l love china!";
    if(s5.size()){
        cout<< s5[4] << endl;  // v
        s5[4] = 'w';
    }
    cout<< s5 << endl;
    //三-(4)s1+s2:字符串的连接
    string s6 = "l";
    string s7 = "love";
    string s8 = s6 + s7;
    cout<< s8 << endl;
    //三-(8)s.c_str()
    string s9 = "abc";
    const char *p = s9.c_str();
    char ch[100];
    strcpy(ch, p);
    cout<< ch << endl;
    //三-(10)范围for针对string的使用
    string s16 = "l love you";
    for(auto &c : s16){  //auto:类型自动推断, char
        cout<< c << endl; //每次输出一个字符。
    }
}
```
</code></pre>
</details>


## 1.8 vector的使用
#### 一、vector类型简介：标准库：集合或在动态数组。我们可以把若干对象放在里边
   (1) vector可以存放其他对象，称作容器。
        a) vector<int>vjihe; 表示vjihe内部存放int型数据(int型对象)
        b) <int>:类模板，vector本身就是个类模板,<int>实际上就算类模板的实例化过程。
        c) vector<int> : 在vector之后加上一对<>,<>内部放上类型信息。
        d) eg: vector<student> stu; //student类 
        e) vector<vector<string>> stu1; //容器类型
        f) vector<int *>vjihe2; // 可以装指针
        g) vector<int &> vjihe3; // 不可以装引用。因为引用不是对象，是个别名。
#### 二、定义和初始化vector对象
    (1) 空vector
        vector<string> mystr; //创建一个string类型的空的对象（空容器）。
    (2) 元素拷贝的初始化方式
        vector<string> mystr2(mystr); //把mystr元素拷贝给了mystr2;
        vector<string> mystr3 = mystr; //把mystr元素拷贝给了mystr3;
    (3) c++11标准中，用列表初始化方法给值， 用{}括起来
        vector<string> mystr4 = {"aaa", "bbb", "ccc"};
    (4) 创建指定数量的元素，默认初始化初值为0
        vector<int> vec(15, -200);  //创建15个int类型的元素，每个元素都是-200
        vector<string> vec_l(5, "hello"); //创建5个string类型的元素，每个元素都司hello。
    (5) 多初始化方式, ()一般表示对象元素数量的概念，{}一般表示元素内容的概念。
        vector<int> vec_s(10); //表示10个元素， 每个元素值是缺省的0。
        vector<int> vec_b{10}; //表示一个元素，该元素的值为0。
        vector<string> vec_m{10};  //表示10个数量，每个元素为""。因为10不是string类型，因此系统认为是数量。
        vector<string> vec_n{10, "hello"}; //10个元素，每个元素的内容都是"hello"
        vector<int> vec_x{10, 1}; //2个元素， 第一个10，第二个1。
    注意：要想正常的通过{}进行初始化，那么{}里面的值的类型，必须要跟vector后的<>内类型相同。  
#### 三、vector对象上的操作：最常用的是
    (1) 判断是否为空empty(), 返回的是布尔值。
    (2) push_back :常用的方法，用于向vector中尾部增加一个元素。
    (3) size：返回元素个数
    (4) clear:移除所有元素，清空容器
    (5) v[n]:返回v中第n个元素，范围0-size()-1
    (6 )= 赋值
    (7) ==, != 两个vector相等：元素数量相同，对应位置元素值也应一样。否则不想等。
    (8) 范围for的应用
    (9) 范围for的错误应用
    注意：在循环遍历中，for语句中(遍历一个容器等等类似操作)，千万不要改动vector容器的容量，增加/删除都不可以。
    因为在容器中最后一个元素存在遍历结束指针，增加/删除元素会造成混乱。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"
using namespace std;
using std::vector;
int main()
{
   //二、定义和初始化vector对象
   //二-(1)
   vector<string> mystr;
   mystr.push_back("abcdef");
   //二-(2)
   vector<string> mystr2(mystr); //把mystr元素拷贝给了mystr2;
   vector<string> mystr3 = mystr; //把mystr元素拷贝给了mystr2;
   //二-(3)
   vector<string> mystr4 = {"aaa", "bbb", "ccc"};
   //二-(4)
   vector<int> vec(15, -200);
   vector<string> vec_l(5, "hello");
   //二-(5)
   vector<int> vec_s(10); 
   vector<int> vec_b{10}; 
   vector<string> vec_m{10};  
   vector<string> vec_n{10, "hello"}; 
   vector<int> vec_x{10, 1};   
   //三、vector对象上的操作
   //三-(1)
   vector<int> ivec;
   if(ivec.empty()){
      cout<<"ivec为空"<< endl;
   }
   //三-(2)
   ivec.push_back(1);
   ivec.push_back(2);
   //三-(6)
   vector<int> ivec2;
   ivec2 = ivec;  // 得到ivec中的元素， ivec2原来元素消失。
   ivec2 = {1,2,3,4,5};  // {}中值替代ivec2原有值，
   cout<< ivec2.size() << endl;   
   //三-(8)
   vector<int> vecvalue{1,2,3,4,5};
   for(auto &vecitem : vecvalue){
      vecitem *= 2;
      cout<< vecitem<< endl;
   }
   //三-(9)
   for(auto vecitem : vecvalue){
      // vecitem.push_back(88); //错误，导致输出彻底乱套
      cout<< vecitem<< endl;
   }
}
```
</code></pre>
</details>

    
## 1.9 迭代器的应用
#### 一、迭代器简介
    vector
    迭代器是一种遍历容器内元素的数据类型，这种数据类型感觉就像指针，理解为迭代器用来指向容器中的某个元素。
    string, vector 很少用[]，更常用的访问方式就是用迭代器。 通过迭代器可以读取/修改容器中元素。 eg:list, map
#### 二、容器的迭代器类型
    vector<int> iv = {100,200,300};
    vector<int>::iterator iter; //定义迭代器, 这里的iter是个迭代器。
#### 三、迭代器begin()/end()操作，反向迭代器rbegin()/rend()操作
    (1) begin()/end()， rbegin()/rend() 用来返回迭代器类型
    (2) begin()返回一个迭代器类型
        iter = iv.begin(); // 如果容器中有元素，则begin返回的迭代器，指向的是容器中第一个元素。
    (3) end()返回一个迭代器类型
        iter = iv.end(); //end返回的迭代器指向的并不算末端元素。而是末端元素的后面，理解成end()指向的是一个不存在的元素。
    (4) 如果一个容器为空，那么begin()和end()返回的迭代器就相同。
    (5) 迭代器用于for遍历。
    (6) 反向迭代器：反向遍历容器中的元素。
        rbegin():返回一个反向迭代器，指向反向迭代器的第一个元素。
        rend():返回一个反向迭代器，指向反向迭代器的最后元素的下一个位置。
#### 四、迭代器运算符
    (1) *iter: 返回迭代器iter所指向元素的引用。必须保证这个迭代器指向的是有效容器元素。
        //不能指向end(), 因为end()是末端元素的后边，end()指向的是一个不存在的元素。
    (2) ++iter iter++: 让迭代器指向下一个元素。已经指向end()的时候不能在++
    (3) --iter iter--: 让迭代器指向上一个元素。指向开头元素的时候不能在++
    (4) iter1 == iter2, iter1 != iter2。判断两个迭代器是否相等。
        如果两个迭代器指向的是同一个元素，就相等，否则不等。
    (5) 引用结构体中的成员
    容器内数据类型是类时，迭代器访问方式：(*iter).num 或 iter->num   (表明iter是指针。)
#### 五、const_iterator迭代器
        const_iterator迭代器表示迭代器指向的值不能改变，迭代器本身可以改变。
    (1) 只能从容器中读元素，不能通过迭代器改写容器中的元素，感觉起来像常量指针。
    (2) 如果容器是const类型，迭代器必须使用const_iterator迭代器。
    (3) c++11中引入两个新函数cbegin(), cend(), 返回的都是常量迭代器，容器内部值不能修改。
#### 六、迭代器失效
    for循环在迭代器内部应用时，容器不能进行增加/删除操作，但是可以在遍历的时候修改。
#### 七、范例演示
    (1) 用迭代器遍历一下string类型数据
        string str("l love you");
        for(auto iter = str.begin(); iter != str.end(); ++iter){
            *iter = toupper(*iter);
        }
    (2) vector 容器常用操作与内存释放
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstring>
#include <vector>
#include "project3.hpp"
#include "project2.h"
using namespace std;
using std::vector;
struct student{
    int num;
};
struct student_l{
    int number;
    string str_l;
};
int main()
{
    //二、迭代器的类型
    vector<int> iv = {100,200,300};
    vector<int>::iterator iter_v; //定以迭代器。
    //三、迭代器begin()/end()操作，反向迭代器rbegin()/rend()操作
    //三-(4)
    vector<int> iv2;
    vector<int>::iterator iterbegin = iv2.begin();
    vector<int>::iterator iterend = iv2.end();
    if(iterbegin == iterend){
        cout<<"容器iv2为空"<< endl;
    }
    //三-(5)
    vector<int> iv3 = {100,200,300};
    for(vector<int>::iterator iter = iv3.begin(); iter != iv3.end(); iter++){
        cout<< *iter << endl;
    }
    //三-(6)
    for(vector<int>::reverse_iterator riter = iv3.rbegin(); riter != iv3.rend(); riter++){
        cout<< *riter << endl;
    }
    //四、迭代器运算符
    //四-(5)
    cout<<"四"<< endl;
    vector<student> sv;
    student mystu;
    mystu.num = 100;
    sv.push_back(mystu);
    vector<student>::iterator iter_s;
    iter_s = sv.begin();
    cout<< (*iter_s).num << endl;
    cout<< iter_s->num << endl;
    //五、const_iterator迭代器
    cout<<"五"<< endl;
    vector<int> iv4 = {100,200,300};
    vector<int>::const_iterator iter_1;
    for(iter_1 = iv4.begin(); iter_1 != iv4.end(); ++iter_1){
        // *iter_1 = 4;   常量迭代器不能赋值。
        cout<< *iter_1 << endl;
    }
    //六、迭代器失效
    cout<<"六"<< endl;
    vector<int> vecvalue{1,2,3,4,5};
    for(auto vecitem : vecvalue){
        // vecvalue.push_back(888); // 不能在for遍历容器时候增加或在删除容器元素
        cout<< vecitem << endl;
    }
    for(auto beg = vecvalue.begin(), end = vecvalue.end(); beg != end; ++beg){
        // vecvalue.push_back(888);  //不能在for遍历容器时候增加或在删除容器元素
        cout<< *beg << endl;
    }
    //七、范例演示
    //七-(1)
    string str("l love you");
    for(auto iter = str.begin(); iter != str.end(); ++iter){
        *iter = toupper(*iter);
    }
    cout<< str << endl;
    //七-(2)
    student_l *pconf1 = new student_l;
    student_l *pconf2 = new student_l;
    cout<< "pconf1: "<< pconf1 << endl;
    cout<< "pconf2: "<< pconf2 << endl;
    vector<student_l *> conflist;
    conflist.push_back(pconf1);
    conflist.push_back(pconf2);
    std::vector<student_l *>::iterator pos;
    for(pos = conflist.begin(); pos != conflist.end(); ++pos){
        // delete (*pos); //*pos是new出的地址。
        cout<< *pos << endl;  
    }
    conflist.clear();
    return 0;
}
```
</code></pre>
</details>


## 1.10 类型转换 static_cast、const_cast、reinterpret_cast
#### 一、隐式类型转换  系统自动进行
    int m = 3 + 4.5;
#### 二、显式类型转换（强制类型转换）
    int k = 5 % (int)3.2; //OK, C语言风格的强制类型转换
    int k = 5 % int(3.2); //函数风格的强制类型转换
    (1) c++强制类型转换分为4种：
        a) static_cast
        b) dynamic_cast
        c) const_cast
        e) reinterpret_cast
        这四个强制类型转换被称为“命名的强制类型转换”。
        f) 通用形式：
            强制类型转换名<type>(express)
            强制类型转换名是上边的a,b,c,e中任一个。
            type: 转换的目标类型
            express: 要转换的值
    (2) static_cast
        - 静态转换，大家理解成“正常转换”，编译的时候就会进行类型转换的检查。
        - 代码中要保证转换的安全性和正确性，static_cast含义跟c语言中的强制类型转换这种感觉差不多。
        - c风格的强制类型转换，以及 编译器能够进行的隐式类型转换，都可以用static_cast显示完成。
        a) 相关类型转换，比如整型和实型之间
            double f = 100.34f;
            int i = (int)f;   //c风格的
            int i2 = static_cast<int>(f);   //c++风格的类型转换    
        b) 类中子类转成父类类型（继承关系），也能用这个static_cast。
        c) void * 与其他类型指针之间的转换，void *; 无类型指针：可以指向任何指针类型（万能指针）。
        //不可用于：
        d) 一般不能用于指针类型之间的转换比如int *转double *,float *转double *等等。
    (3) dynamic_cast
        主要用于运行时类型识别和检查。主要用于父类型和子类型之间的转换(父类型指针指向子类型对象，
        然后用dynamic_cast把父指针类型转化为子指针类型)。
    (4) const_cast
        去除指针 或者引用的const属性。该转换能够将const性质转换掉。编译时就进行类型转换。
    (5) reinterpret_cast
        编译的时候进行类型转换
        处理无关类型的转换，也就是两个转换类型之间没有什么关系。就等于可以乱转。
        常用于如下两种类型：
        a) 将一个类型（地址）转换成指针，一种类型指针转换成另一种类型指针，按照转换后的内容重新解释内存中的内容。
        b) 可以从一个指针类型转换成一个整型。
    总结：强制类型转换，不建议使用，强制类型转换能够抑制编译器报错。
        如果实在需要使用类型转换，不要使用c语言风格的类型转换了，而用c++风格的类型转换。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstring>
#include <vector>
#include "project3.hpp"
#include "project2.h"
using namespace std;
using std::vector;
class A{};
class B : public A{};
int main()
{
    //二、显式类型转换
    //二-(2)-a
    double f = 100.34f;
    int i1 = (int)f;   //c风格的
    int i2 = static_cast< int>(f);   //c++风格的类型转换    
    //二-(2)-b
    B b;    
    A a = static_cast< A >(b);
    //二-(2)-c
    int i        = 10;
    int *p    = & i;
    void *q = static_cast< void *>(p);
    int *db  = static_cast< int *>(q);
    //二-(4)
    const int ai = 90;
    // int ai2 = const_cast< int>(ai);  // ai不是指针也不是引用不能转换。
    const int *pai = & ai;
    int *pai2 = const_cast< int *>(pai);
    cout<< *pai2 << endl;
    //二-(5)-a
    int x = 10;
    int *pi = & x;
    int *pi2 = reinterpret_cast< int *>(&x);
    char *pc = reinterpret_cast< char *>(pi); 
    void * pvoid = reinterpret_cast< void *>(pi);
    int *pi3 = reinterpret_cast< int *>(pvoid);
    return 0;
}
```
</code></pre>
</details>
