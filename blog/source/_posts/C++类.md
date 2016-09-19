---
title: C++类
date: 2016-8-29
categories:
- 术业专攻
tags: 
- C++
---
这篇文章主要是自己对C++中类的一些相关知识点的总结。涉及友员类，友元函数，静态成员，类重载，类复制，运算符重载，多态，抽象类等相关知识点。
<!-- more -->
实践是检验真理的唯一标准，先上代码，代码中所涉及到的知识点在文中均有阐述。
```cpp
#include <iostream>
using namespace std;

/*Box类定义*/
class Box
{
    public:
        //公有成员
        Box(){ cout << "Default constructor called!" << endl; count++; }                     //构造函数
        Box(int h, int w, int l);       //带有参数的构造函数
        Box(const Box &box);    //拷贝构造函数
        double getVolume() const;   //成员函数，获取体积
        friend void printInfo(const Box& box); //友元函数
        ~Box(){ cout << "Destory box...." << endl;}                 //析构函数
        static int count;                       //静态数据成员
        static int getCount(){ return count;}   //静态函数成员
        Box operator++ ();                          //重载前缀递增运算符
        Box operator++ (int);                       //重载后缀递增运算符
        bool operator < (const Box& d);     //重载小于运算符
    private:
        //私有成员
        double length;   //长
        double height;  //高
        double width;  //宽
};

//静态数据成员初始化
int Box::count = 0;

//带参数的构造函数，使用初始化列表来初始化字段
Box::Box(int h, int w, int l):length(l), height(h), width(w)
{
    //使用初始化列表来初始化字段
    count++;
}
//类成员函数的实现
double Box::getVolume() const
{
    return length * height * width;
}

//友元函数的实现, 不属于任何一个类，可以直接访问Box类的私有数据成员
void printInfo(const Box& box)
{
    cout << "BOX INFO:" << endl;
    cout << " BOX Length: " << box.length << endl;
    cout <<" BOX Height: " << box.height << endl;
    cout << "BOX Width: " << box.width << endl;
    cout << endl;
}

//重载递增运算符-----------------前缀递增运算符
Box Box::operator++ ( )
{
    //对象加1
    this->length++;
    this->height++;
    this->width++;

    return Box(length, height, width);
}
//重载递增运算符-----------------后缀递增运算符
Box Box::operator++ (int )
{
    //保存原始值
    Box b(length, height, width);
    //对象加1
    this->length++;
    this->height++;
    this->width++;
    //返回旧值
    return b;
}
//重载小于运算符 <
bool Box::operator < (const Box& b){
    if((getVolume()) < (b.getVolume()))
        return true;
    else
        return false;
}
/*ColorfulBox 类定义，继承自Box, 添加颜色属性*/
class ColorfulBox: public Box
{
    public:
        void setColor(string color){ this->color = color;}
        string getColor(){return this->color;}
    private:
        string color;
};


int main()
{
    //定义对象
    Box box1;
    Box box2(1,2,3);
    Box box3 = box2++;
    Box box4 = ++box2;
    ColorfulBox box5;
    box5.setColor("BLUE");
    cout << "BOX Count: " << Box::getCount() << endl;
    if (box3 < box4)
    {
        cout << "box3 is smaller than box4" << endl;
    }
    printInfo(box1);
    printInfo(box2);
    printInfo(box3);
    printInfo(box4);
    printInfo(box5);
    cout << "box5 color: " << box5.getColor() <<endl;
    return 0;
}

```
输出结果
```
Default constructor called!
Default constructor called!
BOX Count: 5
box3 is smaller than box4
BOX INFO:
 BOX Length: 0		//默认构造函数，没有对数据进行初始化，随机数据
 BOX Height: 2.96439e-323
BOX Width: 3.11253e-317

BOX INFO:
 BOX Length: 5
 BOX Height: 3
BOX Width: 4

BOX INFO:
 BOX Length: 2
 BOX Height: 3
BOX Width: 1

BOX INFO:
 BOX Length: 4
 BOX Height: 5
BOX Width: 3

BOX INFO:					//默认构造函数，没有对数据进行初始化，随机数据
 BOX Length: 6.95251e-310
 BOX Height: 0
BOX Width: 2.07483e-317

box5 color: BLUE
Destory box....
Destory box....
Destory box....
Destory box....
Destory box....
```
## 类与对象
老子在道德经中说道：“ 道生一，一生二，二生三，三生万物。” 这句哲言阐述了抽象和实例化的过程。所谓 ‘ 道 ’就是对万物的抽象，‘ 一，二，三 ，万物 ’ 就是对 ‘ 道 ’ 的实例化。简而言之，类是对象的抽象。对象是类的实例化。


## 类访问修饰符
- public： 类的外部可访问（直接使用 `对象名.成员名`访问）
- private： 类的外部不可访问，只有类和友元函数可以访问私有成员。**默认情况下，类的所有成员都是私有的。** 实际操作中，我们一般会在私有区域定义数据，在公有区域定义相关的函数，以便在类的外部也可以调用这些函数。
- protected：保护成员变量或函数与私有成员十分相似，但有一点不同，保护成员在派生类（即子类）中是可访问的。
<img src="https://github.com/QunxingHu/images/raw/master/c++%E7%B1%BB%E8%AE%BF%E9%97%AE%E6%8E%A7%E5%88%B6%E4%BF%AE%E9%A5%B0%E7%AC%A6.png" width = "800" height = "200" alt="图片名称" align=center />
## 构造函数与析构函数
### 构造函数
类的构造函数是类的一种特殊的成员函数，它会在每次创建类的新对象时执行。
构造函数的名称与类的名称是完全相同的，并且不会返回任何类型，也不会返回 void。构造函数可用于为某些成员变量设置初始值。
- 默认的构造函数 （默认的构造函数没有任何参数）
- 带参数的构造函数
  - 使用初始化列表来初始化字段
  假设有一个类 C，具有多个字段 X、Y、Z 等需要进行初始化，同理地，您可以使用上面的语法，只需要在不同的字段使用逗号进行分隔，如下所示：
	```cpp
	C::C( double a, double b, double c): X(a), Y(b), Z(c)
	{
	  ....
	}
	```
	其效果等价于
	```cpp
    C::C( double a, double b, double c)
    {
        X = a;
        Y = b;
        Z = c;
      ....
    }
    ```
