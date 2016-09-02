---
title: C++单元测试框架Googletest
date: 2016-08-30 23:46:36
categories:
- 术业专攻
tags: 
- C++
- gtest
---
这篇博客主要介绍 如何使用 Google 开源 的 C++ 单元测试框架 google test 创建单元测试。采用的环境为linux（ubuntu 14.04  64bit LTS, g++ 4.6.4, gtest 版本 2.40.2）。
<!-- more -->

## 获取编译gtest
1. 下载源码：http://code.google.com/p/googletest/downloads/list ，下载zip源码包，解压
2. 编译生成 `libgtest.a` 静态库
  - 进入源码目录
  - `cmake  .`
  - `make`
  - 生成`libgtest.a`, `libgmock.a`
 
 ![make][1]

3. 编译运行sample，测试
  -  进入`googletest/make`目录
  -  `make .`
  -  运行测试用例：`./sample1_unittest`
   
   ![make sample][2]

## 编写单元测试用例
为了方便应用和理解，我按照标准的C++工程目录结构创建了一个简单示例，其中待测试的是`Add.h`中`class Add`的成员函数`add()`，在`Add_test.cpp`利用gtest对`add()`进行测试。对于其中所涉及的知识点，接下来会进行解释。示例源码如下所示：

**代码目录结构：**
```shell
.
|____main.cpp			#程序入口
|____makefile			 #make file 文件
|____Add.h				  #待测试的类，只有一个成员函数add
|____Add.cpp			#待测试类的实现
|____Add_test.h	       #测试类的声明
|____Add_test.cpp	 #测试类的实现
```
**main.cpp** 
```cpp?linenums
#include "Add_test.h"
#include "gtest/gtest.h"
int main(int argc, char **argv)
{
	testing::InitGoogleTest(&argc, argv);
	RUN_ALL_TESTS();
	return 0;
}
```
**Add.h**
```cpp?linenums
/**
 * simple class Add declare
 */

#ifndef ADD_H
#define ADD_H
class Add
{
public:
	Add(){}
	~Add(){}
	static int add(int a, int b);
};
#endif
```
**Add.cpp**
```cpp?linenums
/**
 * simple implement for class Add
 */
#include "Add.h"

int Add::add(int a, int b){
	return a+b;
}
```
**Add_test.h**
```cpp?linenums
/**
 * THIS FILE IS AN DEMO FOR GTEST
 * Declare class Add_test
 */
#ifndef ADD_TEST_H
#define ADD_TEST_H

#include "Add.h"
#include "gtest/gtest.h"

/**
 * Test suite for Add.
 *
 * Impelementation of test suite for Add.
 */
class Add_test:public testing::Test
{
public:
    Add_test();
    virtual ~Add_test();
    /*
    virtual void SetUp ();		// do something before all case
    virtual void TearDown();	// do something after all case
	*/
    static void SetUpTestCase();	//do something before first testcase begin
    static void TearDownTestCase();	//do something after last testcase finished
};

#endif // ADD_TEST_H
```
**Add_test.cpp**
```cpp?linenums
/**
 * THIS FILE IS AN DEMO FOR GTEST
 * implement the class Add_test
 */
#include "Add_test.h"
#include <iostream>

using namespace std;

Add_test::Add_test()
{
    cout << "constructor for Add_test" << endl;
}

Add_test::~Add_test()
{
    cout << "destory Add_test" << endl;
}

void Add_test::SetUpTestCase()
{
    cout << "SetUpTestCase...." << endl;
}

void Add_test::TearDownTestCase()
{
    cout << "TearDownTestCase...." << endl;
}
/*测试1*/
TEST_F(Add_test, test1)
{
    int ret1 = Add::add(1,2);
    EXPECT_EQ(3,ret1);

    int ret2 = Add::add(2,-2);
    EXPECT_EQ(0,ret2);

    int ret3 = Add::add(199,299);
    EXPECT_LE(1,ret3);
    
}
/*测试2*/
TEST_F(Add_test, test2)
{
    int ret1 = Add::add(1,2);
    EXPECT_NE(0,ret1);

    int ret2 = Add::add(2,-2);
    EXPECT_NE(5,ret2);
    
}
```
**makefile**：makefile中需包含gtest的`include`目录以及`libgtest.a`的目录
```makefile?linenums
FLAGS = -I /home/huqunxing/Gtest/googletest/include
LIBS = -L /home/huqunxing/Gtest/googletest/ -lgtest -lpthread
objects = main.o Add.o Add_test.o

test:$(objects)
	g++ $(FLAGS) -o test $(objects) $(LIBS)

Add.o:Add.h

Add_test.o: Add_test.h Add.h
	g++ $(FLAGS) -o Add_test.o -c Add_test.cpp
    
main.o: Add_test.h
	g++ $(FLAGS) -o main.o -c main.cpp
    
.PHONY:clean
clean:
	rm test $(objects)
```
![gtest示例][3]

