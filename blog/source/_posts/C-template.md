---
title: C++ 模板
date: 2016-9-18 2:46:43 PM
categories:
- 术业专攻
tags: 
- C++
---
本篇博客主要介绍C++ 模板的相关知识。
<!-- more -->

在C++ 发明阶段，C++ 之父Stroustrup和贝尔实验室的C++ 小组对原先的宏方法进行了修订，对其进行了简化并将它从预处理范围移入了编译器。这种新的代码替换装置被称为模板，而且它变现了完全不同的代码重用方法：模板对源代码重用，而不是通过继承和组合重用对象代码。当用户使用模板时，参数由编译器来替换，这非常像原来的宏方法，却更清晰，更容易使用。

模板使类和函数可在编译时定义所需处理和返回的数据类型，一个模板并非一个实实在在的类或函数，仅仅是一个类和函数的描述。由于**模板可以实现逻辑相同、数据类型不同的程序代码复制**，所以使用模板机制可以减轻编程和维护的工作量和难度。模板一般分为模板函数和类模板。以所处理的数据类型的说明作为参数的类就叫**类模板**，或者模板类，而以所处理的数据类型的说明作为参数的函数，则称为**函数模板**。

## 函数模板
函数模板定义了参数化的非成员函数，这使得程序员能够用不同类型的参数调用相同的函数，由编译器决定调用哪一种类型，并且从模板中生成相应的代码。
定义：
```cpp
Template﹤类型参数表﹥返回类型 函数名 （形参表）{函数体}
```
简单实例，调用函数打印字符串或数字等。

普通函数形式：
```cpp
#include <string>
#include <iostream>
void printstring(const std::string& str) 
{    
    std::cout << str << std::endl;
}
int main()
{    
    std::string str("Hello World");    
    printstring(str);
    return 0;
}
//输出：Hello World
```
模板函数形式：
```cpp
#include <string>
#include <iostream>
using namespace std;
template<typename T> void print(const T& var)
{    
    cout << var << endl;
}
int main()
{    
    string str("Hello World");    
    const int num=1234;
    print(str);
    print(num);
    return 0;
}
//输出：Hello World 
//       1234
```
可以看出使用模板后的函数不仅可以输出字符串形式还可以输出数字形式的内容。

上面两个例子介绍了函数模板的简单使用方法，但只有一个参数，如果需要多个参数，相应的函数模板应采用以下形式定义：
```cpp
Template﹤类型1 变量1，类型2 变量2 ，…﹥返回类型 函数名 （形参表）{函数体}
```
现在，为了看到模板时如何称为函数的，我们假定 `min（）`函数接受各种类型的参数，并找出其中的最小者，如果不采用模板技术，则只能接受一个特定类型的参数，如果希望也能接受其他类型的参数，就需要对每一种类型的参数都定义一个同功能的函数，其实为函数的重载，这里不在讨论，但这将是一件非常让人麻烦的事情。如：

普通定义：
```cpp
#include <iostream.h>
// 定义多态函数，找出三个整数中最小的数
int min0(int ii, int jj, int kk)
{
    int temp;
    if((ii<jj)&&(ii<kk)){temp=ii;}
    else if((jj<ii)&&(jj<kk)){temp=jj;    }
    else{    temp=kk;    }
    return temp;
}
// 定义多态函数，找出三个小数中最小的数
float min1(float ii, float jj, float kk)
{
    float temp;
    if((ii<jj)&&(ii<kk)){temp=ii;}
    else if((jj<ii)&&(jj<kk)){temp=jj;    }
    else{    temp=kk;    }
    return temp;
}

// 定义多态函数，找出三个子符中最小的字符
char min2(char ii, char jj, char kk)
{
    char temp;
    if((ii<jj)&&(ii<kk))    {temp=ii;    }
    else if((jj<ii)&&(jj<kk)){temp=jj;}    
    else{temp=kk;}
    return temp;
}

void main()
{
    int temp1=min0(100,20,30);
    cout<<temp1<<endl;
    float temp2=min1(10.60,10.64,53.21);
    cout<<temp2<<endl;
    char temp3=min2('c','a','C');
    cout<<temp3<<endl;
}
//输出
//20  
//10.6  
//C
```

