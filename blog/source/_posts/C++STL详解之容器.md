---
title: C++ STL详解之容器
date: 2016-9-22 9:32:59 AM
categories:
- 术业专攻
tags: 
- C++
---
本篇博客主要介绍C++ 中STL的基础概念以及容器组件，内容来自网上，经过整合而成。
<!-- more -->
## 概念
STL（Standard Template Library），即标准模板库，是一个具有工业强度的，高效的C++ 程序库。它被容纳于C++ 标准程序库（C++ Standard Library）中，是ANSI/ISO C++ 标准中最新的也是极具革命性的一部分。该库包含了诸多在计算机科学领域里所常用的基本数据结构和基本算法。为广大C++程序员们提供了一个可扩展的应用框架，高度体现了软件的可复用性。

从逻辑层次来看，在STL中体现了泛型化程序设计的思想（generic programming），引入了诸多新的名词，比如像需求（requirements），概念（concept），模型（model），容器（container），算法（algorithmn），迭代子（iterator）等。与OOP（object-oriented programming）中的多态（polymorphism）一样，泛型也是一种软件的复用技术；

从实现层次看，整个STL是以一种类型参数化（type parameterized）的方式实现的，这种方式基于一个在早先C++ 标准中没有出现的语言特性--模板（template）。如果查阅任何一个版本的STL源代码，你就会发现，模板作为构成整个STL的基石是一件千真万确的事情。除此之外，还有许多C++的新特性为STL的实现提供了方便。

从广义上讲，STL的代码分为三类：algorithm, container, iterator 几乎所有的代码都采用了模板类和模板函数的方式。在C++标准中，STL被组织为下面的13个头文件： `<algorithm>、<deque>、<functional>、<iterator>， <vector>， <list>、<map>、<memory>、<numeric>、<queue>、<set>、<stack>和<utility>。`


## 六大组件
- 容器（Container）: 是一种数据结构，如 list，vector，和deques ，以模板类的方法提供。为了访问容器中的数据，可以使用由容器类输出的迭代器；
- 迭代器（Iterator）: 提供了访问容器中对象的方法。例如，可以使用一对迭代器指定list或vector中的一定范围的对象。迭代器就如同一个指针。事实上，C++的指针也是一种迭代器。但是，迭代器也可以是那些定义了operator*()以及其他类似于指针的操作符地方法的类对象；
- 算法（Algorithm）:是用来操作容器中的数据的模板函数。例如，STL用sort()来对一个vector中的数据进行排序，用find()来搜索一个list中的对象，函数本身与他们操作的数据的结构和类型无关，因此他们可以在从简单数组到高度复杂容器的任何数据结构上使用；
- 仿函数（Function object，仿函数(functor): 又称之为函数对象（function object），其实就是重载了()操作符的struct，没有什么特别的地方
- 迭代适配器（Adaptor）:
- 空间配制器（allocator）: 其中主要工作包括两部分1.对象的创建与销毁    2.内存的获取与释放

## 容器 container
### 序列式容器
每个元素都有固定的位置（取决于插入的时机和位置，与元素值无关），分为 `vector`，`deque`, `list`。
#### vector
**特点：**
将一个元素置于一个**动态数组**中加以管理，可以**随机存取**元素。在数组尾部添加或删除元素非常快速，但是在中部或头部插入或删除元素比较耗时。

**使用方法**
假设有`vector<int> c;`

|   函数名     |           功能                          |
|:---------------|:------------------------------------|
|push_back  | 在数组的最后添加一个数据,  示例： `c.push_back(5);`
|pop_back   | 去掉数组的最后一个数据,    示例：`c.pop_back();`
|at               | 得到编号位置的数据，  示例： `int d = c.at(0);`
|begin        |   得到数组头的指针,      示例： `vector<int>::iterator it = c.begin();`
|end          |   得到数组的最后一个单元+1的指针,  示例： `vector<int>::iterator it = c.end();`
|front        |得到数组头的引用, 示例： `int first = c.front();`
|back        |    得到数组的最后一个单元的引用,  示例： `int last = c.back();`
|max_size|     得到vector最大可以是多大
|capacity |      当前vector分配的大小
|size       |    当前使用数据的大小
|resize         |改变当前使用数据的大小，如果它比当前使用的大，则填充默认值
|reserve      |改变当前vecotr所分配空间的大小
|erase        | 删除指针指向的数据项
|clear        |  清空当前的vector
|rbegin     |   将vector反转后的开始指针返回(其实就是原来的end-1)
|rend        |  将vector反转后的结束指针返回(其实就是原来的begin-1)
|empty    |    判断vector是否为空
|swap     |    与另一个vector交换数据

