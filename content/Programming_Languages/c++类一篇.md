---
title: "C++类一篇"
date: 2024-01-26T17:36:51Z
draft: false
author: 李双双
tags:
image: /images/CPlusPlus1.png
description:
toc:
---

## 一 成员函数、对象复制与私有方法
#### 1.1 综述：类是我们自己定义的数据类型（新类型）
    类是我们自己定义的数据类型，新类型
    设计类需要考虑的角度
	(1) 站在设计和实现的角度--stl模板类的设计者
	(2) 站在使用者的角度--哪些是提供给我们的接口
	(3) 继承的角度，父类和子类  公共特性由父类来实现
    
#### 1.2 类基础
    (1) 一个类就是用户自己定义的数据类型，我们可以把类想象成一个命名空间，包着一堆东西（成员函数、成员变量）
	(2)一个类的组成，成员变量（属性）成员函数--由很多特殊的成员函数（构造，析构）和成员属性（this指针）
	(3) 访问类成员的时候，如果是类对象，就是用 对象名.成员名  来访问
		如果使用指向对象的指针来访问，就使用 对象指针->成员属性
	(4) public成员提供类的接口，给外部使用，private成员提供各种实现类功能的细节方法。
		但是不暴露给使用者，外界无法使用这些成员函数，只能在类内部使用
	(5) struct的成员函数和属性默认是public。class定义的结构默认是private的。

#### 1.3 成员函数

#### 1.4 对象的拷贝
    (1) 对象拷贝--本质上是调用拷贝构造函数--类的成员函数
	(2) 默认情况下，这种对象的拷贝，是每个成员属性逐个拷贝
	(3) 在后面如果在Time中定义适当的“赋值运算符”就能实现这种拷贝行为。--->运算符重载
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Student
{
public:
	int number;
	int age;
};

class Time	//定义一个时间类
{
public:
	int Hour;
	int Minute;
	int Second;
private:
	//int MilliSecond;//私有的成员属性，不可以在外部访问
	//成员函数
public:
	Time(){}	//默认构造函数
	Time(int TemHour, int TemMinute, int TemSecond)  // 重载
	{
		Hour      = TemHour;
		Minute  = TemMinute;
		Second = TemSecond;
	}
	// 一个普通的成员属性初始化方法
	void InitTime( int TemHour,  int TemMinute,  int TemSecond)
	{
		Hour      = TemHour;
		Minute  = TemMinute;
		Second = TemSecond;
	}
};

//初始化时间类的函数--c语言风格的写法
void InitTime(Time&TemTime/*输出参数*/, int TemHour, int TemMinute, int TemSecond)
{
	TemTime.Hour      = TemHour;
	TemTime.Minute  = TemMinute;
	TemTime.Second = TemSecond;
}