使用模板：
```cpp
#include <iostream.h>
// 定义函数模板，找出三个值中最小的值，与数据类型无关
template <class T>
T min(T ii, T jj, T kk)
{
    T temp;
    if((ii<jj)&&(ii<kk)){temp=ii;}
    else if((jj<ii)&&(jj<kk)){temp=jj;}
    else{    temp=kk; }
    return temp;
}
// 下面是主函数
void main()
{
    cout<<min(100,20,30)<<endl;
    cout<<min(10.60,10.64,53.21)<<endl;
    cout<<min('c','a','C')<<endl;
}
/输出
//20  
//10.6  
//C
```
输出结果同上，但可以清楚的看到二者之间的工作量大小之差距。
### 函数模板和模板函数
#### 函数模板
函数模板可以用来创建一个通用的函数，以支持多种不同的形参，避免重载函数的函数体重复设计。它的最大特点是把函数使用的数据类型作为参数。
函数模板的声明形式为：
```cpp
template<typename（或class) T>
<返回类型><函数名>(参数表)
{
    函数体
}
```
其中，template是定义模板函数的关键字；template后面的尖括号不能省略；typename（或class)是声明数据类型参数标识符的关键字，用以说明它后面的标识符是数据类型标识符。这样，在以后定义的这个函数中，凡希望根据实参数据类型来确定数据类型的变量，都可以用数据类型参数标识符来说明，从而使这个变量可以适应不同的数据类型。例如：
```cpp
template<typename（或class) T>
T fuc(T x, T y)
{
    T x;
    //……
}
```
函数模板只是声明了一个函数的描述即模板，不是一个可以直接执行的函数，只有根据实际情况用实参的数据类型代替类型参数标识符之后，才能产生真正的函数。

#### 模板函数
 模板函数的生成就是将函数模板的类型形参实例化的过程。
例如：
```cpp
     double d;
     int a;
     fuc(d,a);
```
则系统将用实参d的数据类型double去代替函数模板中的T生成函数：
```cpp
double fuc(double x,int y)
{
    double x;
    //……
}
```
### 函数定制
函数模板功能非常强大，但是有时候可能会陷入困境，加入待比较的函数模板没有提供正确的操作符，则程序不会对此进行编译。为了避免这种错误，可以使用函数模板和同名的非模板函数重载，这就是函数定制。函数模板与同名的非模板函数重载必须遵守以下规定：
- 寻找一个参数完全匹配的函数，如有，则调用它
- 如果失败，寻找一个函数模板，使其实例化，产生一个匹配的模板函数，若有，则调用它
- 如果失败，再试低一级的对函数重载的方法，例如通过类型转换可产生的参数匹配等，若找到匹配的函数，调用它
- 如果失败，则证明这是一个错误的调用

现在用上例的模板函数比较两个字符串，但会出现问题：
```cpp
#include <iostream.h>
// 定义函数模板，找出三个值中最小的值，与数据类型无关
template <class T>
T min(T ii, T jj, T kk)
{
    T temp;
    if((ii<jj)&&(ii<kk)){     temp=ii; }
    else if((jj<ii)&&(jj<kk)){ temp=jj; }
    else{    temp=kk;}
    return temp;
}
void main() 
{
    cout<<min("anderson","Washington","Smith")<<endl;
}
//输出
//Smith
```
输出结果与实际结果不符，原因在于编译器会生成对字符串指针做比较的函数，但比较字符串和比较字符串指针是不一样的，为了解决此问题，我们可以定制函数模板，如：
```cpp
#include <iostream>
#include <string>
using namespace std;
// 定义函数模板，找出三个值中最小的值，与数据类型无关
template <class T>
T min(T ii, T jj, T kk)
{
    T temp;
    if((ii<jj)&&(ii<kk)){        temp=ii;    }
    else if((jj<ii)&&(jj<kk)){        temp=jj;    }
    else    {        temp=kk;    }
    return temp;
}
//非模板函数重载
const char* min(const char* ch1, const char* ch2,const char* ch3)
{
    const char* temp;
    int result1 = strcmp(ch1,ch2);
    int result2 = strcmp(ch1,ch3);
    int result3 = strcmp(ch2,ch1);
    int result4 = strcmp(ch2,ch3);
    if((result1<0)&&(result2<0))    {        temp = ch1;    }
    else if((result3<0)&&(result4<0))    {        temp=ch2;    }
    else    {        temp=ch3;    }
    return temp;
}
void main()
{
    cout<<min(100,20,30)<<endl;
    cout<<min(10.60,10.64,53.21)<<endl;
    cout<<min('c','a','C')<<endl;    
    cout<<min("anderson","Washington","Smith")<<endl;
}
/*输出：
20
10.6
C
Smith
*/
```

