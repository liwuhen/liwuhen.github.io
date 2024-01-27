---
title: "C++类二篇"
date: 2024-01-26T17:38:13Z
draft: false
author: 李双双
tags:
image: /images/CPlusPlus1.png
description:
toc:
---

## 五 拷贝构造函数
#### 5.1 默认情况下，类对象的拷贝是每个成员变量逐个拷贝。

#### 5.2 拷贝构造函数
	(1) 如果一个类的构造函数，它的第一个参数是所属的类类对象引用，如果还有其他额外参数，这些额外参数都有默认值。
		函数默认参数都必须放在函数声明中，除非该函数没有函数声明。
	(2) 拷贝构造函数：在一定的时机被系统自动调用，时机：进行对象复制的时候 =
	(3) 建议拷贝构造函数第一个参数带const, 且一般不要声明explicit来禁止隐式类型转换。
	(4)（一中，默认情况下，类对象的拷贝是每个成员变量逐个拷贝。）成员变量逐个拷贝功能。
        因为我们定义的拷贝构造函数存在而失去作用。我们定义的拷贝构造函数取代了系统默认的拷贝构造函数。
	(5) 当自定义拷贝构造函数，且类内存在有参构造函数时，不能使用Time myTime01=Time(12,12,12); 初始化类对象。
    
#### 5.3 系统默认的拷贝构造函数
	(1) 如果我们没有定义一个拷贝构造函数，编译器会帮我们定义一个拷贝构造函数
	(2) 编译器给我们定义的叫“合成拷贝构造函数”
		--作用将拷贝构造函数中第一个参数类对象的引用的成员逐个拷贝到正在创建的对象中。
	(3) 默认拷贝构造：每个成员的类型决定自身如何拷贝，比如说成员属性是int类型，那么就是直接的值拷贝。
        如果成员是其他类对象，就会调用类的拷贝构造函数。
	(4) 如果自己定义了拷贝构造函数，就会取代系统的拷贝构造函数，那么就需要在拷贝构造函数中赋值。
        以防止出现未初始化的成员变量。

#### 5.4 还有一些情况会调用拷贝构造函数的调用
	(1) 函数传递参数
		将一个实参传递给一个非引用类型形参，会先调用构造函数构造出对象，然后调用拷贝构造函数。
	(2) 函数返回值是一个局部对象类对象，会进行临时对象的拷贝，调用拷贝构造函数创建一个临时对象返回。

#### 5.5 赋值与初始化的区别
    (1) 赋值与初始化的区别：初始化是指创建这个对象的时刻对它进行一些成员变量的定义、赋值等，
        例如常见的初始化有拷贝构造初始化，默认构造初始化，有参构造初始化；赋值是指对已经初始化好了的对象进行值的修改变动，
        将已经初始化了的一个对象的参数赋给另一个已经初始化了的对象。
        //person是用户自定义类类型
        person p1=p2;//此处为拷贝初始化，即创建一个p1对象，使用创建好的p2对象对它进行拷贝初始化
        person p3;//调用默认构造函数初始化p1对象
        p3=p1;//将p1对象赋值给已经初始化好的p3对象
        person p4(30,"老王");//调用有参构造初始化p4对象
        p4=p1;//将p1的成员数据赋值给p4，即改变p4的年龄与姓名

    (2) 调用的是拷贝构造函数还是赋值运算符，主要是看是否有新的对象实例产生。
        如果产生了新的对象实例，那调用的就是拷贝构造函数；如果没有，那就是对已有的对象赋值，调用的是赋值运算符。

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

//一个临时的小类-创建对象作为Time类的一个成员属性
class TemClass
{
public:
	TemClass()
	{
		cout << "TemClass的构造函数" << endl;
	}

	TemClass(TemClass&tem_class)
	{
		cout << "TemClass的拷贝构造函数" << endl;
	}
};


class Time
{
private:
	int Hour;
	int Minute;
	int Second;
public:
	//TemClass tem_class;
	
public:
	explicit Time(int Hour, int Minute, int Second);
	Time() = default;
	
public:
	//拷贝构造函数
	Time(Time&myTime, int TemHour = 10, int Teminute=10, int TemSecond=10);

};