## 断言
gtest中断言相关的宏分为两种：ASSERT，EXCEPT。官方文档解释如下：
ASSERT_\*  :  generate fatal failures when they fail, and abort the current function. 
EXPECT_\*  :  generate nonfatal failures, which don't abort the current function. 
简而言之， ASSERT 检查失败时会退出，EXPECT不会。

我们可以使用`<<`自定义输出错误信息，帮助定位错误。用法如下：
```cpp?linenums
ASSERT_EQ(x.size(), y.size()) << "Vectors x and y are of unequal length";
for (int i = 0; i < x.size(); ++i) {
  EXPECT_EQ(x[i], y[i]) << "Vectors x and y differ at index " << i;
}
```
### 布尔值检查

| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
| `ASSERT_TRUE(`_condition_`)`;  | `EXPECT_TRUE(`_condition_`)`;   | _condition_ is true |
| `ASSERT_FALSE(`_condition_`)`; | `EXPECT_FALSE(`_condition_`)`;  | _condition_ is false |


### 数值型数据检查

| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
|`ASSERT_EQ(`_val1_`, `_val2_`);`|`EXPECT_EQ(`_val1_`, `_val2_`);`| _val1_ `==` _val2_ |
|`ASSERT_NE(`_val1_`, `_val2_`);`|`EXPECT_NE(`_val1_`, `_val2_`);`| _val1_ `!=` _val2_ |
|`ASSERT_LT(`_val1_`, `_val2_`);`|`EXPECT_LT(`_val1_`, `_val2_`);`| _val1_ `<` _val2_ |
|`ASSERT_LE(`_val1_`, `_val2_`);`|`EXPECT_LE(`_val1_`, `_val2_`);`| _val1_ `<=` _val2_ |
|`ASSERT_GT(`_val1_`, `_val2_`);`|`EXPECT_GT(`_val1_`, `_val2_`);`| _val1_ `>` _val2_ |
|`ASSERT_GE(`_val1_`, `_val2_`);`|`EXPECT_GE(`_val1_`, `_val2_`);`| _val1_ `>=` _val2_ |

失败情况下，会打印出 `val1` 和 `val2`的值。

### 字符串检查

| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
| `ASSERT_STREQ(`_str1_`, `_str2_`);`    | `EXPECT_STREQ(`_str1_`, `_str_2`);`     | the two C strings have the same content |
| `ASSERT_STRNE(`_str1_`, `_str2_`);`    | `EXPECT_STRNE(`_str1_`, `_str2_`);`     | the two C strings have different content |
| `ASSERT_STRCASEEQ(`_str1_`, `_str2_`);`| `EXPECT_STRCASEEQ(`_str1_`, `_str2_`);` | the two C strings have the same content, ignoring case |
| `ASSERT_STRCASENE(`_str1_`, `_str2_`);`| `EXPECT_STRCASENE(`_str1_`, `_str2_`);` | the two C strings have different content, ignoring case |


### 异常检查
|**Fatal assertion** 	|**Nonfatal assertion** 	|**Verifies**|
|:--------------------|:-----------------------|:-------------|
|ASSERT_THROW(statement, exception_type); 	|EXPECT_THROW(statement, exception_type); 	|statement throws an exception of the given type|
|ASSERT_ANY_THROW(statement); 	|EXPECT_ANY_THROW(statement); |	statement throws an exception of any type|
|ASSERT_NO_THROW(statement); 	|EXPECT_NO_THROW(statement); |	statement doesn't throw any exception|
示例：
```cpp?linenums
int Foo(int a, int b)
{
    if (a == 0 || b == 0)
    {
        throw "don't do that";
    }

    int c = a % b;
    if (c == 0)
        return b;
    return Foo(b, c);
}

TEST(FooTest, HandleZeroInput)
{
    EXPECT_ANY_THROW(Foo(10, 0));
    EXPECT_THROW(Foo(0, 5), char*);
}
```
### 浮点型检查
| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
|ASSERT_FLOAT_EQ(expected, actual); 	|EXPECT_FLOAT_EQ(expected, actual); |	the two float values are almost equal|
|ASSERT_DOUBLE_EQ(expected, actual); 	|EXPECT_DOUBLE_EQ(expected, actual); |	the two double values are almost equal|