### 模板实参推演
当函数模板被调用时，对函数实参类型的检查决定了模板实参的类型和值的这个过程叫做模板实参推演。如`template <class T> void h(T a){}; h(1); h(0.2);`第一个调用因为实参是int型的，所以模板形参T被推演为int型，第二个T的类型则为double。

在使用函数模板时，请注意以下几点：
- 在模板被实例化后，就会生成一个新的实例，这个新生成的实例不存在类型转换。比如有函数模板`template <class T>void H(T a){};int a=2; short b=3;`第一个调用H(a)生成一个int型的实例版本，但是当调用h(b)的时候不会使用上次生成的int实例把short转换为int，而是会另外生成一个新的short型的实例。
- 在模板实参推演的过程中有时类型并不会完全匹配，这时编译器允许以下几种实参到模板形参的转换，这些转换不会生成新的实例。
    - 数组到指针的转换或函数到指针的转换：
      > 比如 `template<class T> void h(T * a){}，int b[3]={1,2,3}；h(b);`这时数组b和类型`T *`不是完全匹配，但允许从数组到指针的转换因此数组b被转换成`int *`，而类型形参T被转换成int，也就是说函数体中的T被替换成int。
      
    - 限制修饰符转换：即把const或volatile限定符加到指针上。
      > 比如`template<class T> void h(const T* a){}，int b=3; h(&b);`虽然实参`&b`与形参`const T*`不完全匹配，但因为允许限制修饰符的转换，结果就把`&b`转换成`const int *`。而类形型参T被转换成int。如果模板形参是非const类型，则无论实参是const类型还是非const类型调用都不会产生新的实例。

    - 到一个基类的转换(该基类根据一个类模板实例化而来)：
    > 比如
    > ```cpp
    > tessmplate<class T1>class A{}; 
    > template<class T1> class B:public A<T1>{}; 
    > template<class T2> void h(A<T2>& m){}
    > ```
    > 在main函数中有 `B<int> n; h(n);`函数调用的子类对象n与函数的形参`A<T2>`不完全匹配，但允许到一个基类的转换。在这里转换的顺序为，首先把子类对象n转换为基类对象`A<int>`，然后再用`A<int>`去匹配函数的形参`A<T2>&`，所以最后T2被转换为int，也就是说函数体中的T将被替换为int。

-  不能在函数调用的参数中指定模板形参的类型，对函数模板的调用应使用实参推演来进行，即只能进行h(2,3)这样的调用，或者`int a, b; h(a,b)`。