Time::Time(int TemHour, int Teminute, int TemSecond) :Hour(TemHour), Minute(Teminute), Second(TemSecond)//把形式参数赋值给成员属性
{
}

//拷贝构造函数
Time::Time(Time& mytime, int temhour, int teminute, int temsecond):Hour(12),Minute(12),Second(12)
{
	cout << "这是time类的拷贝构造函数" << endl;
	//this->hour = mytime.hour;	//ok
}

void myFunciton01(Time Te)
{
	;
}

Time myFunction02()
{
	Time t;
	return t;
}
int main(void)
{
	//二、拷贝构造函数
	// Time myTime01 = Time(12,12,12); // 因为和拷贝构造函数冲突，
	/*Time(12,12,12)调用重载的有参构造函数， = Time(12,12,12);调用拷贝构造函数。因为自定义了拷贝构造函数，所以使得默认的拷贝构造函数失效，
	  导致类对象的拷贝无法每个成员变量逐个拷贝。因此出错。
	*/
	Time myTime;  // 调用无参的构造函数
	Time myTime04(12,12,12); //调用有参的构造函数
	Time myTime2 = myTime; //拷贝构造函数--类对象的拷贝， 有隐式转换， 若在拷贝构造函数加上explicit，则出错。
	Time myTime03(myTime);  //拷贝构造函数
	Time myTime04{ myTime };  //拷贝构造函数
	Time myTime05={ myTime };  //拷贝构造函数 //有隐式转换

	Time myTime06 = myTime;	//调用了拷贝构造函数， 有隐式转换 
    // 拷贝构造函数，用在定义时，用于初始化。而拷贝赋值运算符，是用于赋值。

	//四、还有一些情况会调用拷贝构造函数的调用
	//四-(1)
	myFunciton01(myTime06);   // 调用拷贝构造函数
	//四-(2)
	// Time t = myFunction02();
	return 0;
}
```
</details>

## 六 重载运算符、拷贝赋值运算符与析构函数
#### 6.1 new对象的时候调用系统的构造函数。
	(1) 但是我们new出来的对象，必须自己释放，系统不会帮我们释放。
	(2) 在系统停止运行的某个时刻之前必须自己使用delete释放。
	(3) 你什么时候delete系统就会调用析构函数。
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
	Time(Time&TemTime);
	Time(int Hour);
	Time();
	~Time();
};

Time::~Time()
{
	cout << "析构函数" << endl;
}

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}
//拷贝构造函数
Time::Time(Time& TemTime)
{
	this->Hour = TemTime.Hour;
	this->Minute = TemTime.Minute;
	this->Second = TemTime.Second;
}
Time::Time()
{
	;
}

int main(void)
{
	Time myTime01(12, 12, 12);
	Time myTime02 = myTime01;	//初始化--调用拷贝构造函数
	Time myTime03;

	myTime03 = myTime01;	//这个是赋值运算符，系统默认重载

	//使用new创建对象, 返回的是创建对象的指针（地址）
	Time *Ti = new Time;//调用无带参数的构造函数
	Time*Ti2 = new Time();//调用无带参数的构造函数 ----两个区别不研究

	//new出来的对象必须自己手动释放
	delete Ti;	//调用析构函数
	delete Ti2;
	return 0;
}

```
</details>

#### 6.2 拷贝赋值运算符
	(1) 我们可以自己重载赋值运算符，如果没有重载，编译器自动生成的赋值运算符重载比较粗糙，
	一般就是将非static成员属性赋值给运算符左侧的对象的成员中去。
    (2) 拷贝赋值运算符对于成员是类对象时，进行赋值运算符的操作。

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
	Time(Time&TemTime); //自定义拷贝构造函数
	Time(int Hour);
	Time() = default; // 使用编译器默认无参构造函数

public:
	//重载赋值运算符
	Time& operator=(const Time&TemTime);
};