**vector 内存管理与效率**
**使用reserve()函数提前设定容量大小，避免多次容量扩充操作导致效率低下。**
关于STL容器，最令人称赞的特性之一就是是只要不超过它们的最大大小，它们就可以自动增长到足以容纳你放进去的数据。(要知道这个最大值，只要调用名叫max_size的成员函数。)
对于vector和string，如果需要更多空间，就以类似realloc的思想来增长大小。vector容器支持随机访问，因此为了提高效率，它内部使用动态数组的方式实现的。在通过 reserve() 来申请特定大小的时候总是按指数边界来增大其内部缓冲区。当进行 insert 或 push_back 等增加元素的操作时，如果此时动态数组的内存不够用，就要 **动态的重新分配当前大小的1.5~2倍的新内存区** ，再把原数组的内容复制过去。所以，在一般情况下，其访问速度同一般数组，只有在重新分配发生时，其性能才会下降。正如上面的代码告诉你的那样。而进行pop_back操作时，capacity并不会因为vector容器里的元素减少而有所下降，还会维持操作之前的大小。对于vector容器来说，**如果有大量的数据需要进行push_back，应当使用reserve()函数提前设定其容量大小，否则会出现许多次容量扩充操作，导致效率低下。**

reserve成员函数允许你最小化必须进行的重新分配的次数，因而可以避免真分配的开销和迭代器/指针/引用失效。但在我解释reserve为什么可以那么做之前，让我简要介绍有时候令人困惑的四个相关成员函数。在标准容器中，只有vector和string提供了所有这些函数。

- `size()` : 告诉你容器中有多少元素。它没有告诉你容器为它容纳的元素分配了多少内存。 

- `capacity()`: 告诉你容器在它已经分配的内存中可以容纳多少元素。那是容器在那块内存中总共可以容纳多少元素，而不是还可以容纳多少元素。如果你想知道一个vector或string中有多少没有被占用的内存，你必须从capacity()中减去size()。如果size和capacity返回同样的值，容器中就没有剩余空间了，而下一次插入（通过insert或push_back等）会引发上面的重新分配步骤。

- `resize(Container::size_type n)`强制把容器改为容纳n个元素。调用resize之后，size将会返回n。如果n小于当前大小，容器尾部的元素会被销毁。如果n大于当前大小，新默认构造的元素会添加到容器尾部。如果n大于当前容量，在元素加入之前会发生重新分配。

- `reserve(Container::size_type n)`: 强制容器把它的容量改为至少n，提供的n不小于当前大小。这一般强迫进行一次重新分配，因为容量需要增加。（如果n小于当前容量，vector忽略它，这个调用什么都不做，string可能把它的容量减少为size()和n中大的数，但string的大小没有改变。在我的经验中，使用reserve来从一个string中修整多余容量一般不如使用“交换技巧”）

**只要有元素需要插入而且容器的容量不足时就会发生重新分配**（包括它们维护的原始内存分配和回收，对象的拷贝和析构和迭代器、指针和引用的失效）。所以，避免重新分配的关键是使用reserve尽快把容器的容量设置为足够大，最好在容器被构造之后立刻进行。

例如，假定你想建立一个容纳1-1000值的vector<int>。没有使用reserve，你可以像这样来做：

```cpp
vector<int> v;
 for (int i = 1; i <= 1000; ++i)
    v.push_back(i);
 ```
 在大多数STL实现中，这段代码在循环过程中将会导致2到10次重新分配。（10这个数没什么奇怪的。记住vector在重新分配发生时一般把容量翻倍，而1000约等于2^10。）
把代码改为使用reserve，我们得到这个：
```cpp
vector<int> v;
 v.reserve(1000);
 for (int i = 1; i <= 1000;++i)
v.push_back(i);
 ```
 这在循环中不会发生重新分配。
在大小和容量之间的关系让我们可以预言什么时候插入将引起vector或string执行重新分配，而且，可以预言什么时候插入会使指向容器中的迭代器、指针和引用失效。例如，给出这段代码，
```cpp
string s;
 ...
 if (s.size() < s.capacity()) {
 s.push_back('x');
 }
 ```
 push_back 的调用不会使指向这个string中的迭代器、指针或引用失效，因为string的容量保证大于它的大小。如果不是执行push_back，代码在string的任意位置进行一个insert，我们仍然可以保证在插入期间没有发生重新分配，但是，与伴随string插入时迭代器失效的一般规则一致，所有从插入位置到string结尾的迭代器/指针/引用将失效。