```cpp
#include <iostream>
using namespace std;
//带有一个类型形参T的模板函数的定义方法，typeid(变量名).name()为测试变量类型的语句。
template<class T> void h(T a)
{
	cout<<" h()"<<typeid(T).name()<<endl;
}  
 
 //注意语句T c。模板类型形参T可以用来声明变量，作为函数的反回类型，函数形参等凡是类类型能使用的地方。
template<class T>void k(T a,T b)
{
	T c;cout<<" k()"<<typeid(T).name()<<endl;
}

 //定义带有两个类型形参T1，T2的模板函数的方法template<class T> void g(const T* a){T b;cout<<" g()"<<typeid(b).name()<<endl;} 
//template<class T1,class T2=int> void g(){}  //错误，默认模板类型形参不能用于函数模板，只能用于类模板上。
template<class T1,class T2> void f(T1 a, T2 b)
{
	cout<<" f()"<<typeid(T1).name()<<","<<typeid(T2).name()<<endl;
}  

int main()
{ 
  // template<class T>void h(){} //错误，模板的声明或定义只能在全局，命名空间或类范围内进行。即不能在局部范围，函数内进行。

  //函数模板实参推演示例。
  // h(int); //错误，对于函数模板而言不存在h(int,int)这样的调用，不能在函数调用的参数中指定模板形参的类型，对函数模板的调用应使用实参推演来进行，即只能进行h(2,3)这样的调用，或者int a, b; h(a,b)。
  //h函数形式为：template<class T>void h(T a)
  h(2);//输出" h() int"使用函数模板推演，在这里数值2为int型，所以把类型形参T推演为int型。
  h(2.0);//输出" h() double"，因为2.0为double型，所以将函数模板的类型形参推演为double型
  //k函数形式为：template<class T>void k(T a,T b)
  k(2,3);//输出" k() int"
  //k(2,3.0);错误，模板形参T的类型不明确，因为k()函数第一个参数类型为int，第二个为double型，两个形参类型不一致。
  //f函数的形式为：template<class T1,class T2> void f(T1 a, T2 b)
  f(3,4.0);	//输出" f() int,double"，这里不存在模板形参推演错误的问题，因为模板函数有两个类型形参T1和T2。在这里将T1推演为int，将T2推演为double。
  int a=3;double b=4;
  f(a,b); //输出同上，这里用变量名实现推板实参的推演。
  //模板函数推演允许的转换示例，g函数的形式为template<class T> void g(const T* a)
  int a1[2]={1,2};g(a1); //输出" g() int"，数组的地址和形参const T*不完全匹配，所以将a1的地址T &转换为const T*，而a1是int型的，所以最后T推演为int。
  g(&b); //输出" g() double",这里和上面的一样，只是把类型T转换为double型。
  h(&b); //输出" h() double *"这里把模参类型T推演为double *类型。
  return 0;
}
```
### 函数模板的显式实例化
- 隐式实例化：比如有模板函数`template<class T> void h(T a){}`。`h(2)`这时h函数的调用就是隐式实例化，既参数T的类型是隐式确定的。
-  函数模板显示实例化声明：其语法是：`template  函数反回类型 函数名<实例化的类型> (函数形参表);` 注意这是声明语句，要以分号结束。例如：`template  void h<int> (int a);`这样就创建了一个h函数的int 实例。再如有模板函数`template<class T> T h( T a){}`，注意这里h函数的反回类型为T，显示实例化的方法为`template int h<int>(int a);` 把h模板函数实例化为int 型。

- 对于给定的函数模板实例，显示实例化声明在一个文件中只能出现一次。

- 在显示实例化声明所在的文件中，函数模板的定义必须给出，如果定义不可见，就会发生错误。

**注意：** 不能在局部范围类显示实例化模板，实例化模板应放在全局范围内，即不能在main函数等局部范围中实例化模板。因为**模板的声明或定义不能在局部范围或函数内进行**。
显示模板实参：
1. 显示模板实参：适用于函数模板，即在调用函数时显示指定要调用的时参的类型。
2. 格式：显示模板实参的格式为在调用模板函数的时候在函数名后用`<>`尖括号括住要显示表示的类型，比如有模板函数`template<class T> void h(T a, T b){}`。则`h<double>(2, 3.2)`就把模板形参T显示实例化为double类型。
3. 显示模板实参用于同一个模板形参的类型不一致的情况。比如`template<class T> void h(T a, T b){}`，则h(2, 3.2)的调用会出错，因为两个实参类型不一致，第一个为int 型，第二个为double型。而用h<double>(2, 3.2)就是正确的，虽然两个模板形参的类型不一致但这里把模板形参显示实例化为double类型，这样的话就允许进行标准的隐式类型转换，即这里把第一个int 参数转换为double类型的参数。
4. 显示模板实参用法二：用于函数模板的反回类型中。例如有模板函数`template<class T1, class T2, class T3> T1 h(T2 a, T3 b){}`，则语句int a=h(2,3)或h(2,4)就会出现模板形参T1无法推导的情况。而语句int h(2,3)也会出错。用显示模板实参就参轻松解决这个问题，比如`h<int, int, int>(2,3)`即把模板形参T1实例化为int 型，T2和T3也实例化为int 型。

5. 显示模板实参用法三：应用于模板函数的参数中没有出现模板形参的情况。比如`template<class  T>void h(){}`如果在main函数中直接调用h函数如h()就会出现无法推演类型形参T的类型的错误，这时用显示模板实参就不会出现这种错误，调用方法为h<int>()，把h函数的模板形参实例化为int 型，从而避免这种错误。