Time::Time(int Hour, int Minute, int Second) :Hour(Hour), Minute(Minute), Second(Second)//把形式参数赋值给成员属性
{
	cout<<"调用explicit Time(int Hour, int Minute, int Second)"<<endl;
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}
//拷贝构造函数
Time::Time(Time& TemTime)
{
	this->Hour = TemTime.Hour;
	this->Minute = TemTime.Minute;
	this->Second = TemTime.Second;
}

//重载赋值运算符
Time& Time::operator=(const Time& TemTime)	//建议写上const，常量属性
{
	this->Hour = TemTime.Hour;
	this->Minute = TemTime.Minute;
	this->Second = TemTime.Second;
	return *this;
}

int main(void)
{
	//一、拷贝赋值运算符
	// Time myTime01 = Time(12,12,12); // 因为和拷贝构造函数冲突，
	/*Time(12,12,12)调用重载的有参构造函数， = Time(12,12,12);调用拷贝构造函数。因为自定义了拷贝构造函数，所以使得默认的拷贝构造函数失效，
	  导致类对象的拷贝无法每个成员变量逐个拷贝。因此出错。
	*/
	Time myTime01;  //调用无参构造函数
	Time myTime04(12,12,12); //调用有参的构造函数
	Time myTime02 = myTime01;	//初始化--调用拷贝构造函数
	Time myTime03;  //调用无参构造函数
	
	myTime03 = myTime01;	//这个是赋值运算符，系统默认重载，没有调用拷贝构造函数
	//系统会调用一个拷贝赋值运算符,我们可以自己进行运算符的重载,重载后调用重载后的重载函数
	//myTime03就是赋值运算符重载返回的*this
	//等价于
    // 但是拷贝构造函数，用在定义时，用于初始化。而拷贝赋值运算符，是用于赋值。
    // 64行没有产生新的对象，因此是赋值，不是拷贝构造

	// 二、重载赋值运算符
	myTime03.operator=(myTime01);
	
	return 0;

}
```
</details>

#### 6.3 什么是运算符重载
在c++中我们都知道有函数重载的概念，在类和对象的设计中还设计到了成员函数重载。同样c++也提供了运算符的重载，所谓重载，就是赋予新的含义。实际上我们在前期的学习过程中已经不知不觉使用了运算符重载，如"<<“和”>>“本来是c++的位移运算符，但是却可以用来输入与输出，这是因为c++系统对”<<“和”>>"运算符进行了重载，用户在不同的场合下使用它们，作用是不同的。

#### 6.4 运算符重载的方法
    (1) 运算符重载的方法是定义一个运算符重载函数，也就是说，运算符重载函数是通过定义一个函数来实现的，运算符重载实质上是函数的重载。
        运算符重载的格式：
            函数类型 operator 运算符名称(形参列表)
    (2) 在上面的格式中，operator是c++的关键字，是专门用于定义重载运算符的函数的，运算符名称就是c++已经有的运算符。注意：函数名是由operator和运算符组成
    (3) 运算符重载有两种方式：一种是类内重载（运算符重载函数作为类的成员函数），另一种是类外重载（运算符重载函数作为类的友元函数）
    (4) 调用的时候为 myTime01.operator==(myTime02) 等价于myTime01==myTime02
    (5) 如果我们不重载运算符，对于简单的运算符，如赋值运算符系统会自动生成一个。但是对复杂的运算符，如==，系统不会生成。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
// 类内重载
#include <iostream>
using namespace std;
class complex
{
public:
	complex() { real = 0; imag = 0; } //定义构造函数
	complex(double r, double i) { real = r; imag = i; }//构造函数重载
	complex operator +(complex& c2) //定义重载运算符+的函数
	{
		complex c;
		c.real = real + c2.real;
		c.imag = imag + c2.imag;
		return c;//返回局部对象
	}
	void display()//定义输出函数
	{
		cout << "(" << real << "," << imag<< "i)"<< endl;
	}
private:
	double real;
	double imag;
};
int main()
{
	complex c1(3, 4), c2(5, -10), c3;
	c3 = c1+c2;//c3=c1.operator(c2)
	c1.display();
	c2.display();
	c3.display();
	return 0;
}
————————————————————————————————
// 类外重载-友元函数
#include <iostream>
using namespace std;
class complex
{
public:
	friend complex operator +(complex& c1, complex& c2); //重载函数作为友元
	complex() { real = 0; imag = 0; }//定义构造函数
	complex(double r,double i) { real = r; imag = i; }//构造函数重载
	void display()//定义输出函数
	{
		cout << "(" << real << "," << imag<< "i)"<< endl;
	}
private:
	double real;
	double imag;
};
//定义运算符+重载函数
complex operator +(complex& c1, complex& c2)
{
	return complex(c1.real + c2.real, c1.imag + c2.imag);
}
int main()
{
	complex c1(3, 4), c2(5, -10), c3;
	c3 = c1+c2;//c3=operator(c1,c2)
	c1.display();
	c2.display();
	c3.display();
	return 0;
}
```
</details>