**对相近的两个数比较：**
| **Fatal assertion** | **Nonfatal assertion** | **Verifies** |
|:--------------------|:-----------------------|:-------------|
|ASSERT_NEAR(val1, val2, abs_error); 	|EXPECT_NEAR(val1, val2, abs_error); 	|the difference between val1 and val2 doesn't exceed the given absolute error |

### 直接返回成功或失败
- 直接返回成功： `SUCCEED( )`
- 返回失败： `FAIL() ` (不往下执行)， `ADD_FAILURE()`(继续往下执行)

## 事件机制
gtest事件一共有三种：
- 全局级别：所有案例执行前后
- testsuit级别：在某一批案例中第一个案例前，最后一个案例执行后
- testcase级别：每个TestCase前后
### 全局事件
要实现全局事件，必须写一个类，继承`testing::Environment`类，实现里面的`SetUp`和`TearDown`方法。
- SetUp()方法在所有案例执行前执行
- TearDown()方法在所有案例执行后执行
```cpp?linenums
class FooEnvironment : public testing::Environment
{
public:
    virtual void SetUp()
    {
        std::cout << "Foo FooEnvironment SetUP" << std::endl;
    }

    virtual void TearDown()
    {
        std::cout << "Foo FooEnvironment TearDown" << std::endl;
    }
};
```
当然，这样还不够，我们还需要告诉gtest添加这个全局事件，我们需要在main函数中通过testing::AddGlobalTestEnvironment方法将事件挂进来，也就是说，我们可以写很多个这样的类，然后将他们的事件都挂上去。
```cpp?linenums
int main(int argc, char** argv[])
{
    testing::AddGlobalTestEnvironment(new FooEnvironment);
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
### testsuit事件
我们需要写一个类，继承`testing::Test`，然后实现两个静态方法`SetUpTestCase()`,`TearDownTestCase()`
-  SetUpTestCase() 方法在第一个TestCase之前执行
- TearDownTestCase() 方法在最后一个TestCase之后执行
```cpp?linenums
class FooTest : public testing::Test {
 protected:
  static void SetUpTestCase() {
    shared_resource_ = new ;
  }
  static void TearDownTestCase() {
    delete shared_resource_;
    shared_resource_ = NULL;
  }
  // Some expensive resource shared by all tests.
  static T* shared_resource_;
};
```
在编写测试案例时，我们需要使用`TEST_F`这个宏，**第一个参数必须是我们上面类的名字，代表一个TestSuite**。
```cpp?linenums
TEST_F(FooTest, Test1){
    // you can refer to shared_resource here 
}
TEST_F(FooTest, Test2){
    // you can refer to shared_resource here 
}
```
### testcase事件
TestCase事件是挂在每个案例执行前后的，实现方式和上面的几乎一样，不过需要实现的是`SetUp`方法和`TearDown`方法
- SetUp()方法在每个TestCase之前执行
- TearDown()方法在每个TestCase之后执行
```cpp?linenums
class FooCalcTest:public testing::Test
{
protected:
    virtual void SetUp()
    {
      m_foo.Init();
    }
    virtual void TearDown()
    {
        m_foo.Finalize();
    }
    FooCalc m_foo;
};