6. 显示模板实参用法四：用于函数模板的非类型形参。比如`template<class T,int a> void h(T b){}`，而调用h(3)将出错，因为这个调用无法为非类型形参推演出正确的参数。这时正确调用这个函数模板的方法为h<int, 3>(4)，首先把函数模板的类型形参T推演为int 型，然后把函数模板的非类型形参int a用数值3来推演，把变量a设置为3，然后再把4传递给函数的形参b，把b设置为4。注意，因为int a是非类型形参，所以调用非类型形参的实参应是编译时常量表达式，不然就会出错。

7. 在使用显示模板实参时，我们只能省略掉尾部的实参。比如`template<class T1, class T2, class T3> T1 h(T2 a, T3 b){}`在显示实例化时h<int>(3, 3.4)省略了最后两个模板实参T2和T3，T2和T3由调用时的实参3和3.4隐式确定为int 型和double型，而T1被显示确定为int 型。h<int, , double><2,3.4>是错误的，**只能省略尾部的实参**。

8. 显示模板实参最好用在存在二义性或模板实参推演不能进行的情况下。

下面来看看实例：
```cpp
#include <iostream>
using namespace std;
template<class T>void g1(T a, T b){cout<<"hansu g1()"<<typeid(T).name()<<endl;}
template<class T1,class T2,class T3>T1 g2(T2 a,T3 b)
{T1 c=a;cout<<"hansug2()"<<typeid(T1).name()<<typeid(T2).name()<<typeid(T3).name()<<endl; return c;}
template<class T1,class T2> void g3 ( T1 a ) {cout<<"hansu g3()"<<typeid(T1).name()<<typeid(T2).name()<<endl;}
template<class T1,int a> void g4(T1 b, double c){cout<<"hansu g4()"<<typeid(T1).name()<<typeid(a).name()<<endl;}
template<class T1,class T2> class A{public:void g();};
//模板显示实例化示例。
//因为模板的声明或定义不能在局部范围或函数内进行。所以模板实例化都应在全局范围内进行。
template void g1<double>(double a,double b); //把函数模板显示实例化为int型。
template class A<double,double>; //显示实例化类模板，注意后面没有对象名，也没有{}大括号。
//template class A<int,int>{};  //错误，显示实例化类模板后面不能有大括号{}。
//template class A<int,int> m;  //错误，显示实例化类模板后面不能有对象名。
//main函数开始
int main()
{//显示模板实参示例。显示模板实参适合于函数模板
//1、显示模板实参用于同一个模板形参的类型不一致的情况。函数g1形式为template<class T>void g1(T a, T b)
g1<double>(2,3.2);//输出"hansu g1() int"两个实参类型不一致，第一个为int第二个为double。但这里用显示模板实参把类型形参T指定为double，所以第一个int型的实参数值2被转换为double类型。
//g1(2,3.2);错误，这里没有用显式模板实参。所以两个实参类型不一致。
//2、用于函数模板的反回类型中。函数g2形式为template<class T1,class T2,class T3> T1 g2(T2 a,T3 b)
//g2(2,3);错误，无法推演类型形参T1。
//int g2(2,3);错误，不能以这种方法试图推导类型形参T1为int型。
//int a=g2(2,3);错误，以这种方式试图推演出T1的类型为int也是错误的。
g2<int,int,int>(2,3);//正确，将T1，T2，T3 显示指定为int型。输出"hansu g2() intintint"
//3、应用于模板函数的参数中没有出现模板形参的情况其中包括省略的用法，函数g3的形式为template<class T1,class T2> void g3(T1 a)
//g3(2);错误，无法为函数模板的类型形参T2推演出正确的类型
//g3(2,3);错误，岂图以这种方式为T2指定int型是错误的，因为函数只有一个参数。
//g3<,int>(2);错误，这里起图用数值2来推演出T1为int型,而省略掉第一个的显示模板实参，这种方法是错误的。在用显示模板实参时，只能省略掉尾部的实参。
//g3<int>(2);错误，虽然用了显示模板实参方法，省略掉了尾部的实参，但该方法只是把T1指定为int型，仍然无法为T2推演正确的类型。
g3<int,int>(2);//正确，显示指定T1和T2的类型都为int型。
//4、用于函数模板的非类型形参。g4函数的形式为template<class T1,int a> void g4(T1 b,double c)
//g4(3,3.2);错误，虽然指定了两个参数，但是这里仍然无法为函数模板的非类型形参int a推演出正确的实参。因为第二个函数参数x.2是传递给函数的参数double c的，而不是函数模板的非类型形参int a。
//g4(3,2);错误，起图以整型值把实参传递给函数模板的非类型形参是不行的，这里数值2会传递给函数形参double c并把int型转换为double型。所以非类型形参int a仍然无实参。
//int d=1; g4<int ,d >(3,3.2); //错误，调用方法正确，但对于非类型形参要求实参是一个常量表达式，而局部变量c是非常量表达式，不能做为非类型形参的实参，所以错误。
g4<int,1>(2,3.2);//正确，用显示模板实参，把函数模板的类型形参T1设为int型，把数值1传给非类型形参int a，并把a设为1，把数值2 传给函数的第一个形参T1 b并把b设为2，数值?.2传给函数的第二个形参double c并把c设为?.2。
const int d=1; g4<int,d>(2,3.2);//正确，这里变量d是const常量，能作为非类型形参的实参，这里参数的传递方法同上面的语句。
return 0;
}
```
### 显示具体化和函数模板的重载