#### 6.5 析构函数--与构造函数相反，对象销毁的时候就会调用析构函数。
	(1) 如果我们不写自己的析构函数，编译器就会为我们生成一个默认的析构函数。
	(2) 在构造函数里面new一段内存，就要在析构函数函数里面调用delete函数释放内存。
	(3) 析构函数也是类的成员函数，是~类名(){}。没有返回值，不接受任何参数，不能被重载。所以只能有一个析构函数。
        函数重载：函数名字相同，但是函数的参数个数，参数类型，参数顺序不同。
        那么我们调用的时候会根据参数类型，个数，顺序来调用不同的函数。

#### 6.6 构造函数的成员初始化
	(1) 构造函数做的工作可以分为两部分：
		1. 函数体之前（初始化列表的形式）都是用来初始化类的成员属性的。
		2. 函数体中对成员变量的操作是赋值，不是初始化。这种浪费了系统初始化的一次机会，因为系统也会给一个默认值初始化。
	(2) 析构函数也是2部分：
		1. 析构函数先执行函数体{}，进行释放内存等操作。销毁自己new出来的东西。
		2. 函数体之后是系统销毁对象（成员变量），进行系统销毁，不是我们手动销毁。

#### 6.7 成员变量的创建和销毁时机
	(1) 创建对象的时候，先定义的先初始化。每个成员变量是按照成员变量定义的顺序来初始化（先定义的成员变量先有值）
	    销毁的时候，先定义的时候，后销毁。	
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
	Time(Time&TemTime);
	Time(int Hour);
	Time() = default;
public:
	~Time()
	{
		cout << "析构函数执行" << endl;
	}
};

Time::Time(int TemHour, int TemMinute, int TemSecond) :Hour(TemHour), Minute(TemMinute), Second(TemSecond)//把形式参数赋值给成员属性
{
}
Time::Time(int Hour)
{
	this->Hour = Hour;
}
//拷贝构造函数
Time::Time(Time& TemTime)
{
	this->Hour = TemTime.Hour;
	this->Minute = TemTime.Minute;
	this->Second = TemTime.Second;
}
void playObj()
{
	Time myTime01(12, 12, 12);
	Time myTime02 = myTime01;	//初始化--调用拷贝构造函数
	Time myTime03;
	myTime03 = myTime01;	//这个是赋值运算符，系统默认重载
}

