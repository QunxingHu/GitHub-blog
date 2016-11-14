---
title: C++ define 中的特殊符号
date: 2016-11-14 12:00:56 PM
categories:
- 术业专攻
tags: 
- C++
---
本篇博客主要介绍宏定义中出现的一些特殊符号，如 `#`，`##`，`@#`, `/` 等符号的含义及用法。
<!-- more -->
### 1. `#` 字符串化操作符（stringizing）
**作用:** 将 宏定义中传入的参数名转换为 用一对双引号刮起来的字符串
如：
```cpp
#define example(str) #str //替换结果为 “str”
```
**注意：**
该宏定义会忽略参数名前面， 后面的空格，对于参数名中间出现的空格，会将连续的多余一个以上的空格替换为一个空格。
如：
```cpp
#define example(str) #str
// str1 = example(  a b   c      e   f   )  //结果为str1 = ”a b c e f“
```
### 2. `##` 符号链接符（token-pasting）
**作用：** 将宏定义中的多个参数连接形成一个参数
如：
```cpp
#define cat(x,y) x##y
//cat(var,123)  //结果为 var123
```
**注意：**
用`##`连接时，`##`前后空格可有可无

### 3. `@#` 将值序列变为一个字符（charizing）
**作用：** 将传入的单字符参数名转换为字符
如：
```cpp
#define ch(c) #@c
// ch(a) //结果为‘a’
```
### 4. `\` 行继续操作符
**作用：** 当定义的宏不能用一行表达完整时，可以用`\`表示下一行继续此宏的定义
**注意：** 换行不能切断单词，只能在有空格的地方进行。

### 5. `do{}while(0)`
采用 这种方式进行宏定义，主要是为了防止出现以下错误：
1. 空的宏定义避免出现warnning
```cpp
#define foo() do{}while(0)
```
2. 存在一个独立的block，可以进行变量定义，进行比较复杂的实现
3. 若果出现在判断语句过后的宏，这样可以作为一个整体来实现
```cpp
#define foo(x)	\
	action1();	\
    action2();
```
在以下情况中：
```cpp
if (NULL == pointer)
	foo();		//在这种情况下就会出现 action1, action2不会被同时执行的情况。
```
4. 以上三种情况均可以用`{}`实现，但为什么一定要用`do{}while(0)`：
```cpp
#define switch(x,y) {int tmp; tmp =x; x = y; y = tmp;}
if (x > y)
	switch(x,y);	
else	//出现编译错误，宏引入代码后多一个分号
	otherAction();
```
将宏用`do{}while(0)`将宏包裹起来使其成为一个独立的语法单元，从而不会与上下文发生混淆，同时大部分编译器能够识别 `do{}while(0)` 这种无用的循环，并对其进行优化，所以使用这种方法不会导致性能的降低。
### 6. 宏的高级用法示例
```cpp
#include <stdio.h>

// '\'
#define PRINT1(a,b) \
    {   \
        printf("print a \n");   \
        printf("print b \n");   \
    }

// 'do{...}while(0)'
#define PRINT2(a,b) \
    do {    \
        printf("print a \n");   \
        printf("print b \n");   \
    } while(0)

// '#'
#define PRINT3(a,b) \
    do {    \
        printf("%s %d\n", #a, a);   \
        printf("%d %d\n", a, a);    \
    } while(0)

// ##
#define TYPE1(type, name)   type name_##type##_type
#define TYPE2(type, name)   type name##_##type##_type
#define ERROR_LOG(module) fprintf(stderr, "error : "#module"\n");

int main(int argc, char const *argv[])
{
    int a = 10;
    int b = 20;
    TYPE1(int, c);
    ERROR_LOG("addr");
    name_int_type = a;
    TYPE2(int, d);
    d_int_type = a;
    PRINT1(a,b);
    PRINT2(a,b);
    PRINT3(a,b);
    
    return 0;
}
```