通常有两情况使用reserve来避免不必要的重新分配。第一个可用的情况是当你确切或者大约知道有多少元素将最后出现在容器中。那样的话，就像上面的vector代码，你只是提前reserve适当数量的空间。第二种情况是保留你可能需要的最大的空间，然后，一旦你添加完全部数据，修整掉任何多余的容量。

**使用“交换技巧”来修整vector过剩空间/内存**
      有一种方法来把它从曾经最大的容量减少到它现在需要的容量。这样减少容量的方法常常被称为“收缩到合适（shrink to fit）”。该方法只需一条语句：
      `vector<int>(ivec).swap(ivec);`
 表达式vector<int>(ivec)建立一个临时vector，它是ivec的一份拷贝：vector的拷贝构造函数做了这个工作。但是，vector的拷贝构造函数只分配拷贝的元素需要的内存，所以这个临时vector没有多余的容量。然后我们让临时vector和ivec交换数据，这时我们完成了，ivec只有临时变量的修整过的容量，而这个临时变量则持有了曾经在ivec中的没用到的过剩容量。在这里（这个语句结尾），临时vector被销毁，因此释放了以前ivec使用的内存，收缩到合适。

**用swap方法强行释放STL Vector所占内存**
```cpp
template < class T> void ClearVector( vector<T>& v )
 { 
     vector<T>vtTemp;
     vtTemp.swap( v );
 } 
 如 
     vector<int> v ;
     nums.push_back(1);
     nums.push_back(3);
     nums.push_back(2);
     nums.push_back(4);
     vector<int>().swap(v);
/* 或者v.swap(vector<int>()); */
/*或者{ std::vector<int> tmp = v;   v.swap(tmp);   }; //加大括号{ }是让tmp退出{ }时自动析构*/
```
**其他相关函数**
```cpp
c.assign(beg,end)   //将[beg; end)区间中的数据赋值给c。
c.assign(n,elem)    //将n个elem的拷贝赋值给c。 
c.at(idx)   //传回索引idx所指的数据，如果idx越界，抛出out_of_range。 
c.back()    //传回最后一个数据，不检查这个数据是否存在。
c.front()   //传回第一个数据。 
get_allocator   //使用构造函数返回一个拷贝。 
c.rbegin()  //传回一个逆向队列的第一个数据。 
c.rend()    //传回一个逆向队列的最后一个数据的下一个位置。 
c.~ vector <Elem>() //销毁所有数据，释放内存。    
```
**使用示例：**
```cpp
#include <iostream>  
#include <vector>  
using namespace std;  
   
typedef vector<int> INTVECTOR;//自定义类型INTVECTOR  

//测试vector容器的功能     
int main()  
{  
    //vec1对象初始为空  
    INTVECTOR vec1;    
    //vec2对象最初有10个值为6的元素    
    INTVECTOR vec2(10,6);   
    //vec3对象最初有3个值为6的元素，拷贝构造  
    INTVECTOR vec3(vec2.begin(),vec2.begin()+3);   
    //声明一个名为i的双向迭代器  
    INTVECTOR::iterator i;  
    
    //从前向后显示vec1中的数据  
    cout<<"vec1.begin()--vec1.end():"<<endl;  
    for (i =vec1.begin(); i !=vec1.end(); ++i)  
        cout << *i << " ";  
    cout << endl;  
    
    //从前向后显示vec2中的数据  
    cout<<"vec2.begin()--vec2.end():"<<endl;  
    for (i =vec2.begin(); i !=vec2.end(); ++i)  
       cout << *i << " ";  
    cout << endl;  

    //从前向后显示vec3中的数据  
    cout<<"vec3.begin()--vec3.end():"<<endl;  
    for (i =vec3.begin(); i !=vec3.end(); ++i)  
        cout << *i << " ";  
    cout << endl;  

    //测试添加和插入成员函数，vector不支持从前插入  
    vec1.push_back(2);//从后面添加一个成员  
    vec1.push_back(4);  
    vec1.insert(vec1.begin()+1,5);//在vec1第一个的位置上插入成员5  

    //从vec1第一的位置开始插入vec3的所有成员  
    vec1.insert(vec1.begin()+1,vec3.begin(),vec3.end());  
    cout<<"after push() and insert() now the vec1 is:" <<endl;  
    for (i =vec1.begin(); i !=vec1.end(); ++i)  
        cout << *i << " ";  
    cout << endl;  

    //测试赋值成员函数  
    vec2.assign(8,1);   // 重新给vec2赋值，8个成员的初始值都为1  
    cout<<"vec2.assign(8,1):" <<endl;  
    for (i =vec2.begin(); i !=vec2.end(); ++i)  
        cout << *i << " ";  
    cout << endl;  

    //测试引用类函数  
    cout<<"vec1.front()="<<vec1.front()<<endl;//vec1第零个成员  
    cout<<"vec1.back()="<<vec1.back()<<endl;//vec1的最后一个成员  
    cout<<"vec1.at(4)="<<vec1.at(4)<<endl;//vec1的第五个成员  
    cout<<"vec1[4]="<<vec1[4]<<endl;  

    //测试移出和删除  
    vec1.pop_back();//将最后一个成员移出vec1  
    vec1.erase(vec1.begin()+1,vec1.end()-2);//删除成员  
    cout<<"vec1.pop_back() and vec1.erase():" <<endl;  
    for (i =vec1.begin(); i !=vec1.end(); ++i)  
        cout << *i << " ";  
    cout << endl;  
    //显示序列的状态信息  
    cout<<"vec1.size(): "<<vec1.size()<<endl;//打印成员个数  
    cout<<"vec1.empty(): "<<vec1.empty()<<endl;//清空  
}

/**运行结果
vec1.begin()--vec1.end():

vec2.begin()--vec2.end():
6 6 6 6 6 6 6 6 6 6 
vec3.begin()--vec3.end():
6 6 6 
after push() and insert() now the vec1 is:
2 6 6 6 5 4 
vec2.assign(8,1):
1 1 1 1 1 1 1 1 
vec1.front()=2
vec1.back()=4
vec1.at(4)=5
vec1[4]=5
vec1.pop_back() and vec1.erase():
2 6 5 
vec1.size(): 3
vec1.empty(): 0

*/
```

