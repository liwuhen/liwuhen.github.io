---
title: "C++指针"
date: 2024-01-26T17:22:39Z
draft: false
author: 李双双
tags:
image: /images/CPlusPlus1.png
description:
toc:
---

## 一 指针
### 1 单指针
#### 1.1 声明指针
要声明指向特定类型的指针，请使用下面的格式：
``` c++
typrName * pointerName;
```
#### 1.2 指针赋值
应将内存地址赋给指针。可以对变量名应用&运算符，来获得被命名的内存的地址，new运算符返回未命名的内存的地址。
``` c++
double * pn;  // pn can point to a double value
double * pa; //
char * pc;  // pc can point to a char value 
double bubble=3.2;
pn = &bubble;   // assign address of bubble to pn
pc = new char;  // assign address of newly allocated char memory to pc
pa = new double[30]; //assign address of 1st element of array of 30 double to pa
``` 
#### 1.3 指针解引用
对指针解除引用意味着获得指针指向的值。对指针应用解除引用或间接值运算符（*）来解除引用。因此，如果像上面的例子中那样，pn是指向bubble的指针，则*pn是指向的值，即3.2。
``` c++
cout << *pn;  // print  the value of bubble
*pc = 'S';  // place 'S' into the memory location whose address is pc
```
另一种对指针解除引用的方法是使用数组表示法，例如，pn[0]与*pn是一样的。决不要对未被初始化为适当地址的指针解除引用。
#### 1.4 区分指针和指针所指向的值
如果pt是指向int的指针，则*pt不是指向int的指针，而是完全等同于一个int类型的变量。pt才是指针。
``` c++
int * pt = new int;   // assigns an address to the pointer pt
*pt = 5;  // stores the value 5 at that address
```
#### 1.5 数组名
在多数情况下，C++将数组名视为数组的第一个元素的地址。
``` c++
int tacos[10];    // now tacos is the same as &tacos[0]
```
一种例外情况是，将sizeof运算符用于数组名用时，此时将返回整个数组的长度（单位为字节）。
#### 1.6 指针算术
C++允许将指针和整数相加。加1的结果等于原来的地址值加上指向的对象占用的总字节数。还可以将一个指针减去另一个指针，获得两个指针的差。后一种运算将得到一个整数，仅当两个指针指向同一个数组（也可以指向超出结尾的一个位置)时，这种运算才有意义；这将得到两个元素的间隔。
``` c++
int tacos[10] = {5,2,8,4,1,2,2,4,6,8};
int *pt = tacos;   // suppose of and tacos are the address 3000
pt = pt + 1 ;   // now pt is 3004 if a int is 4 bytes
int *pe = & tacos[9];  //pe is 3036 if an int is 4 bytes
pe = pe - 1; // now pe is 3032, the address of tacos[8]
int diff = pe - pt; // diff is 7, the separation between  tacos[8] and tacos[1]
```
#### 1.7 数组的动态联编和静态联编
使用数组声明来创建数组时，将采用静态联编，即数组的长度在编译时设置：
``` c++
int tacos[10];  // static binding, size fixed at compile time
```
使用new[ ]运算符创建数组时，将采用动态联编（动态数组），即将在运行时为数组分配空间，其长度也将在运行时设置。使用完这种数组后，应使用delete [ ]释放其占用的内存：
``` c++
int size;
cin >> size;
int * pz = new int[size];   // dynamic binding, size set at run time
...
delete [] pz;   // free memory when finished
```
#### 1.8 数组表示法和指针表示法
使用方括号数组表示法等同于对指针解除引用：
``` c++
tacos[0]   // means *tacos means the value at address  tacos
tacos[3]  // means  *(tacos +3) means the value at address tacos + 3
```
数组名和指针变量都是如此，因此对于指针和数组名，既可以使用指针表示法，也可以使用数组表示法。
``` c++
int *pt = new int[10];  // pt points to block of 10 ints
*pt  =  5;   // set element number 0 to 5
pt[0] = 6; 
pt[9] = 44;
int tacos[10];
*(tacos  + 4) 12;   // set tacos[4]  to 12; 
```