int main(void)
{
    //二、类基础
    //二-(3)
	//.成员方式访问
	Student stu1={100,12};
	cout << stu1.number << endl;
	//指针方式访问
	Student*pstu1 = new Student;
	pstu1->number = 200;
	pstu1->age = 13;
	
    //三、成员函数
	//三-(1)类对象
	Time myTime;	//调用默认构造函数
	InitTime(myTime, 12, 12, 30);	//12:12:30
	
	Time myTime2(18,35,30);	//这里调用带参数的构造函数
	//c语言的初始化方法 成员属性和成员方法没有具体的关系
	myTime.InitTime(13, 13, 45);

    //四、对象的拷贝
	Time myTime03(myTime);
	Time myTime04 = myTime;
	Time myTime05 = { myTime };
	Time myTime06 { myTime };
	myTime06.Hour = 8;
	//4种方式都可以初始化，也可以理解为对象拷贝。

	return 0;
}
```
</details>

## 二 构造函数详解、explicit与初始化列表
#### 2.1 隐式类型转换和explicit
    (1) 编译系统私下做了很多我们不知道的事情，当参数不够或者类型不匹配时候就会进行隐式类型转换。
	(2) 大括号内部可以当做一个对象·	
		--如果带一个参数的构造函数加explicit进行隐式类型转换，都会错误    
    (3) explicit防止隐式类型转化
    (4)总结：一般可以认为()是参数，{}是一个对象

#### 2.2 是否可以强制系统明确要求构造函数不做强制类型转换？
 	可以。如果构造函数中声明explicit(显式的)。则这个构造函数只能用于初始化显式类型转换。不能进行隐式类型转化。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);

};

Time::Time(int Hour, int Minute, int Second)//把形式参数赋值给成员属性
{
	this->Hour = Hour;
	this->Minute = Minute;
	this->Second = Second;
}
Time::Time(int Hour)
{
	this->Hour = Hour;
    cout<< "this->Hour: "<< this->Hour <<endl;
}

void functionTransform(Time TemTime)
{
	return;
}

int main(void)
{
	Time myTime01 = 12;   // 编译器将12转成了Time类型(存在临时对象隐式转换)。
	// Time myTime03(12, 23, 43, 54);	//错误, 
	Time myTime04 = { 16 };  //存在临时对象隐式转换
	
	Time myTime02=(12, 23, 43, 54);	//会调用单参数的构造函数--相当于逗号表达式，最后是Hour=54
                         
	functionTransform(12);	//首先会调用一个参数的构造，构造一个Time临时对象

	//explicit防止隐式类型转化
	//Time myTime05 = { 12,12,12 };	//错误，不能隐式类型转换
	Time myTime06(2, 12, 34);//正确，调用构造函数
	Time myTime07=(2, 12, 34);//正确，调用构造函数
	/*
	 * 总结：一般可以认为()是参数，{}是一个对象
	 * (1)对于单个参数的构造函数，一般声明为explicit,除非有特殊原因。
	 */
	return 0;
}

```
</details>

#### 2.3 构造函数初始化列表
    (1) 直接在构造函数的实现中，在函数参数列表后面：成员变量（初始化形参变量(初始值)）,成员变量值2（初始化形参变量2），
        这样写的执行时机是执行{}前开始执行，以后会遇到必须使用构造函数成员初始化列表形式的。
    (2) 建议使用构造函数初始化列表形式。初始化列表形式叫初始化，写在{}中的叫赋值操作(在初始化的时候是个垃圾值)。
        写在{}相当于放弃了初始化，由系统给一个垃圾值。
    (3) 每个成员变量的初始化顺序和系统定义顺序有关，而与构造函数初始化列表的写的顺序无关。

#### 2.4 为什么需要构造函数初始化列表？
    (1) 初始化而不是先存入一个垃圾值，然后再赋值。
    (2) 有些场合只能使用初始化列表的方式。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
    private:
    int Hour;
    int Minute;
    int Second;
    public:
    explicit Time(int Hour, int Minute, int Second);
    Time(int Hour);
};

Time::Time(int TemHour, int TemMinute, int TemSecond):Hour(TemHour),Minute(TemMinute),Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
    this->Hour = Hour;
}