int main(void)
{
	playObj();
	return 0;
}
```
</details>

## 七 子类、调用顺序、访问等级与函数遮蔽
#### 7.1 派生类的概念
 	(1) 有父类和子类。构成了层次关系，叫继承。子类可以从父类那里继承很多东西。
 	(2) 继承的概念是面向对象程序设计的核心思想之一。
 
 	实现继承：
 		1. 定义一个父类，定义一些公用的成员变量、成员函数。
 		2. 通过继承父类来构造新的类：子类。写代码时候我们只需要写和子类相关的一些内容即可。
 		3. 子类继承自父类，子类是特殊的父类，可以用子类对象来初始化父类对象。子类一般比父类拥有更多的功能。

#### 7.2 二、继承方式
有public private protected, 继承是 class 子类名字:public/private/protected 父类
  
#### 7.3 派生类调用构造函数与析构函数的顺序
 	(1) 先调用父类的构造函数，初始化父类的成员变量，然后调用子类的构造函数，初始化子类的成员变量。
    (2) 当撤销派生类对象时，则先执行派生类的析构函数，随后再执行基类的析构函数。

#### 7.4 类成员函数和成员属性的3中访问权限 与 3中继承方式
 	public：可以被任意实体访问
 	private：只允许本类的成员函数访问
 	protected：只允许本类或者子类的成员函数访问。
 
 	父类和子类使用public方式继承
 	父类			子类			子类访问类型
 	public		public		public
 	protected	public		protected	//重要
 	private		public		无访问权限
 
 	public		protected	protected
 	protected	protected	protected
 	private		protected	无访问权限
 
 
 	public		private		private
 	protected	private		private
 	private		private		无访问权限
 
 	注意：
		1. 子类public继承不改变父类的访问权限
		2. protected继承将父类中的public成员改为子类的protected成员。
		3. private继承使的父类所有成员在子类中变为private
		4. 父类的private成员不受继承方式的影响，子类永远无法访问
		5. 对于父类来讲，尤其是父类的成员函数，如果你不想让外部访问，就设置为private，
		如果你想让子类访问，就设置为protected，
		如果想公开，就设置为public。
 	
#### 7.5 函数遮蔽
 	(1) 子类对象可以调用父类的public，protected函数，但是当有同名函数时候，子类函数对象就会屏蔽父类的同名函数
 	(2) using Human::myFunction;
 	    方法只能制定函数名，则凡是基类中的public protected的所有同名函数（myFucntion）在子类中都可见。
 	    不能让一部分同名函数在子类中可见，只能全部可见。(例如重载的问题)
 	    using引入的主要目的是用来实现在子类中调用父类的同名函数。
        当子类中的参数个数、类型顺序和父类中的同名函数可以构成函数重载的时候才能区分开。
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

class Human
{
private:
	
public:
	//构造函数
	Human();
	Human(int m_i,string str,int myProtected,int myPrivate);

	//析构函数
	~Human();
	
public:
	int age;	//年龄
	string str;	//名字
	void myPubFunction() { ; }

public:	//父类的同名函数构成函数重载
	void myFunction();
	void myFunction(int tem);
protected:	//只能在子类的成员函数中调用
	int myProtected;	//本类和子类的成员函数可以访问
	void myProFunction() { ; }
private://子类都不能访问
	int myPrivate;
	void myPriFunction() { ; }
};

//构造函数
Human::Human()
{
	cout << "执行了Human默认构造函数" << endl;
}

Human::Human(int age, string str,int myWeath,int myPrivete)
{
	this->age = age;	
	this->str = str;
	this->myProtected = myWeath;
	this->myPrivate = myPrivete;
	cout << "执行了Human带参数的构造函数" << endl;
}

//析构函数
Human::~Human()
{
	cout << "执行了Human析构函数" << endl;
}

void Human::myFunction()
{
	cout << "myFunction" << endl;
}

void Human::myFunction(int tem)
{
	cout << "myFunction" << tem << endl;
}

class Man:public Human	//男人类继承自Human类
{
public:
	Man();
	void myFunction(int tem);
public:
	using Human::myFunction;	//vs2015对这个支持不太好
};

void Man::myFunction(int tem)
{
	Human::myFunction(3);
	Human::myFunction();
	cout << "myFunction man：" << tem << endl;
}

Man::Man()
{
	cout << "执行了man类的默认构造函数" << endl;
}

int main(void)
{
	Man m1;	//先调用父类的构造函数，然后调用子类的构造函数
	m1.age = 12;
	m1.str = "白乾龙";

	m1.myPubFunction();	//子类可以调用父类的public成员函数,

	//如果子类和父类有同名函数，那么子类对象只能调用子类的函数，不能调用父类的重载的函数
	m1.myFunction(123);
	/*如果确实想调用父类的成员函数，1.在子类的成员函数中，可以使用 父类名字：：同名函数名 强制调用父类函数
	 *2.using using namespace
	 *c++11中，让父类同名函数在子类中可见
	 *通过using这个关键字，让父类的同名函数在子类中可见，说白了就是让父类同名函数在子类中以重载的方式实现。
	 *
	 */
	m1.myFunction();	//会调用父类的myFunction函数

	return 0;
}
```
</details>