#### deque
“double-ended queue” 双端队列，可以**随机存取**。数组尾部或头部添加或删除元素非常快速，但在中部插入或删除元素比较费时。

双端队列是一种优化了的、对序列两端元素进行添加和删除操作的基本序列容器。它允许较为快速地随机访问，但它不像vector 把所有的对象保存在一块连续的内存块，而是采用多个连续的存储块，并且在一个映射结构中保存对这些块及其顺序的跟踪。向deque 两端添加或删除元素的开销很小。它不需要重新分配空间，所以向末端增加元素比vector 更有效。 
双向队列和向量很相似，但是它允许在容器头部快速插入和删除（就像在尾部一样）。
实际上，deque 是对vector 和list 优缺点的结合，它是处于两者之间的一种容器。 

**Deque 的特点**： 
- 随机访问方便，即支持 `[ ]` 操作符和`vector.at()` ，但性能没有vector 好； 
- 可以在内部进行插入和删除操作，但性能不及list ； 
- 可以在两端进行push 、pop ； 
- 相对于verctor 占用更多的内存。 

**使用方法：**
- Constructors 创建一个新双向队列 
   语法: 
     ```cpp
      deque();//创建一个空双向队列 
      deque( size_type size );// 创建一个大小为size的双向队列 
      deque( size_type num, const TYPE &val ); //放置num个val的拷贝到队列中 
      deque( const deque &from );// 从from创建一个内容一样的双向队列 
      deque( input_iterator start, input_iterator end );       // start 和 end - 创建一个队列，保存从start到end的元素。 
    ```
- Operators 比较和赋值双向队列 
      //可以使用[]操作符访问双向队列中单个的元素 

- assign() 设置双向队列的值 
   语法: 
   ```cpp
      void assign( input_iterator start, input_iterator end);       //start和end指示的范围为双向队列赋值 
      void assign( Size num, const TYPE &val );//设置成num个val。 
    ```
- at() 返回指定的元素 
  语法: `reference at( size_type pos );` 返回一个引用，指向双向队列中位置pos上的元素 

- back() 返回最后一个元素 
   语法: `reference back(); ` //返回一个引用，指向双向队列中最后一个元素 

- begin() 返回指向第一个元素的迭代器 
   语法: `iterator begin();`//返回一个迭代器，指向双向队列的第一个元素 

- clear() 删除所有元素 

- empty() 返回真如果双向队列为空 

- end() 返回指向尾部的迭代器 

- erase() 删除一个元素 
  语法:
  ```cpp
      iterator erase( iterator pos ); //删除pos位置上的元素 
      iterator erase( iterator start, iterator end ); //删除start和end之间的所有元素 
      //返回指向被删除元素的后一个元素 
    ```
- front() 返回第一个元素的引用

- get_allocator() 返回双向队列的配置器 

- insert() 插入一个元素到双向队列中 
   语法: 
    ```cpp
      iterator insert( iterator pos, size_type num, const TYPE &val ); //pos前插入num个val值 
      void insert( iterator pos, input_iterator start, input_iterator end ); //插入从start到end范围内的元素到pos前面 
    ```