int main(void)
{
    Time myTime01 = Time(12, 12, 12);

    return 0;
}
```
</details>

#### 2.5 构造函数
    为什么需要构造函数？
	为了方便类对象的初始化操作。构造函数被系统自动调用。构造函数的目的就是初始化类对象的数据成员。
	在类中有一类特殊的成员函数，函数名字与类名相同，我们在创建类对象的时候，自动调用这个特殊的成员函数，这个成员函数就叫构造函数。
	总结：
        (1) 与类名字相同，并且没有返回值, 连void也没有。
        (2) 不可以手动调用构造函数，否则会出错、
        (3) 构造函数应该声明为public，否则在类的外部不能调用。类内的属性默认是private，所以要显式写上public
        (4) 构造函数中如果有多个参数，那么我们创建对象的时候需要传递参数。
        (5) 写了带参数的构造函数，系统就不会添加默认构造函数。
        (6) Time myTime05 = { 13, 13, 45 };
		首先, 调用构造函数Time(int TemHour, int TemMinute, int TemSecond=30); 函数创建一个临时对象。然后, 调用复制构造函数，把这个临时对象作为参数，构造对象myTime05。
    问题引入：一个类中是否可以存在多个构造函数？
		可以。多个构造函数可以为类对象提供多种初始化方法。但是各个构造函数之间要构成函数重载。
    
#### 2.6 对象拷贝

#### 2.7 函数的默认参数
    在函数的形式参数加上“=值”来显式的制定默认值
	(1) 默认值只能放在函数的声明中，不要放在函数的定义中。除非该函数没有函数声明.
	(2) 在具有多个参数的函数中制定默认值的时候，默认参数必须出现在参数列表的最右侧，
		- 一旦一个参数有默认值，它右侧的参数必须都是具有默认值.
        	- 默认参数都出现在不默认参数的右侧。
	(3) 默认参数遇上函数重载。当三个参数有一个是默认参数遇见只有两个参数的函数，会调用哪一个？
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
public:
	int Hour;	//小时
	int Minute;	//分钟
	int Second;	//秒
public:
	//Time(int TemHour, int TemMinute, int TemSecond);	//构造函数
	Time(int TemHour, int TemMinute, int TemSecond=30);	//构造函数

	Time();	//无参构造函数
};

//构造函数的实现
Time::Time(int TemHour, int TemMinute, int TemSecond)
{
	Hour = TemHour;
	Minute = TemMinute;
	Second = TemSecond;
}

//无参构造函数
Time::Time()	//默认时间是10：10：10
{
	Hour = 10;
	Minute = 10;
	Second = 10;
}

//普通函数带默认构造函数
int functionSum(int a, int b=10)
{
	return a + b;
}

//int functionSum(int a)
//{
//	return a;
//}

int main(void)
{
    //一、构造函数
	//创建类对象
	Time myTime01(12, 34, 23);
	Time myTime02 = Time(12, 13, 45);
	Time myTime03 = Time{ 13, 23, 45 };
	Time myTime04{ 12, 14, 56 };
	Time myTime05 = { 13, 13, 45 };
	
	//Time myTime06();	//vs1015编译通过，但是不是调用无参构造函数，也不是调用有参数构造函数，系统有处理
	Time myTime07;	//下面都是调用无参构造函数，
	Time myTime08 = Time();
	Time myTime09{};
	Time myTime10 = {};

    //二、对象拷贝
	//(2)对象拷贝
	Time myTime20 = {12,12,12};	//调用默认无参构造函数
	Time myTime21 = myTime20;	//都是调用默认拷贝构造函数
	Time myTime22(myTime20);
	Time myTime23{ myTime20 };
	Time myTime24 = { myTime20 };

    //三、函数的默认参数
	//（3）构造函数带默认参数
	Time myTime25(12, 12);
	//(3)普通函数带默认参数
	int Number01 = functionSum(33, 13);	//错误--有多个函数实例与函数调用匹配
	int Number02 = functionSum(10);
	
	return 0;

}

```
</details>

## 三 inline、const、mutable、this与static
#### 3.1 mutable 不稳定，容易改变的<---->const
	(1) mutable的引入就是为了突破const的限制。
	(2) 当成员函数后面有const，是不能修改成员变量的值，如果想在const常成员函数中修改成员变量的值。就是用mutable。
	(3) mutalbe是为了突破const限制而开的一个后门。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	mutable int Hour;	//mutable修饰
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
	Time(){}
public:
	void AddHour(int TemHour)const
	{
		//用mutable修饰一个成员变量，就表示这个成员变量永远处于可以修改状态，及时在const常成员函数中.
		Hour = Hour + TemHour;
	}
};

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}

int main(void)
{
	Time myTime01 = Time(12, 12, 12);
	const Time myTime02;
	myTime02.AddHour(12);
	
	return 0;
}

