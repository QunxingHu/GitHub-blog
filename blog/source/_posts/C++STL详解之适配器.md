---
title: C++ STL详解之适配器
date: 2016-9-30 9:51:39 AM
categories:
- 术业专攻
tags: 
- C++
---
摘要部分
<!-- more -->
STL提供了三个容器适配器：`queue` 、`priority_queue`、`stack`。这些适配器都是包装了vector、list、deque中某个顺序容器的包装器。
注意：**适配器没有提供迭代器，也不能同时插入或删除多个元素**。

## stack
stack用法：
```cpp
#include <stack>
template < typename T, typename Container=deque > class stack;
```
可以使用三个标准顺序容器 vecotr、deque(默认)、list中的任何一个作为stack的底层模型。
```cpp
bool stack<T>::empty()                               //判断堆栈是否为空
void stack<T>::pop()                                 //弹出栈顶数据
stack<T>::push(T x)                                  //压入一个数据
stack<T>::size_type stack<T>::size()                 //返回堆栈长度
T stack<T>::top()                                    //得到栈顶数据
```
代码示例：
```cpp
stack<int> intDequeStack;  
stack<int,vector<int>> intVectorStack;  
stack<int,list<int>> intListStack; 
```

## queue
```cpp
include <queue>
template<typename T, typename Container = deque<T> > class  queue;
```
第一个参数指定要在queue中存储的类型，第二个参数规定queue适配的底层容器，可供选择的容器只有dequeue和list。对大多数用途使用默认的dequeue。
```cpp
queue<T>::push(T x)
void queue<T>::pop()
T queue<T>::back()
T queue<T>::front()
queue<T>::size_type 
queue<T>::size()
bool queue<T>::empty()
```
使用示例：
```cpp
queue<int> intDequeQueue;    
queue<int,list<int>> intListQueue;
```

## priority_queue
```cpp
#include <queue>
template <typename T, typename Container = vector<T>, typename Compare = less<T> > class priority_queue;
```
priority_queue也是一个队列，其元素按有序顺序排列。其不采用严格的 **FIFO** 顺序，给定时刻位于队头的元素正是有最高优先级的元素。如果两个元素有相同的优先级，那么它们在队列中的顺序就遵循FIFO语义。默认适配的底层容器是**vector**，也可以使用**deque**，list不能用，**因为priority_queue要求能对元素随机访问以便进行排序。**
```cpp
priority_queue<T>::push(T x)
void priority_queue<T>::pop()
T priority_queue<T>::top()
priority_queue<T>::size_type 
priority_queue<T>::size()
bool priority_queue<T>::empty()
```
代码示例：
```cpp
priority_queue< int, vector<int>, greater<int> >
priority_queue< int, list<int>, greater<int> >
```

标准库默认使用元素类型的`<`操作符来确定它们之间的优先级关系，用法有三：
1. 通过默认使用的`<`操作符可知在整数中元素大的优先级高。
```cpp
priority_queue<int> qi; 
```
2. 建立priority_queue时传入一个比较函数，使用functional.h函数对象作为比较函数。
```cpp
priority_queue<int, vector<int>, greater<int> >qi2;
```
3. 是自定义优先级。
```cpp
struct node 
{
     friend bool operator< (node n1, node n2)
     {
         return n1.priority < n2.priority;
     } 
     int priority;
     int value; 
}; 
priority_queue<node> qn; 
```
在该结构中，value为值，priority为优先级。通过自定义`operator<`操作符来比较元素中的优先级。
注意：**必须是自定义`<`操作符才行，把上述的结构中的`<`操作符改成`>`编译不通过。**