1. 具体化或特化或模板说明指的是一个意思，就是把模板特殊化，比如有模板`template<class T>void h(T a){}`，这个模板适用于所有类型，但是有些特殊类型不需要与这个模板相同的操作或者定义，比如int 型的h实现的功能和这个模板的功能不一样，这样的话我们就要重定义一个h模板函数的int 版本，即特化版本。

2. 显示特化格式为：`template<>  返回类型函数名<要特化的类型>(参数列表) {函数体}`，显示特化以`template<>`开头，表明要显示特化一个模板，在函数名后<>用尖括号括住要特化的类型版本。比如template <class T> void h(T a){}，其int 类型的特化版本为template<> void h<int>(int a){}，当出现int 类型的调用时就会调用这个特化版本，而不会调用通用的模板，比如h(2)，就会调用int 类型的特化版本。

3. 如果可以从实参中推演出模板的形参，则可以省略掉显示模板实参的部分。比如：template<> void h(int a){}。注意函数h后面没有<>符号，即显示模板实参部分。

4. 对于返回类型为模板形参时，调用该函数的特化版本必须要用显示模板实参调用，如果不这样的话就会出现其中一个形参无法推演的情况。如`template<class T1,class T2,class T3> T1 h(T2 a,T3 b){}`，有几种特化情况：
	- 情况一：`template<> int h<int,int>(int a, in b){}`该情况下把T1，T2，T3的类型推演为int 型。在主函数中的调用方式应为h<int>(2,3)。

	- 情况二：`template<> int h(int a, int b){}`，这里把T2,T2推演为int 型，而T1为int 型，但在调用时必须用显示模板实参调用，且在<>尖括号内必须指定为int 型，不然就会调用到通用函数模板，如h<int>(2,3)就会调用函数模板的特化版本，而h(2,3)调用会出错。h<double>(2,3)调用则会调用到通用的函数模板版本。

这几种情况的特化版本是错误的，如`template<> T1 h(int a,int b){}`，这种情况下T1会成为不能识别的名字，因而出现错误，`template<> int h<double>(int a,int b){}`在这种情况下反回类型为int 型，把T1确定为int 而尖括号内又把T1确定为double型，这样就出现了冲突。

5. 具有相同名字和相同数量反回类型的非模板函数(即普通函数)，也是函数模板特化的一种情况，这种情况将在后面参数匹配问题时讲解。

**函数模板重载（函数定制）：**

1. 函数模板可以重载，注意类模板不存在重载问题，也就是说出现这两条语句时`template<class  T>class A{}; template<classT1,class T2>class A{};将出错。`

2. 模板函数重载的形式为：`template<class T> void h(T a, int b){}`。`Template<class T>void h(T a, double b){}`等。

3. 重载模板函数要注意二义性问题，比如`template<class T> void h(T a, int b){}`和`template<class T>void h(T a, T b){}`这两个版本就存在二义性问题，当出现语句h(2,3)时就不知道调用哪个才正确，在程序中应避免这种情况出现。