TEST_F(FooCalcTest, HandleNoneZeroInput)
{
    EXPECT_EQ(4, m_foo.Calc(12, 16));
}
TEST_F(FooCalcTest, HandleNoneZeroInput_Error)
{
    EXPECT_EQ(5, m_foo.Calc(12, 16));
}
```
gtest提供的这三种事件机制还是非常的简单和灵活的。同时，通过继承Test类，使用TEST_F宏，我们可以在案例之间共享一些通用方法，共享资源。使得我们的案例更加的简洁，清晰。
## 参数化测试
假设我们需要传入一系列数值让函数IsPrime去判断是否为True，使用之前的测试方案，写法如下：
```cpp?linenums
TEST(IsPrimeTest, HandleTrueReturn)
{
    EXPECT_TRUE(IsPrime(3));
    EXPECT_TRUE(IsPrime(5));
    EXPECT_TRUE(IsPrime(11));
    EXPECT_TRUE(IsPrime(23));
    EXPECT_TRUE(IsPrime(17));
}
```
使用参数化测试方案，写法如下：
1. 添加一个类，继承`testting::TestWithParam<T>`,其中`T`就是你需要参数化的参数类型，比如判定素数，我需要参数化一个`int`型的参数。
```cpp?linenums
class IsPrimeParamTest : public::testing::TestWithParam<int>
{

};
```
2. 使用宏`TEST_P`，并在`TEST_P`宏中，使用`GetParam`获取当前的参数的具体值。
```cpp?linenums
TEST_P(IsPrimeParamTest, HandleTrueReturn)
{
    int n =  GetParam();
    EXPECT_TRUE(IsPrime(n));
}
```
3. 使用`INSTANTIATE_TEST_CASE_P`设置参数范围
```cpp?linenums
INSTANTIATE_TEST_CASE_P(TrueReturn, IsPrimeParamTest, testing::Values(3, 5, 11, 23, 17));
```
		参数说明：
		第一个参数是测试案例的前缀，可以任意取。
		第二个参数是测试案例的名称，需要和之前定义的参数化的类的名称相同，如：IsPrimeParamTest	
		第三个参数是可以理解为参数生成器，上面的例子使用test::Values表示使用括号内的参数。Google提供了一系列的参数生成的函数：
  | 函数名 | 函数说明 |
  | :--------|:-----------|
  |ange(begin, end[, step]) 	|范围在begin~end之间，步长为step，不包括end
  |Values(v1, v2, ..., vN) 	|v1,v2到vN的值|
  |ValuesIn(container) and ValuesIn(begin, end) |	从一个C类型的数组或是STL容器，或是迭代器中取值
  | Bool() 	|取false 和 true 两个值|
  | Combine(g1, g2, ..., gN) 	| 这个比较强悍，它将g1,g2,...gN进行排列组合，g1,g2,...gN本身是一个参数生成器，每次分别从g1,g2,..gN中各取出一个值，组合成一个元组(Tuple)作为一个参数。说明：这个功能只在提供了<tr1/tuple>头的系统中有效。gtest会自动去判断是否支持tr/tuple，如果你的系统确实支持，而gtest判断错误的话，你可以重新定义宏GTEST_HAS_TR1_TUPLE=1。|

### 类型参数化
gtest还提供了应付各种不同类型的数据时的方案，以及参数化类型的方案。
1. 首先定义一个模板类，继承`testing::Test`
```cpp?linenums
template <typename T>
class FooTest : public testing::Test {
 public:
  typedef std::list<T> List;
  static T shared_;
  T value_;
};
```
2. 定义需要测试的具体数据类型（如char， int，unsigned int）
```cpp?linenums
typedef testing::Types<char, int, unsigned int> MyTypes;
TYPED_TEST_CASE(FooTest, MyTypes);
```
3. 使用宏`TYPED_TEST`完成测试案例，在声明模版的数据类型时，使用TypeParam 
```cpp?linenums
TYPED_TEST(FooTest, DoesBlah) {
  // Inside a test, refer to the special name TypeParam to get the type
  // parameter.  Since we are inside a derived class template, C++ requires
  // us to visit the members of FooTest via 'this'.
  TypeParam n = this->value_;

  // To visit static members of the fixture, add the 'TestFixture::'
  // prefix.
  n += TestFixture::shared_;

  // To refer to typedefs in the fixture, add the 'typename TestFixture::'
  // prefix.  The 'typename' is required to satisfy the compiler.
  typename TestFixture::List values;
  values.push_back(n);
  
}
```
上面的例子看上去也像是类型的参数化，但是还不够灵活，因为需要事先知道类型的列表。gtest还提供一种更加灵活的类型参数化的方式，允许你在完成测试的逻辑代码之后再去考虑需要参数化的类型列表，并且还可以重复的使用这个类型列表。下面也是官方的例子：
```cpp?linenums
template <typename T>
class FooTest : public testing::Test {
  
};