- max_size() 返回双向队列能容纳的最大元素个数 

- pop_back() 删除尾部的元素 

- pop_front() 删除头部的元素 

- push_back() 在尾部加入一个元素 

- push_front() 在头部加入一个元素 

- rbegin() 返回指向尾部的逆向迭代器 

- rend() 返回指向头部的逆向迭代器 

- resize() 改变双向队列的大小 

- size() 返回双向队列中元素的个数 

- swap() 和另一个双向队列交换元素 
  语法： `void swap( deque &target ); ` // 交换target和现双向队列中元素

**使用示例**：
```cpp
//双向队列 deque
#include <deque>
#include <cstdio>
#include <algorithm>
using namespace std;
int main()
{
    deque<int> ideq(20); //Create a deque ideq with 20 elements of default value 0
    deque<int>::iterator pos;
    int i;

    //使用assign()赋值  assign在计算机中就是赋值的意思
    for (i = 0; i < 20; ++i)
        ideq[i] = i;
    
    //输出deque
    printf("输出deque中数据:\n");
    for (i = 0; i < 20; ++i)
        printf("%d ", ideq[i]);
    putchar('\n');

    //在头尾加入新数据
    printf("\n在头尾加入新数据...\n");
    ideq.push_back(100);
    ideq.push_front(i);

    //输出deque
    printf("\n输出deque中数据:\n");
    for (pos = ideq.begin(); pos != ideq.end(); pos++)
        printf("%d ", *pos);
    putchar('\n');

    //查找
    const int FINDNUMBER = 19;
    printf("\n查找%d\n", FINDNUMBER);
    pos = find(ideq.begin(), ideq.end(), FINDNUMBER);
    if (pos != ideq.end())
        printf("find %d success\n", *pos);
    else
        printf("find failed\n");

    //在头尾删除数据
    printf("\n在头尾删除数据...\n");
    ideq.pop_back();
    ideq.pop_front();

    //输出deque
    printf("\n输出deque中数据:\n");
    for (pos = ideq.begin(); pos != ideq.end(); pos++)
        printf("%d ", *pos);
    putchar('\n');
    return 0;
}

/**输出结果：
输出deque中数据:
0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 

在头尾加入新数据...

输出deque中数据:
20 0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 100 

查找19
find 19 success

在头尾删除数据...

输出deque中数据:
0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 

*/
```
#### list
双向链表，不提供随机存取（**按顺序存储**），在任何位置插入或删除动作都非常迅速。只能顺序访问（从前向后或者从后向前）。与前面两种容器类有一个明显的区别就是：它**不支持随机访问**。要访问表中某个下标处的项需要从表头或表尾处（接近该下标的一端）开始循环。而且缺少下标预算符：operator[]。

**用法：**

|   函数名 |   说明  |
|:------------|:---------|
|assign() |给list赋值 
 |back() |返回最后一个元素 
 |begin() | 返回指向第一个元素的迭代器 
 |clear() | 删除所有元素 
 |empty() | 如果list是空的则返回true 
 |end() | 返回末尾的迭代器 
 |erase() | 删除一个元素 
 |front() | 返回第一个元素 
 |get_allocator() | 返回list的配置器 
 |insert() |插入一个元素到list中 
| max_size() |返回list能容纳的最大元素数量 
 |merge() |合并两个list 
| pop_back() |删除最后一个元素 
 |pop_front() |删除第一个元素 