## 八 父类指针、虚-纯虚函数、多态性与析构函数
    为什么要用子类对象初始化父类指针，有哪些作用与好处？
	由于基类的同名函数派生类会遮蔽的问题，子类要调用父类的同名函数就必须分别定义子类的对象或者对象指针。这样就比较浪费内存空间等问题。
	子类对象初始化父类指针的好处是只定义一个指针，就可以调用父类与子类的同名函数，从而实现多态。

	调用问题：基类与派生类的同名函数，派生类的对象调用同名函数时，会屏蔽基类的同名函数。
	如果想要使用一个对象指针实现调用父类与各子类的同名函数，
    方式：
	    - 通过使用子类对象初始化父类指针的形式。

#### 8.1 基类指针，派生类指针
 	(1) 使用new创建对象都是返回指针
 	(2) 父类指针使用子类对象初始化，但是只能调用父类中的同名函数，不能调用子类中的同名函数。---->如何调用子类同名函数--->新需求--->虚函数-->多态
 	    实际中当函数的参数是父类指针，可以接受所有的子类对象作为实参传递过来，实现动态类型识别来调用父类和子类中的同名函数。
 
#### 8.2 虚函数--实现多态
    多态性使得程序调用的函数是在运行时动态确定的，而不是在编译时静态确定的。
	如何实现父类指针使用子类对象初始化后，调用子类中同名函数。
	做法：父类与子类的同名函数必须为虚函数(即在同名函数前面增加virtual关键字)。
 	(1) 父类的同名函数前面增加virtual.来表示这是一个虚函数，可以实现多态。
        子类的同名函数可以不写virtual，自动为虚函数。
 	(2) 父类函数和子类函数的名字，参数，参数类型，必须完全相同。才会有多态。
 	(3) 重载--在一个类内部函数名字相同，但是参数个数，参数类型，参数顺序不同，调用时候使用参数来区别。
 	(4) 重写：虚函数实现多态，父类中的同名函数在子类中函数名字，参数都不能改变。
 	(5) 重定义：继承中子类重新写了这个函数，改变了参数个数，类型，参数顺序等。
 	(6) 为了避免你在子类中写错虚函数，在c++中，你可以在函数声明中增加一个override的关键字。
        这个关键字用在“子类”中，必须父类是虚函数，才可以加上override，否则会报错。当函数不能覆盖的时候也会报错。
 	(7) override就是用来说明派生类中的虚函数，编译器就会认为你这个eat()函数覆盖了父类中的同名函数，
        编译器就会在父类中找同名同参数的虚函数，如果没找到，就会报错。
 
#### 8.3 final关键字
 	(1) 是与override相对的，也是用在虚函数中，使用在“父类”中，
        如果我们在父类的函数中声明了“final”关键字，那么任何尝试覆盖该函数的操作都将引发错误。
	(2) 动态绑定：动态表示的就是我们的程序运行的时候才知道父类指针指向哪个对象，
        调用对象的对应的函数。运行的时候才知道哪个对象绑定到eat()函数中。

#### 8.4 多态性
 	(1) 多态性是针对虚函数来说的。核心理念就是通过基类访问派生类定义的函数。
 	(2) 体现在具有继承关系的父类和子类之间，子类重写了父类成员函数virtual，
        通过父类的指针，只有到了程序运行时期找到程序动态绑定到
 	(3) 父类指针的函数，然后系统内部查询虚函数表执行对应的函数（父类和子类的虚函数）。
    (4) 多态性使得程序调用的函数是在运行时动态确定的，而不是在编译时静态确定的。
    (5) 使用一个基类类型的指针或者引用，来指向子类对象，进而调用由子类复写的个性化的虚函数。
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

class  Human
{
public:
	int age;
	string name;
public:
	//构造函数
	Human();
	Human(int age, string name);
	//析构函数
	~Human()
	{
		cout << "Human析构函数" << endl;
	}
public:
	void myFunction();
	//virtual void eat() final;
	virtual void eat()//虚函数的实现	运行时动态识别
	{
		cout << "这是Human的eat函数" << endl;
	}
};