TYPED_TEST_CASE_P(FooTest);
```
接着，使用宏`TYPED_TEST_P`完成测试案例
```cpp?linenums
TYPED_TEST_P(FooTest, DoesBlah) {
  // Inside a test, refer to TypeParam to get the type parameter.
  TypeParam n = 0;
  
}

TYPED_TEST_P(FooTest, HasPropertyA) {  }
```
接着，我们需要我们上面的案例，使用`REGISTER_TYPED_TEST_CASE_P`宏，第一个参数是testcase的名称，后面的参数是test的名称
```cpp?linenums
REGISTER_TYPED_TEST_CASE_P(FooTest, DoesBlah, HasPropertyA);
```
接着指定需要的类型列表：
```cpp?linenums
typedef testing::Types<char, int, unsigned int> MyTypes;
INSTANTIATE_TYPED_TEST_CASE_P(My, FooTest, MyTypes);
```
这种方案相比之前的方案提供更加好的灵活度，当然，框架越灵活，复杂度也会随之增加。

## 死亡测试
通常在测试过程中，我们需要考虑各种各样的输入，有的输入可能直接导致程序崩溃，这时我们就需要检查程序是否按照预期的方式挂掉，这也就是所谓的“死亡测试”。gtest的死亡测试能做到在一个安全的环境下执行崩溃的测试案例，同时又对崩溃结果进行验证。

**使用的宏**：
|**Fatal assertion** |	**Nonfatal assertion** |	**Verifies**|
|:--------------------|:--------------------------|:------------|
|ASSERT_DEATH(statement, regex); 	|EXPECT_DEATH(statement, regex); 	|statement crashes with the given error|
|ASSERT_EXIT(statement, predicate, regex);| EXPECT_EXIT(statement, predicate, regex); |	statement exits with the given error and its exit code matches predicate|

由于有些异常只在Debug下抛出，因此还提供了*_DEBUG_DEATH，用来处理Debug和Realease下的不同。
### 宏 `*_DEATH(statement, regex)`
statement: 被测试的代码语句
regex: 正则表达式，用于匹配异常时在stderr中输出的内容
示例：
```cpp?linenums
void Foo()
{
    int *pInt = 0;
    *pInt = 42 ;
}
/*编写死亡测试案例时，TEST的第一个参数，即testcase_name，请使用DeathTest后缀。原因是gtest会优先运行死亡测试案例，应该是为线程安全考虑。*/
TEST(FooDeathTest, Demo)
{
    EXPECT_DEATH(Foo(), "");
}
```

### 宏 `*_EXIT(statement, predicate, regex)`
statement: 被测试的代码语句
**predicate:** 在这里必须是一个委托，接收int型参数，并返回bool。只有当返回值为true时，死亡测试案例才算通过。gtest提供了一些常用的predicate：
```cpp?linenums
testing::ExitedWithCode(exit_code); //如果程序正常退出并且退出码与exit_code相同则返回 true
testing::KilledBySignal(signal_number);  //Windows下不支持,如果程序被signal_number信号kill的话就返回true
```
regex: 是一个正则表达式，用来匹配异常时在stderr中输出的内容
这里， 要说明的是，`*_DEATH`其实是对`*_EXIT`进行的一次包装，`*_DEATH`的predicate判断进程是否以非0退出码退出或被一个信号杀死。
示例：
```cpp?linenums
TEST(ExitDeathTest, Demo)
{
    EXPECT_EXIT(_exit(1),  testing::ExitedWithCode(1),  "");
}
```
### 宏 `*_DEBUG_DEATH(statement, regex)`
gtest中关于DEBUG_DEATH的定义
```cpp?linenums
#ifdef NDEBUG
#define EXPECT_DEBUG_DEATH(statement, regex) \
  do { statement; } while (false)

#define ASSERT_DEBUG_DEATH(statement, regex) \
  do { statement; } while (false)

#else

#define EXPECT_DEBUG_DEATH(statement, regex) \
  EXPECT_DEATH(statement, regex)

#define ASSERT_DEBUG_DEATH(statement, regex) \
  ASSERT_DEATH(statement, regex)