| push_back() |在list的末尾添加一个元素 
| push_front() |在list的头部添加一个元素 
| rbegin() |返回指向第一个元素的逆向迭代器 
| remove() |从list删除元素 
| remove_if() |按指定条件删除元素 
| rend() |指向list末尾的逆向迭代器 
| resize() |改变list的大小 
| reverse() |把list的元素倒转 
| size() |返回list中的元素个数 
| sort() |给list排序 
| splice() |合并两个list,源list的内容部分或全部元素删除，拼插入到目的list 
| swap() |交换两个list 
| unique() |删除list中重复的元素
 
 **用法示例：**
 ```cpp
#include <iostream> 
#include <list> 
#include <numeric> 
#include <algorithm> 
using namespace std; 

//创建一个list容器的实例LISTINT 
typedef list<int> LISTINT; 
//创建一个list容器的实例LISTCHAR 
typedef list<int> LISTCHAR; 

int main() 
{ 
    //用list容器处理整型数据  
    //用LISTINT创建一个名为listOne的list对象 
    LISTINT listOne; 
    //声明i为迭代器 
    LISTINT::iterator i; 
    
    //从前面向listOne容器中添加数据 
    listOne.push_front (2); 
    listOne.push_front (1); 
    
    //从后面向listOne容器中添加数据 
    listOne.push_back (3); 
    listOne.push_back (4); 
    
    //从前向后显示listOne中的数据 
    cout<<"listOne.begin()--- listOne.end():"<<endl; 
    for (i = listOne.begin(); i != listOne.end(); ++i) 
        cout << *i << " "; 
    cout << endl; 
    
    //从后向后显示listOne中的数据 
    LISTINT::reverse_iterator ir; 
    cout<<"listOne.rbegin()---listOne.rend():"<<endl; 
    for (ir =listOne.rbegin(); ir!=listOne.rend();ir++) { 
        cout << *ir << " "; 
    } 
    cout << endl; 
    
    //使用STL的accumulate(累加)算法 
    int result = accumulate(listOne.begin(), listOne.end(),0); 
    cout<<"Sum="<<result<<endl; 
    cout<<"------------------"<<endl; 
    
    //-------------------------- 
    //用list容器处理字符型数据 
    //-------------------------- 
    
    //用LISTCHAR创建一个名为listOne的list对象 
    LISTCHAR listTwo; 
    //声明i为迭代器 
    LISTCHAR::iterator j; 
    
    //从前面向listTwo容器中添加数据 
    listTwo.push_front ('A'); 
    listTwo.push_front ('B'); 
    
    //从后面向listTwo容器中添加数据 
    listTwo.push_back ('x'); 
    listTwo.push_back ('y'); 
    
    //从前向后显示listTwo中的数据 
    cout<<"listTwo.begin()---listTwo.end():"<<endl; 
    for (j = listTwo.begin(); j != listTwo.end(); ++j) 
        cout << char(*j) << " "; 
    cout << endl; 
    
    //使用STL的max_element算法求listTwo中的最大元素并显示 
    j=max_element(listTwo.begin(),listTwo.end()); 
    cout << "The maximum element in listTwo is: "<<char(*j)<<endl; 
} 

/**输出结果：
listOne.begin()--- listOne.end():
1 2 3 4 
listOne.rbegin()---listOne.rend():
4 3 2 1 
Sum=10
------------------
listTwo.begin()---listTwo.end():
B A x y 
The maximum element in listTwo is: y
*/
 ```
