---
title: C++面试篇之常量
date: 2017-05-08 11:52:15
categories:
	- 术业专攻
tags:
  - C++
  - 面试
---

在C++的面试过程中，关于常量的知识点总是让人混淆。const 引用， const 指针， const 常量乍看起来很简单，但是从碰到的各种面试题来看，指针，引用，函数与const 夹杂在一起，const关键字的位置也会影响表达的含义。本篇博客主要总结一些常见的 const 相关的知识点。
<!-- more -->

## const 常量

- 有`const`修饰的量是常量，常量的值不可修改。

- 常量在定义的时候必须初始化。使用未初始化的常量事错误的。

```cpp
// 用变量给常量赋值
int a = 3;
const int b = a;
// 用常量给变量赋值
int a;
const int b =7;
a = b;
```

## const 指针

- 常量指针不允许通过自身来修改对象的值。

```cpp
const int *p;
int i =9;
p = &i;
int *r =&i;
*r =8; //ok
*p =10; //error， *p为const
int j =0;
p = &j; //p可变
```

- 指针常量，指针指向的地址不能被改变。

```cpp
int i=9;
int * const p= &i;
int j=8;
p = &j; //error
*p =10; //ok
```

- 判定 const 修饰指针还是指针所指的内存技巧：**沿着*号划一条线，const和谁在一边，那么谁就是const，即const限定的元素就是它。**。另外，需要注意：对于const (char *) ; 因为`char *`是一个整体，相当于一个类型(如 char)，因此，这是限定指针是const。

```cpp
const char* p; //*p 为 const
const (char *) p; // p 为const
char * const p; //p 为 const
(char *) const p; //p为const
char const *p; // *p为const
const char * const p; //p ,*p 均为const
char const * const p; //p,*p 均为const
```
## const 引用

- 引用一旦与对象绑定，中途不可改变

- **const 引用，指向一个 const 的引用。不能通过 const 引用改变绑定对象的值。**

```cpp
int i =12;
const int& r =i;
i =13; //ok
r = 1; //error
```

- **非const引用不能与const对象绑定。**因为非const 引用可以修改绑定对象的值。

```cpp
const in a = 9;
int & r = a; //error
```

- 引用的类型必须与所引用的对象类型一致，但是有例外情况：在初始化常量引用时可以用任意的表达式作为初始值，只要表达式的结果能转换成引用的类型。

```cpp
int i =9;
const int& r1 = i; //ok
const int& r2 = 28; //ok
const int& r3 = r1*2; //ok
int &r4 = 45; //error,r4 b不是const引用
double d = 3.2;
const int& r = d; //ok,其实编译器进行了两步，1. const int temp =d; 2. const int &r = temp;
```

## const 函数
函数中使用 const 主要分为两种情况:
1. 修饰传入参数， 传入参数不可变
`int func(const int a)`

2. 修饰返回值，返回值为常量
`const int func(int a)`

## 类相关的const

1. const修饰成员变量 

const修饰类的成员函数，表示成员常量，不能被修改，同时它只能在初始化列表中赋值。 

2. const修饰成员函数 
const修饰类的成员函数，则该成员函数不能修改类中任何非const成员函数。一般写在函数的最后来修饰。

```cpp
class A
{ 
    …
    void function()const; //常成员函数, 它不改变对象的成员变量, 也不能调用类中任何非const成员函数。
}
```
对于const类对象/指针/引用，只能调用类的const成员函数，因此，const修饰成员函数的最重要作用就是限制对于const对象的使用。
  - const成员函数**不被允许修改它所在对象的任何一个数据成员**。 
  - const成员函数**能够访问对象的const成员**，而其他成员函数不可以。

3. const修饰类对象/对象指针/对象引用 
- const修饰**类对象**表示该对象为常量对象，其中的**任何成员都不能被修改**。对于对象指针和对象引用也是一样。 
- const修饰的对象，该对象的**任何非const成员函数都不能被调用**，因为任何非const成员函数会有修改成员变量的企图。 

## const类型转换为非const类型
采用 const_cast 进行转换。 
用法：const_cast (expression) 
该运算符用来修改类型的const或volatile属性。除了const 或volatile修饰之外， type_id和expression的类型是一样的。 
- 常量指针被转化成非常量指针，并且仍然指向原来的对象； 
- 常量引用被转换成非常量引用，并且仍然指向原来的对象； 
- 常量对象被转换成非常量对象。

## const 的使用建议
- 要大胆的使用const，这将给你带来无尽的益处，但前提是你必须搞清楚原委
- 要避免最一般的赋值操作错误，如将const变量赋值，具体可见思考题；
- 在参数中使用const应该使用引用或指针，而不是一般的对象实例，原因同上；
- const在成员函数中的三种用法（参数、返回值、函数）要很好的使用；
- 不要轻易的将函数的返回值类型定为const;
- 除了重载操作符外一般不要将返回值类型定为对某个对象的const引用;
- 任何不会修改数据成员的函数都应该声明为const 类型

## 注意事项
### 类内部的常量限制：
1. 使用这种类内部的初始化语法的时候，常量必须是被一个常量表达式 
初始化的整型或枚举类型，而且必须是static和const形式。

2. 如何初始化类内部的常量：一种方法就是static 和 const 并用，在外部初始化，例如： 
```cpp
class A { 
public: 
    A() {} 
private: 
    static const int i; //注意必须是静态的！ 
}; 

const int A::i=3;  // 初始化
```

另一个很常见的方法就是初始化列表：
```cpp
class A { 
public: 
    A(int i=0):test(i) {}  // 
private: 
    const int i; // const int i =100; error: 不能在类中初始化
};
```

3. 类中const成员与 this 指针类型的关系
- 如果在非const成员函数中，this指针只是一个类类型的
- 如果在const成员函数中，this指针是一个const类类型的
- 如果在volatile成员函数中,this指针就是一个volatile类类型的，new返回的指针必须是const类型的。