- 拷贝构造函数
拷贝构造函数是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。拷贝构造函数通常用于：
    - 通过使用另一个同类型的对象来初始化新创建的对象。
    - 复制对象把它作为参数传递给函数。
    - 复制对象，并从函数返回这个对象。

	如果在类中没有定义拷贝构造函数，编译器会自行定义一个。如果类带有指针变量，并有动态内存分配，则它必须有一个拷贝构造函数。拷贝构造函数的最常见形式如下：
    ```cpp
    classname (const classname &obj) 
    {
   // 构造函数的主体
	}
    ```
### 析构函数
类的析构函数是类的一种特殊的成员函数，它会在每次删除所创建的对象时执行。
析构函数的名称与类的名称是完全相同的，只是在前面加了个波浪号（~）作为前缀，它不会返回任何值，也不能带有任何参数。析构函数有助于在跳出程序（比如关闭文件、释放内存等）前释放资源。

## 友元函数
- 类的友元函数是定义在类外部，但有权访问类的所有私有（private）成员和保护（protected）成员。
- 尽管友元函数的原型有在类的定义中出现过，但是友元函数并不是成员函数。
- 友元可以是一个函数，该函数被称为友元函数；友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。
- 如果要声明函数为一个类的友元，需要在类定义中该函数原型前使用关键字 friend。

## 内联函数
- C++ 内联函数是通常与类一起使用。如果一个函数是内联的，那么在编译时，编译器会把该函数的代码副本放置在每个调用该函数的地方。
- 对内联函数进行任何修改，都需要重新编译函数的所有客户端，因为编译器需要重新更换一次所有的代码，否则将会继续使用旧的函数。
- 如果想把一个函数定义为内联函数，则需要在函数名前面放置关键字 inline，在调用函数之前需要对函数进行定义。如果已定义的函数多于一行，编译器会忽略 inline 限定符。
- 在类定义中的定义的函数都是内联函数，即使没有使用 inline 说明符。

## this指针
- this 指针是所有成员函数的隐含参数。因此，在成员函数内部，它可以用来指向调用对象。
- 友元函数没有 this 指针，因为友元不是类的成员。只有成员函数才有 this 指针。

## `.` VS `->`
- `.`（点）运算符和 `->`（箭头）运算符用于引用类、结构和共用体的成员。
- 点运算符应用于实际的对象。箭头运算符与一个指向对象的指针一起使用。
- **访问结构的成员时使用点运算符，而通过指针访问结构的成员时，则使用箭头运算符。**

## 静态成员
- 使用`static`关键字将类成员定义为静态的。
- 当我们声明类的成员为静态时，这意味着无论创建多少个类的对象，静态成员都只有一个副本。
- 静态数据成员的初始化
  - 静态成员在类的所有对象中是共享的。如果不存在其他的初始化语句，在创建第一个对象时，所有的静态数据都会被初始化为零。（没有初始化语句，创建对象时默认初始化为0）
  - 我们不能把静态成员放置在类的定义中，但是可以在类的外部通过使用范围解析运算符 :: 来重新声明静态变量从而对它进行初始化。如例子中Box类中的count变量的初始化所示。
- 静态函数成员：
  - 如果把函数成员声明为静态的，就可以把函数与类的任何特定对象独立开来。静态成员函数即使在类对象不存在的情况下也能被调用，静态函数只要使用类名加范围解析运算符 :: 就可以访问。
  - 静态成员函数只能访问静态数据成员，不能访问其他静态成员函数和类外部的其他函数。
  - 静态成员函数有一个类范围，他们不能访问类的 this 指针。您可以使用静态成员函数来判断类的某些对象是否已被创建。
## 继承
- 当创建一个类时，您不需要重新编写新的数据成员和成员函数，只需指定新建的类继承了一个已有的类的成员即可。这个已有的类称为基类，新建的类称为派生类。
- 继承代表了 is a 关系。例如，哺乳动物是动物，狗是哺乳动物，因此，狗是动物，等等。
语法格式：
```cpp
class derived-class: access-specifier base-class
```
### 访问控制
派生类可以访问基类中所有的非私有成员。因此基类成员如果不想被派生类的成员函数访问，则应在基类中声明为 private。

一个派生类继承了所有的基类方法，但下列情况除外：
- 基类的构造函数、析构函数和拷贝构造函数。
- 基类的重载运算符。
- 基类的友元函数。

### 继承类型
通过指定访问修饰符，将继承分为三类（保护继承，私有继承很少使用）
- 公有继承（public）: 基类的公有成员成为派生类的公有成员，基类的保护成员成为派生类的保护成员
- 保护继承（protected）: 基类的公有和保护成员将成为派生类的保护成员。
- 私有继承（private）：基类的公有和保护成员将成为派生类的私有成员。

### 多继承
一个子类可以有多个父类，继承多个父类的特性。
```cpp
//继承方式即为public，protected，private三种之一
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
```