class Man:public Human
{
public:
	Man();
public:
	void myFunction();
	void eat() override;
};
void Man::eat()
{
	cout << "man中的eat（）函数执行" << endl;
}

class Woman:public Human
{
public:
	Woman();
public:
	void myFunction();
	void eat() override
	{
		cout << "woman中的eat()函数" << endl;
	}
};

void Human::myFunction()
{
	cout << "Human类中的函数执行" << endl;
}

void Man::myFunction()
{
	cout << "Man类中的myFunction函数执行" << endl;
}

Human::Human()
{
	cout << "Human默认构造函数" << endl;
}

Human::Human(int age, string name)
{
	this->age = age;
	this->name = name;
}

Woman::Woman()
{
	cout << "woman的构造函数" << endl;
}

void Woman::myFunction()
{
	cout << "Woman类中的myFunction函数" << endl;
}

Man::Man()
{
	cout << "Man类的默认构造函数" << endl;
}

int main(void)
{
	//一、基类指针，派生类指针
	Human*p_human = new Human;
	Man *p_man = new Man;   // 这里因为继承会先调用父类的构造函数，初始化父类的成员变量，然后调用子类的构造函数，初始化子类的成员变量。
	
	//先调用父类的构造函数，初始化父类的成员变量，然后调用子类的构造函数，初始化子类的成员变量。
	//子类是特殊的父类，父类指针可以new一个子类对象。
	Human*p_human2 = new Man;	//ok，使用子类对象初始化父类指针
	Human*p_human3 = new Woman;
	
	p_human2->myFunction();	//这里调用父类中的函数
	//但是父类指针实际上是指向子类的对象，如果想调用子类的函数？--->新需求
	//---父类指针指向父类对象还是子类对象就调用对应的函数--->多态
	p_human->eat();	//调用父类eat()
	p_human2->eat();//调用Man类eat()
	p_human3->eat();//调用Woman中的函数eat()
	p_human3->Human::eat();//显式调用父类的eat()函数

	delete p_human;
	delete p_human2;
	delete p_human3;
	/*
	 *都是调用Human的析构函数,若果想调用各个子类的析构函数应该怎么办？
	 *
	*/

	return 0;
}
```
</details>

#### 8.5 纯虚函数 
    在基类中定义的函数返回值  函数名字（函数参数） = 0 子类必须实现所有的纯虚函数
	 (1) 一旦父类有纯虚函数，那么就不能生成父类的实例化对象。主要用于当成基类用来生成子类用的。
	 (2) 因为父类不能实例化对象，所以父类的纯虚函数也就没有办法调用。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include<iostream>
#include<cstdlib>
#include<string>
#include<vector>
using namespace std;

class  Human
{
public:
	int age;
	string name;
public:
	//构造函数
	Human();
	Human(int age, string name);
	//析构函数
	~Human()
	{
		cout << "Human析构函数" << endl;
	}
public:
	virtual  void eat() = 0;
};

class Man :public Human
{
public:
	Man();
	virtual void eat() override;
public:

};

class Woman :public Human
{
public:
	Woman();
	virtual void eat() override;
public:

};

Woman::Woman()
{
	cout << "这是woman的构造函数" << endl;
}

Human::Human()
{
	cout << "Human默认构造函数" << endl;
}

Human::Human(int age, string name)
{
	this->age = age;
	this->name = name;

}

Man::Man()
{
	cout << "Man类的默认构造函数" << endl;
}

void Woman::eat()
{
	cout << "Woman类的eat()执行" << endl;
}
void Man::eat()
{
	cout << "Man类的eat()执行" << endl;
}

void playObject()
{
	Human *p_human01 = new Woman;
	p_human01->eat();//调用Woman的eat()函数

	Human*p_human02 = new Man;
	p_human02->eat();//调用Man的eat()函数
}

int main(void)
{
	playObject();
	return 0;

}

```
</details>