#endif  // NDEBG for EXPECT_DEBUG_DEATH
```
可以看到，在Debug版和Release版本下， *_DEBUG_DEATH的定义不一样。因为很多异常只会在Debug版本下抛出，而在Realease版本下不会抛出，所以针对Debug和Release分别做了不同的处理。看gtest里自带的例子就明白了：
```cpp?linenums
int DieInDebugElse12(int* sideeffect) {
    if (sideeffect) *sideeffect = 12;
#ifndef NDEBUG
    GTEST_LOG_(FATAL, "debug death inside DieInDebugElse12()");
#endif  // NDEBUG
    return 12;
}

TEST(TestCase, TestDieOr12WorksInDgbAndOpt)
{
    int sideeffect = 0;
    // Only asserts in dbg.
    EXPECT_DEBUG_DEATH(DieInDebugElse12(&sideeffect), "death");

    #ifdef NDEBUG
    // opt-mode has sideeffect visible.
    EXPECT_EQ(12, sideeffect);
    #else
    // dbg-mode no visible sideeffect.
    EXPECT_EQ(0, sideeffect);
    #endif
}
```
关于正则表达式：
在POSIX系统（Linux, Cygwin, 和 Mac）中，gtest的死亡测试中使用的是POSIX风格的正则表达式，想了解POSIX风格表达式可参考：
1. [POSIX extended regular expression][4]
2. [Wikipedia entry.][5]
在Windows系统中，gtest的死亡测试中使用的是gtest自己实现的简单的正则表达式语法。 相比POSIX风格，gtest的简单正则表达式少了很多内容，比如 ("x|y"), ("(xy)"), ("[xy]") 和("x{5,7}")都不支持。

gtest定义两个宏，用来表示当前系统支持哪套正则表达式风格：
1. POSIX风格：GTEST_USES_POSIX_RE = 1
2. Simple风格：GTEST_USES_SIMPLE_RE=1 

### 死亡测试运行方式
1. fast方式（默认的方式）
```cpp?linenums
testing::FLAGS_gtest_death_test_style = "fast";
```
2. threadsafe方式
```cpp?linenums
testing::FLAGS_gtest_death_test_style = "threadsafe";
```
你可以在 main() 里为所有的死亡测试设置测试形式，也可以为某次测试单独设置。Google Test会在每次测试之前保存这个标记并在测试完成后恢复，所以你不需要去管这部分工作 。
```cpp?linenums
TEST(MyDeathTest, TestOne) {
  testing::FLAGS_gtest_death_test_style = "threadsafe";
  // This test is run in the "threadsafe" style:
  ASSERT_DEATH(ThisShouldDie(), "");
}

TEST(MyDeathTest, TestTwo) {
  // This test is run in the "fast" style:
  ASSERT_DEATH(ThisShouldDie(), "");
}