```
</details>

#### 3.2 静态数据成员
	(1) 类体中的数据成员的声明前加上static关键字，该数据成员就成为了该类的静态数据成员。
	    和其他数据成员一样，静态数据成员也遵守public/protected/private访问规则。
	(2) 静态成员变量属于整个类所有
		- 静态成员变量的生命期不依赖于任何对象，为程序的生命周期
		- 可以通过类名直接访问公有静态成员变量
		- 所有对象共享类的静态成员变量
		- 可以通过对象名访问公有静态成员变量
		- 静态成员变量需要在类外单独分配空间
		- 静态成员变量在程序内部位于全局数据区
	(3) 不定义对象也可以使用类的成员函数与成员变量。
	(4) 常量静态成员可以在类内初始化
		const static int bc=2;//常量静态成员可以在类内初始化
	(5) 如何定义静态成员变量：一般在一个.cpp文件的开头定义静态成员变量。这样可以在任何函数前面进行分配内存。

#### 3.3 静态数据函数
	(1) 静态成员函数是类的一个特殊的成员函数
		- 静态成员函数属于整个类所有，没有this指针
		- 静态成员函数只能直接访问静态成员变量和静态成员函数
		- 可以通过类名直接访问类的公有静态成员函数
		- 可以通过对象名访问类的公有静态成员函数
		- 定义静态成员函数，直接使用static关键字修饰即可

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

static int number02 = 10;	//全局静态变量的作用域是定义的文件。
//限制在本文件中，不能在其他文件中使用extern来声明调用。

//局部静态变量保存上一次调用的时候结果的值，再次进入时候不会执行初始化工作
void myFunction()
{
	static int number1 = 5;//局部静态变量，跳出函数后值保存在静态存储区中
	//第二次调用的时候不执行上面操作，值只被初始化一次，不会再次执行初始化，但是可以修改
	number1 = 10;
}

class Time
{
public:
	int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
public:
	// 静态成员属性和静态成员函数
	static int myCount;	//静态成员函数的声明，没有分配内存，所以在这里不能初始化,在类外面显式初始化
	static void myStaticFunction(int testValue);
};



Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}
//静态成员函数
void Time::myStaticFunction(int testValue)
{
	myCount = testValue;
	//Hour = 12;	//error不是静态成员属性，不能在静态成员函数访问
}

//静态成员属性显式初始化-分配内存
int Time::myCount = 0;
int main(void)
{
	
	Time myTime01 = Time(12, 12, 12);
	Time myTime02(02, 34, 54);

	Time::myCount = 100;
	cout << myTime01.myCount << endl;

	//如何定义静态成员变量：一般在一个.cpp文件的开头定义静态成员变量。这样可以在任何函数前面进行分配内存。

	//静态成员函数
	//使用类调用
	Time::myStaticFunction(14);
	//使用类对象调用
	myTime01.myStaticFunction(45);
	
	return 0;

}

```
</details>

#### 3.4 返回自身对象的引用 *this
	
#### 3.5 this指针
	每一个成员对象都有一个隐含的成员属性this指针，指向自己本身的地址，当进行函数调用的时候，把this指针(自己本身的地址）当做第一个参数传递过去，后面依次是自己写的参数。
	对系统看来，任何对成员属性的访问都是通过this指针来访问的。
	(1) 隐藏的this指针只能在成员函数中，全局函数和静态函数（static）中不存在this指针
	(2) 在普通成员函数中，this是一个指向非const对象的const指针。this只能指向当前对象，指向不能改变。
	(3) 在const成员函数中，this指针就是一个指向const对象的const指针。const Time *const this
	总结：返回对象本身可以实现函数连续调用，多值函数。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	mutable int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
	Time(){}
public:
	//返回对象自身的应用，返回对象自身
	Time&AddHour(int TemHour);
	//Time&AddHour(Time *const this,int TemHour);	//等价于这种，隐藏this指针
	Time&AddMinute(int TemMinute);
};

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}
//返回对象自身的引用
Time& Time::AddHour(int TemHour)
{
	//Hour = (Hour + TemHour) % 24;	//时间是0-24小时
	this->Hour = (this->Hour + TemHour) % 24;
	cout << this->Hour << endl;	//10
	return *this;	//返回对象自身
}

Time& Time::AddMinute(int TemMinute)
{
	this->Minute = (this->Minute + TemMinute) % 60;
	cout << this->Minute << endl;
	return *this;
}

int main(void)
{
	Time myTime01 = Time(12, 12, 12);
	myTime01.AddHour(22);
	//返回对象本身，就可以实现多值函数的调用，类似于cout<<可以实现调用多次，返回的就是ofstream对象本身
	//返回对象本身可以实现函数连续调用，多值函数
	myTime01.AddHour(13).AddMinute(54);
	return 0;
}