4. 重载函数模板的第二个二义性问题是`template<class T>void h(T a, T b){}`与`template<class T1, class T2>void h(T1 a,T2 b){}`，当出现h(2,4)这样的调用时就会出现二义性。解决这个问题的方法是使用显示模板实参，比如要调用第一个h函数，可以使用语法h<int>(2,3)，调用第二个h函数的方法为h<int, int>(2,3)。

5. 函数模板的特化也可以理解为函数模板重载的一种形式。只是特化以template<>开始。

6. 重载的特殊情况：比如template<class T1,class T2> void h(T1 a, T2 b){}，还有个版本如template<class T1>void h(T1 a, int b){}这里两个函数具有两同的名字和相同的形参数量，但形参的类型不同，可以认为第二个版本是第一个版本的重载版本。

7. 函数模板的重载和特化很容易混晓，因为特化很像是一个函数的重载版本，只是开头以template<>开始而已。

**特化类模板：**

1. 特化整个类模板：比如有template<class T1,class T2> class A{};其特化形式为template<> class A<int, int>{};特化形式以template<>开始，这和模板函数的形式相同，在类名A后跟上要特化的类型。

2. 在类特化的外部定义成员的方法：比如template<class T> class A{public: void h();};类A特化为template<>  class A<int>{public: void h();};在类外定义特化的类的成员函数h的方法为：void A<int>::h(){}。在外部定义类特化的成员时应省略掉template<>。

3. 类的特化版本应与类模板版本有相同的成员定义，如果不相同的话那么当类特化的对象访问到类模板的成员时就会出错。因为当调用类的特化版本创建实例时创建的是特化版本的实例，不会创建类模板的实例，特化版本如果和类的模板版本的成员不一样就有可能出现这种错误。比如：模板类A中有成员函数h()和f()，而特化的类A中没有定义成员函数f()，这时如果有一个特化的类的对象访问到模板类中的函数f()时就会出错，因为在特化类的实例中找不到这个成员。

4. 类模板的部分特化：比如有类模板template<class T1, class T2> class A{};则部分特化的格式为template<class T1> class A<T1, int>{};将模板形参T2特化为int 型，T1保持不变。部分特化以template开始，在<>中的模板形参是不用特化的模板形参，在类名A后面跟上要特化的类型。如果要特化第一个模板形参T1，则格式为template<class T2> class A<int, T2>{};部分特化的另一用法是template<class T1> class A<T1,T1>{};将模板形参T2也特化为模板形参T1的类型。

5. 在类部分特化的外面定义类成员的方法：比如有部分特化类template<class T1> class A<T1,int>{public: void h();};则在类外定义的形式为template<class T1> void A<T1,int>::h(){}。注意当在类外面定义类的成员时template 后面的模板形参应与要定义的类的模板形参一样，这里就与部分特化的类A的一样template<class T1>。

**其他说明：**

1. 可以对模板的特化版本只进行声明，而不定义。比如template<> void h<int>(int a);注意，声明时后面有个分号。

2. 在调用模板实例之前必须要先对特化的模板进行声明或定义。一个程序不允许同一模板实参集的同一模板既有显示特化又有实例化。比如有模板template<class T> void h(T a){}在h(2)之前没有声明该模板的int 型特化版本，而是在调用该模板后定义该模板的int 型特化版本，这时程序不会调用该模板的特化版本，而是调用该模板产生一个新的实例。这里就有一个问题，到底是调用由h(2)产生的实例版本呢还是调用程序中的特化版本。

3. 注意：因为模板的声明或定义不能在局部范围或函数内进行。所以特化类模板或函数模板都应在全局范围内进行。

4. 在特化版本中模板的类型形参是不可见的。比如template<> void h<int,int>(int a,int b){T1 a;}就会出现错误，在这里模板的类型形参T1在函数模板的特化版本中是不可见的，所以在这里T1是未知的标识符，是错误的。

## 类模板
### 类模板的定义
与函数模板相同，类模板的声明语句也必须至于类声明的前面。有两个以上模板参数时，应使用逗号分开。使用含类模板的类定义对象时也必须在类名的后面带上“﹤实际类型﹥”的参数列表。类模板最常用于各种类包容关系的设计模型中。
定义：
```cpp
Template ﹤类型参数表﹥ class 类名 {类声明体}
```
 定义一个类模板，一般有两方面的内容：