### 关联式容器
元素位置取决于特定的排序准则，与插入顺序无关，包含 `set，multiset, map, multimap`
**Sets/Multisets：**
内部的元素依据其值自动排序，Set内的相同数值的元素只能出现一次，Multisets内可包含多个数值相同的元素，内部由二叉树实现（实际上基于红黑树(RB-tree）实现），便于查找；

**Maps/Multimaps：**
Map的元素是成对的键值/实值，内部的元素依据其值自动排序，Map内的相同数值的元素只能出现一次，Multimaps内可包含多个数值相同的元素，内部由二叉树实现（实际上基于红黑树(RB-tree）实现），便于查找；

另外有其他容器hash_map,hash_set,hash_multiset,hash_multimap。

**关联容器和顺序容器的本质区别：** 关联容器是通过键存取和读取元素、顺序容器通过元素在容器中的位置顺序存储和访问元素。因此，关联容器不提供front、push_front、pop_front、back、push_back以及pop_back，此外对于关联容器不能通过容器大小来定义，因为这样的话将无法知道键所对应的值什么。

**pair 类型**
在介绍关联容器之前，必须先介绍一种与它相关的简单的标准库类型--pair类型：
1. pair类型的初始化 （在头文件utility中）
```cpp
pair<T1,T2> p1;           // 创建一个pair对象，两个元素的类型分别是T1,T2类型，采用初值进行初始化
pair<T1,T2> p1(v1,v2);        //创建一个pair对象，两个元素的类型分别是T1,T2类型，采用v1,v2分别进行初始化
make_pair(v1,v2);        // 以v1、v2的值创建一个pair对象，类型分别是v1、v2的类型
p.first                       // 返回p中第一个公有数据成员
p.second                     // 返回p中第二个公有数据成员
```
2. pair使用：
```cpp
#include<utility>
pair<string,int> author("Peter",30);
cout<<author.first<<"\t"<<author.second<<endl;//可以直接访问数据成员

//使用typedef进行简化
typedef pair<string,string> Student;
Student s1,s2("aaa","bbb");
s1.first="ccc";
s1.second="ddd";

//使用make_pair函数生成一个新的pair对象
string first="eee",second="fff";
Student s3=make_pair(first,second);
```


#### map
map就是一个容器，里面装的就是若干个pair。每个pair的第一个元素，称为键（**注意键的类型必须支持小于(<)操作**），第二个元素，称为值。对于普通的map，每个键都对应一个值。这样的看起来，键类似于数组的下标，而值类似于数组的值。map类定义了3种类型，分别为key_type、mapped_type、以及vaule_type。他们分别表示了键的类型、值的类型，以及一个pair类型，pair的第一个元素是键，第二个元素是值。

**map 的使用**
- map 初始化：
    ```cpp
    map<k,v>   m1             // 创建一个名m1的空的map对形象，对其键和值的类型分别为k、v
    map<k,v>  m(m2)        // 创建m2的副本m,m与m2的类型必须相同的键值类型和值类型
    map<k,v>   m(b,e)        // 创建map类型的对象m，存储迭代器b和e标记范围内所有元素的副本，元素的类型必须转换为pair<const k, v>
    ```
  map定义的类型：
    ```cpp
            map<K,V>::key_type      //在map中用做索引的键的类型
            map<K,V>::value_type    //在map中键所关联的值的类型
            map<K,V>::mapped_type  //一个pair类型，它的first元素具有const map<K,V>::key::type 类型，而second元素则为map<K,V>::mapped_type类型
    ```
   map迭代器进行解引用将产生pair类型的对象，如：
   ```cpp
           map<string,int>::iterator map_it = word_count.begin();
           cout<<map_it->first<<""<<map_it->second<<endl;
    ```
- 给map添加成员：
两种方法： 使用键（下标）或者 使用 insert 。
使用下标访问map对象时，首先在map对象查找键值为x的元素如果找到则返回它的值（类型是map<k,v>::mapped_type），否则插入一个新的对象键值为x,值为map<k,v>中v的默认初值。因此，使用下标访问map与使用下标访问数组或vector的行为不同，用**下标访问不存在的元素将导致在map容器中添加一个新的元素，它的键即为该下标值**。这一特性可以使程序简练：
    ```cpp
  map<string,int> word_count;
  word_count["Peter"]=10;           //相当于增加一个键值对
  //创建一个map对象,用来记录每个单词出现的次数,十分简洁。

  //因为如果读取的是一个新单词，则将在word_count中添加以该单词为索引的新元素
  map<string,int> word_count;
  string word;
  while(cin>>word)
  {
    ++word_count[word];
  }
    // 使用Insert对map进行插入：
    m .insert(e)             //e是一个用在m上的value_type类型的值，如果e.first不在m中则插入一个值为.second的新元素，否则该键值在m中已经存在则保持不变，该函数返回一个pair新类型，包含一个指向键值为e.first的元素的map迭代器，以及一个bool类型的对象，表示是否插入该元素               

    m.insert(beg,end)   //插入beg、end标记范围内的元素，如果该元素的m.first已经存在则不插入否则插入。返回void类型

    m.insert(iter,e)      //如果e.first不在m中，则创建新元素，并以迭代器iter为起点搜索新元素的存储位置，否则返回一个迭代器，指向m中具有的给定键的元素。

  //用insert方法重写单词统计程序
  map<string,int> word_count;
  word_count.insert(map<string,int>::value_type("aaa",1));
  map<string,int> word_count;
  string word;
  while(cin>>word)
  {
     pair<map<string,int>::iterator,bool> ret=word_count.insert(make_pair<string,int>(word,1));
    if(!ret.second)//如果没插入成功，证明原来已经存在键值,将统计值+1
    {
       ++ret.first->second;// first是一个迭代器，指向插入的键
    }
   }
    ```
     
- 查找和读取map中的元素：
  `m.count(k)`：返回k在m中出现的次数，在map中只是返回0、1
  `m.find(k)`：   如果k在m中的键值存在则返回相应的迭代器，否则返回超出来末端迭代器
    ```cpp
   //读取元素而又不插入新元素
    int occurs;
    map<string,int>::iterator it= word_count.find("foobar");//不存在，则返回end迭代器
   if(it!=word_count.end())//可能找不到
   {
    occurs=it.second;
   }
    ```
  
- 从map中删除元素 ，使用erase与顺序容器功能一样：
     `m.erase(k)`： 删除m中键为k的元素。返回值为被删除元素的个数，对于map容器而言，其值必然是0或1。
     `m.erase(p)` ： 从m中删除迭代器p所指向的元素。返回值为void类型。
     `m.erase(b,e)`： 从m中删除一段由一对迭代器范围的元素。返回值为void类型。
     
- map对象的迭代遍历:
    ```cpp
      map<string,int> word_count;
      map<string,int>::const_iterator iter = word_count.begin();
      while(iter!=word_count.end())
       {
         cout<<iter->second<<endl;
         iter++;
      }
    ```
**使用示例：**
```cpp
#include <iostream>
#include <map>
#include <string>
#include <vector>
using namespace std;

int main()
{
    string str;
    map<string,int> wordCount;
    while(cin>>str)
    {
        //对于每个单词，都尝试去插入它
        pair<map<string,int>::iterator,bool> ret = wordCount.insert(make_pair(str,1));
        //通过检测返回值来判断插入是否成功
        if(!ret.second)
            //插入失败表明map中有这个单词，只需要把对应键的值自增即可
            ++ret.first->second;
    }
    

    map<string,int>::iterator it_map = wordCount.begin();
    cout<<"word"<<"\t\t"<<"count"<<endl;
    for(;it_map != wordCount.end();++it_map)
        cout<<it_map->first<<"\t\t"<<it_map->second<<endl;

    //count方法：对于map，返回1或者0
    int cnt = 0;
    cnt = wordCount.count("bird");
    cout<<"cnt"<<cnt<<endl;

    //find方法：返回的是指向键的迭代器
    int occurs = 0;
    map<string,int>::iterator it = wordCount.find("bird");
    if(it != wordCount.end())
        occurs = it->second;
    cout<<"occurs = "<<occurs<<endl;


    //删除元素
    int del;
    string s1 = "hate";
    //使用值删除
    del = wordCount.erase(s1);
    if(del)
        cout<<s1<<" has been removed! "<<endl;
    else
        cout<<"can't find the word! "<<endl;

    //使用迭代器删除
    map<string,int>::iterator iter = wordCount.begin();
    wordCount.erase(iter);

    return 0;
}
/*
输入：
hi
hi
hi
hi
hi
hello
hello
world
world
jack
jack
mali
mali
mary
mary
haha
haha
hi
hell
^Z

输出：
word            count
haha            2
hell            1
hello           2
hi              6
jack            2
mali            2
mary            2
world           2
cnt0
occurs = 0
can't find the word!
*/
```

#### set
   set只有键，没有值，所以他的vaule_type不是pair类型，而是key_type类型。同样的，它也支持insert、find、count操作。map比较适合于储存键值对应的情况，而set适合于储存键，判断键在不在这个集合中，比如黑名单之类的。
   set的作用就是排序。每个元素的值不能直接被改变。 它是一个容器，它用于储存数据并且能从一个数据集合中取出数据。它的每个元素的值必须惟一，而且系统会根据该值来自动将数据排序。每个元素的值不能直接被改变。
   大小可变的集合，支持通过键值实现的快速读取。对于单纯想知道一个值是否存在则set容器最适用 
   在set容器中value_type不是pair类型，而是与key_value的类型相同，它的键值类型也是唯一的且不能修改
   在set也支持find、count、insert、erase操作

在set中添加元素：
    ```cpp
    set<int> set1;
    pair<set<int>::iterator,bool> p=set1.insert(1);//返回pair类型对象，包含一个迭代器和一个布尔值
   set1.insert(2);
   int arr[]={1,2,3};
   set<int> set2;
   set2.insert(arr,arr+3);//返回void类型
    ```
 从set中获取元素:与map方法使用类似，使用find和count函数。

```cpp
#include <iostream>
#include <map>
#include <string>

using namespace std;

int main()
{
    multimap<string,string> authors;
    string author,work,searchItem;

    //建立作者及其作品的容器
    do
    {
        cout<<"enter authors name"<<endl;
        cin>>author;
        if(!cin)
            break;
        cout<<"enter authors works"<<endl;
        while(cin>>work)
            authors.insert(make_pair(author,work));
        cin.clear();
    }while(cin);

    cin.clear();
    //删除元素

    cout<<"who is the author that you want erase:"<<endl;
    cin>>searchItem;
    /*
    //使用erase删除：输出对应键的所有值
    multimap<string,string>::iterator iter = authors.find(searchItem);
    if(iter != authors.end())
        authors.erase(searchItem);
    else
        cout<<"cannot find the author!"<<endl;
    */

    //使用equal_range或得迭代器删除
    typedef multimap<string,string>::iterator itType;
    pair<itType,itType> pos = authors.equal_range(searchItem);
    if(pos.first != pos.second)
        authors.erase(pos.first,pos.second);
    else
        cout<<"can not find this author!"<<endl;


    //输出删除结果
    cout<<"author\t\twork:"<<endl;
    multimap<string,string>::iterator itbegin = authors.begin();
    for(;itbegin != authors.end();++itbegin)
        cout<<itbegin->first<<"\t\t"<<itbegin->second<<endl;

    return 0;
}
```