#### 8.6 用基类指针指向子类的对象，delete指针对象时，只调用父类的析构函数，不会执行子类的析构函数
    解决办法：
        把父类中的析构函数写成虚函数。

    总结：
        1. 一般子类都是public继承。在public继承中，基类对派生类机器对象的操作，只能影响到从基类继			
            承下来的成员。如果想要用派生类对基类非继承成员函数进行操作，则要把这个成员函数定义为虚函数。析构函数也是如此。
        2. 基类中析构函数的虚函数特性也会继承给子类，这样子类的析构函数自然也是虚函数。
            虽然子类的析构函数和父类的析构函数名字不同，但是他们都是继承的虚函数。
        3. delete删除一个指向子类对象的父类指针的时候，肯定要调用父类的析构函数，但是要想让系统先调用子类的析构函数，
            父类的析构函数就要声明为虚函数，也就是说，c++为了获得运行时的多态，所调用的成员函数必须是Virtual类型的。
        4. 结论：如果一个类如果想做基类，务必把类的析构函数写成virtual类型，只要基类的析构函数是虚函数，
            就能保证我们delete基类指针的时候调用正确的析构函数版本（先调用子类，再调用父类）

        虚函数会增加内存开销，类里面定义虚函数，编译器就会给这个类增加虚函数表，这个表里面存放虚函数的指针。

    调试信息：
        当我们把析构或者构造函数的写在类内部实现，可能更改为内联函数，不能f11进入函数内部执行。可以在函数体设置一个断点。
        （前面说的没有执行我们自己的写的构造或者析构是错误的）

#### 8.7 抽象类
	抽象类：带有纯虚函数的类叫抽象类。
	(1) 抽象类不能用来生成对象，主要当作父类用来生成子类。
	(2) 子类必须要实现父类中声明的纯虚函数。
	(3) 抽象类中的虚函数不写函数体。
	(4) 抽象类主要是用做父类，把一些公共接口写成纯虚函数。

<details>
<summary><font size="4" color="orange">Show Code</font></summary> 

``` c++
#include<iostream>
#include<cstdlib>
#include<string>
#include<vector>
using namespace std;

class  Human
{
public:
	int age;
	string name;
public:
	//构造函数
	Human();
	Human(int age, string name);
	//析构函数
	virtual ~Human()
	{
		cout << "Human析构函数" << endl;
	}
public:
	virtual  void eat() = 0;
};

class Man :public Human
{
public:
	Man();
	virtual void eat() override;
public:
	~Man()
	{
		cout << "执行了Man的析构函数" << endl;
	}

};

class Woman :public Human
{
public:
	Woman();
	virtual void eat() override;
public:
	~Woman()
	{
		cout << "执行了Woman的析构函数" << endl;
	}

};

Woman::Woman()
{
	cout << "这是woman的构造函数" << endl;
}

Human::Human()
{
	cout << "Human默认构造函数" << endl;
}

Human::Human(int age, string name)
{
	this->age = age;
	this->name = name;

}

Man::Man()
{
	cout << "Man类的默认构造函数" << endl;
}

void Woman::eat()
{
	cout << "Woman类的eat()执行" << endl;
}
void Man::eat()
{
	cout << "Man类的eat()执行" << endl;
}

void playObject()
{
	Human *p_human01 = new Woman;  // 这里因为继承会先调用父类的构造函数，初始化父类的成员变量，然后调用子类的构造函数，初始化子类的成员变量。
	p_human01->eat();//调用Woman的eat()函数

	Human*p_human02 = new Man;
	p_human02->eat();//调用Man的eat()函数

	//执行正确，会执行父类构造和子类构造，子类析构、父类析构。
	Woman *p_woman = new Woman;
	delete p_woman;    // 先调用子类析构函数，再调用父类析构函数。
	
	//都是只执行父类的析构函数，没有执行子类的析构函数
	cout << "-------------------------------------" << endl;
	delete p_human01;
	delete p_human02;
	/*
	执行了Woman的析构函数
	Human析构函数
	执行了Man的析构函数
	Human析构函数
	 */
}

int main(void)
{
	playObject();
	return 0;
}
```
</details>