```
</details>

#### 3.6 在类定义中实现成员函数inline
	(1) 在类定义中实现成员函数inline，类内的成员函数实现其实也叫类的成员函数定义。
	(2) AddInline()函数在类的定义中实现的成员函数，会被当做inline内联函数来处理。
	注意：能不能内联成功，取决于具体的编译器。

#### 3.7 成员函数尾部的const
	作用：
		(1) 告诉系统，这个成员函数，不会修改该对象里面任何成员变量的值。
		(2) 也就是说，这个成员函数不会修改类Time的任何状态。
		(3) 后面加一个const的成员函数也叫常量成员函数。
	(1) 声明为const的对象不能调用普通的成员函数, 但是这种对象可以调用const常量成员函数
	(2) const不能放在普通函数的末尾

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
	Time(){}
public:
	void AddHour(int TemHour)  //在类定义中实现成员函数inline
	{
		Hour = Hour + TemHour;
	}
	void test(string& str)
	{
		cout << str << endl;
	}
	void AddHour_l(int TemHour) const;  
};

// 常量成员函数--不能修改成员变量的值
void Time::AddHour_l(int TemHour) const
{
	//Hour = Hour + TemHour;	//error不能修改成员变量的属性
}

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}

int main(void)
{
	//一、在类定义中实现成员函数inline
	Time myTime01 = Time(12, 12, 12);

	//二、成员函数尾部的const
	const Time myTime02;	//定义const对象，这种对象一旦初始化就不能修改。 这里是调用无参的默认构造函数。
	myTime02.AddHour_l(12);
	//myTime02.test();	//不兼容的修饰符号，不能调用非const函数。
	return 0;
}
```
</details>

## 四 类内初始化、默认构造函数、"=default;"、"=delete;"
#### 4.1 =default、 =delete
	(1) Time() = default; 编译器能够自动为我们生成函数体，用于特殊的函数
	(2) 只能用于构造函数，拷贝构造函数等特殊函数

#### 4.2 Time() = delete;	//让编译器禁止使用该函数
	(1) delete 关键字可用于任何函数，不仅仅局限于类的成员函数
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
	Time() = default;	//编译器为=default这种函数自动生成函数体,表示使用编译器默认构造函数
	//Time() = delete;	//让编译器禁止使用该函数
};

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}

int main(void)
{
	Time myTime01 = Time(12, 12, 12);
	//默认构造函数
	Time myTime02;	//默认构造函数
	return 0;
}
```
</details>

#### 4.3 类相关非成员函数
	(1) 和类几乎无关的成员函数，访问私有数据成员---友元函数
	(2) 访问公有数据成员，可以直接访问
	
#### 4.4 类内初始化
	在c++11中我们可以为类内成员变量提供一个初始值。创建对象的时候，使用初始值来初始化该成员变量。
	
#### 4.5 常量属性的成员属性初始化
	常量属性的成员属性必须在构造函数初始化列表中初始化，在{}后不能给值。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
public:
	int Hour;
	int Minute={0};
	int Second=0;	//类内初始值
	//常量属性使用构造函数初始化列表方式初始化
	const int TestConst;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
	Time():TestConst(12) {}

};
//友元函数访问类中的私有数据成员。公有数据成员可以直接访问。
void ReadTime(Time&MyTime)
{
	cout << MyTime.Hour << endl;
}

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond),TestConst(12)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour):TestConst(12)
{
	this->Hour = Hour;
}

int main(void)
{
	Time myTime01 = Time(12, 12, 12);

	//一、类相关的非成员函数调用
	ReadTime(myTime01);

	//二、类内初始化
	Time myTime02;	//Minute=Second=0  
	return 0;
}
```
</details>

#### 4.6 默认构造函数--没有任何参数的构造函数
	(1) 如果没有任何构造函数，系统默认给一个默认构造函数，编译器自动定义一个默认构造函数，这个叫“合成的默认构造函数”
	(2) 一旦我们写了构造函数，系统就不会自动创建默认构造函数。

#### 4.7 当进行类的组合时候，会包含其他类的对象，必须使用成员初始化列表和初始值，手工写自己的构造函数。
<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include <iostream>
#include <cstdlib>
#include <string>
#include <vector>
#include "project3.hpp"
#include "project2.h"

using namespace std;

class Time
{
private:
	int Hour;
	int Minute;
	int Second;
public:
	explicit Time(int Hour, int Minute, int Second);
	Time(int Hour);
    Time(){}

};

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}

int main(void)
{
	//Time myTime01 = Time(12, 12, 12);
	Time myTime02;	//调用默认构造函数
	return 0;
}
```
</details>