1. 首先要定义类，其格式为：
```cpp
template <class T>
class foo
{
……
}
```
foo 为类名，在类定义体中，如采用通用数据类型的成员，函数参数的前面需加上T，其中通用类型T可以作为普通成员变量的类型，还可以作为const和static成员变量以及成员函数的参数和返回类型之用。例如：
```cpp
template<class T>
class Test{
private:
    T n;
    const T i;
    static T cnt;
public:
    Test():i(0){}
    Test(T k);
    ~Test(){}
    void print();
    T operator+(T x);
};
```
2. 在类定义体外定义成员函数时，若此成员函数中有模板参数存在，则除了需要和一般类的体外定义成员函数一样的定义外，还需在函数体外进行模板声明

例如
```cpp
template<class T>
void Test<T>::print(){
    std::cout<<"n="<<n<<std::endl;
    std::cout<<"i="<<i<<std::endl;
    std::cout<<"cnt="<<cnt<<std::endl;
}
```
如果函数是以通用类型为返回类型，则要在函数名前的类名后缀上“<T>”。例如：
```cpp
template<class T>
Test<T>::Test(T k):i(k){n=k;cnt++;}
template<class T>
T Test<T>::operator+(T x){
               return n + x;
               }
```

3. 在类定义体外初始化const成员和static成员变量的做法和普通类体外初始化const成员和static成员变量的做法基本上是一样的，唯一的区别是需再对模板进行声明，例如
```cpp
template<class T>
int Test<T>::cnt=0;
template<class T>
Test<T>::Test(T k):i(k){n=k;cnt++;}
```

### 类模板的使用
模板的使用 类模板的使用实际上是将类模板实例化成一个具体的类，它的格式为：`类名<实际的类型>`。
```cpp
#include <iostream>
#include <vector>
#include <cstdlib>
#include <string>
#include <stdexcept>

using namespace std;

template <class T>
class Stack { 
  private: 
    vector<T> elems;     // 元素 

  public: 
    void push(T const&);  // 入栈
    void pop();               // 出栈
    T top() const;            // 返回栈顶元素
    bool empty() const{       // 如果为空则返回真。
        return elems.empty(); 
    } 
}; 

template <class T>
void Stack<T>::push (T const& elem) 
{ 
    // 追加传入元素的副本
    elems.push_back(elem);    
} 

template <class T>
void Stack<T>::pop () 
{ 
    if (elems.empty()) { 
        throw out_of_range("Stack<>::pop(): empty stack"); 
    }
	// 删除最后一个元素
    elems.pop_back();         
} 

template <class T>
T Stack<T>::top () const 
{ 
    if (elems.empty()) { 
        throw out_of_range("Stack<>::top(): empty stack"); 
    }
	// 返回最后一个元素的副本 
    return elems.back();      
} 

int main() 
{ 
    try { 
        Stack<int>         intStack;  // int 类型的栈 
        Stack<string> stringStack;    // string 类型的栈 

        // 操作 int 类型的栈 
        intStack.push(7); 
        cout << intStack.top() <<endl; 

        // 操作 string 类
        stringStack.push("hello"); 
        cout << stringStack.top() << std::endl; 
        stringStack.pop(); 
        stringStack.pop(); 
    } 
    catch (exception const& ex) { 
        cerr << "Exception: " << ex.what() <<endl; 
        return -1;
    } 
}  
```
### 模板类与类模板
模板类是类模板实例化后的一个产物。说个形象点的例子吧。我把类模板比作一个做饼干同的模子，而模板类就是用这个模子做出来的饼干，至于这个饼干是什么味道的就要看你自己在实例化时用的是什么材料了，你可以做巧克力饼干，也可以做豆沙饼干，这些饼干的除了材料不一样外，其他的东西都是一样的了。


**注意：关于`typename 与 class`**
在c++ Template中很多地方都用到了typename与class这两个关键字，而且好像可以替换，是不是这两个关键字完全一样呢?
答：class用于定义类，在模板引入c++后，最初定义模板的方法为：template<class T>，这里class关键字表明T是一个类型，后来为了避免class在这两个地方的使用可能给人带来混淆，所以引入了typename这个关键字，它的作用同class一样表明后面的符号为一个类型，这样在定义模板的时候就可以使用下面的方式了：       template<typename T>.在模板定义语法中关键字class与typename的作用完全一样。