int main(int argc, char** argv) {
  testing::InitGoogleTest(&argc, argv);
  testing::FLAGS_gtest_death_test_style = "fast";
  return RUN_ALL_TESTS();
}
```
### 死亡测试注意事项
1. 不要在死亡测试里释放内存。
2. 在父进程里再次释放内存。
3. 不要在程序中使用内存堆检查。

## 设置运行参数
对于运行时参数设置，gtest提供了三种方案：
1. 命令行参数
2. 代码中指定FLAG
3. 系统环境变量
优先级：命令行参数 > 代码中flag > 系统环境变量

```cpp?linenums
int main(int argc, char* argv[])
{
    testing::GTEST_FLAG(output) = "xml:";	//代码中设置FLAG，指定输出xml
    testing::InitGoogleTest(&argc, argv);		//接收命令行参数
    return RUN_ALL_TESTS();
}
```
使用系统环境变量设置运行参数
如果需要gtest的设置系统环境变量，必须注意的是：
1. 系统环境变量全大写，比如对于--gtest_output，响应的系统环境变量为：GTEST_OUTPUT
2.  有一个命令行参数例外，那就是--gtest_list_tests，它是不接受系统环境变量的。（只是用来罗列测试案例名称

**参数列表**：
如果想要获得详细的命令行说明，直接运行你的案例，输入命令行参数：/? 或 --help 或 -help
 1. 测试案例集合

|**命令行参数** |	**说明**|
|:--------------------|:-------------|
|--gtest_list_tests 	|使用这个参数时，将不会执行里面的测试案例，而是输出一个案例的列表。|
|--gtest_filter 	|	对执行的测试案例进行过滤，支持通配符 <br>?    单个字符<br>*    任意字符<br>-    排除，如，-a 表示除了a<br>:    取或，如，a:b 表示a或b<br>比如下面的例子：<br>`./foo_test `没有指定过滤条件，运行所有案例<br>`./foo_test --gtest_filter=*` 使用通配符*，表示运行所有案例<br>`./foo_test --gtest_filter=FooTest.*` 运行所有“测试案例名称(testcase_name)”为FooTest的案例<br>`./foo_test --gtest_filter=*Null*:*Constructor*` 运行所有“测试案例名称(testcase_name)”或“测试名称(test_name)”包含Null或Constructor的案例。<br>`./foo_test --gtest_filter=-*DeathTest.*` 运行所有非死亡测试案例。<br>`./foo_test --gtest_filter=FooTest.*-FooTest.Bar` 运行所有“测试案例名称(testcase_name)”为FooTest的案例，但是除了FooTest.Bar这个案例<br>|
|--gtest_also_run_disabled_tests 	|执行案例时，同时也执行被置为无效的测试案例。关于设置测试案例无效的方法为：<br>在测试案例名称或测试名称中添加DISABLED前缀，比如： <br><code>// Tests that Foo does Abc.<br>TEST(FooTest, DISABLED_DoesAbc) {  }<br>class DISABLED_BarTest : public testing::Test {  };<br>// Tests that Bar does Xyz.<br>TEST_F(DISABLED_BarTest, DoesXyz) {  }<br></code>|
|--gtest_repeat=[COUNT] |	设置案例重复运行次数，非常棒的功能！比如：<br>`--gtest_repeat=1000`      重复执行1000次，即使中途出现错误。<br>`--gtest_repeat=-1`          无限次数执行。。。。<br>`--gtest_repeat=1000 --gtest_break_on_failure`     重复执行1000次，并且在第一个错误发生时立即停止。这个功能对调试非常有用。<br>`--gtest_repeat=1000 --gtest_filter=FooBar`     重复执行1000次测试案例名称为FooBar的案例。<br>|

 

2. 测试案例输出

|**命令行参数** |	**说明**|
|:--------------------|:-------------|
|--gtest_color=(yes|no|auto) |	输出命令行时是否使用一些五颜六色的颜色。默认是auto。
|--gtest_print_time 	|输出命令行时是否打印每个测试案例的执行时间。默认是不打印的。
|--gtest_output=xml[:DIRECTORY_PATH\|:FILE_PATH] |将测试结果输出到一个xml中。<br>`--gtest_output=xml:  `  不指定输出路径时，默认为案例当前路径。<br>`--gtest_output=xml:d:\ `指定输出到某个目录<br>`--gtest_output=xml:d:\foo.xml` 指定输出到d:\foo.xml<br>如果不是指定了特定的文件路径，gtest每次输出的报告不会覆盖，而会以数字后缀的方式创建。xml的输出内容后面介绍吧。

 

3. 对案例的异常处理

|**命令行参数** |	**说明**|
|:--------------------|:-------------|
|--gtest_break_on_failure 	|调试模式下，当案例失败时停止，方便调试
|--gtest_throw_on_failure 	|当案例失败时以C++异常的方式抛出
|--gtest_catch_exceptions 	|是否捕捉异常。gtest默认是不捕捉异常的，因此假如你的测试案例抛了一个异常，很可能会弹出一个对话框，这非常的不友好，同时也阻碍了测试案例的运行。如果想不弹这个框，可以通过设置这个参数来实现。如将--gtest_catch_exceptions设置为一个非零的数。<br>注意：这个参数只在Windows下有效。|

**比较常用的运行参数设置是**：
1. `--gtest_filter`
2. `--gtest_output=xml[:DIRECTORY_PATH\|:FILE_PATH]`
3. ` --gtest_catch_exceptions `





参考资料：
官方文档：[ gtest ](https://github.com/google/googletest/blob/master/googletest/docs/Primer.md) [gmock](https://github.com/google/googletest/blob/master/googlemock/README.md)
[玩转gtest](http://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)


  [1]: ./images/1472522982760.jpg "编译gtest，gmock"
  [2]: ./images/1472523836623.jpg "编译测试用例"
  [3]: ./images/1472541201310.jpg "编译运行第一个示例"
  [4]: http://pubs.opengroup.org/onlinepubs/009695399/basedefs/xbd_chap09.html#tag_09_04
  [5]: https://en.wikipedia.org/wiki/Regular_expression#POSIX_Extended_Regular_Expressions