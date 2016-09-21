---
title: C++ 多线程
date: 2016-9-18 5:18:23 PM
categories:
 - 术业专攻
tags: 
 - C++
---
本篇博客主要介绍C++ 的多线程机制。
<!-- more -->
多线程是多任务处理的一种特殊形式，多任务处理允许让电脑同时运行两个或两个以上的程序。一般情况下，两种类型的多任务处理：基于进程和基于线程。
 - 基于进程的多任务处理是程序的并发执行。
 - 线程的多任务处理是同一程序的片段的并发执行。

多线程程序包含可以同时运行的两个或多个部分。这样的程序中的每个部分称为一个线程，每个线程定义了一个单独的执行路径。

C++ 不包含多线程应用程序的任何内置支持。相反，它完全依赖于操作系统来提供此功能。

本教程假设您使用的是 Linux 操作系统，我们要使用 POSIX 编写多线程 C++ 程序。POSIX Threads 或 Pthreads 提供的 API 可在多种类 Unix POSIX 系统上可用，比如 FreeBSD、NetBSD、GNU/Linux、Mac OS X 和 Solaris。

## 创建线程
下面的程序，我们可以用它来创建一个 POSIX 线程：
```cpp
#include <pthread.h>
pthread_create (thread, attr, start_routine, arg) 
```
在这里，`pthread_create` 创建一个新的线程，并让它可执行。下面是关于参数的说明：
### 参数说明

| 参数 | 说明|
|:-------|:------|
|thread 	|指向线程标识符指针。|
|attr |	一个不透明的属性对象，可以被用来设置线程属性。您可以指定线程属性对象，也可以使用默认值 NULL。|
|start_routine |	线程运行函数起始地址，一旦线程被创建就会执行。|
|arg |	运行函数的参数。它必须通过把引用作为指针强制转换为 void 类型进行传递。如果没有传递参数，则使用 NULL。|

创建线程成功时，函数返回 0，若返回值不为 0 则说明创建线程失败。
## 终止线程
使用下面的程序，我们可以用它来终止一个 POSIX 线程：
```cpp
#include <pthread.h>
pthread_exit (status) 
```
在这里，`pthread_exit` 用于显式地退出一个线程。通常情况下，pthread_exit() 函数是在线程完成工作后无需继续存在时被调用。

如果 main() 是在它所创建的线程之前结束，并通过 pthread_exit() 退出，那么其他线程将继续执行。否则，它们将在 main() 结束时自动被终止。

实例：
以下简单的实例代码使用 pthread_create() 函数创建了 5 个线程，每个线程输出"Hello Runoob！":
```cpp
#include <iostream>
// 必须的头文件是
#include <pthread.h>

using namespace std;

#define NUM_THREADS 5

// 线程的运行函数,函数返回的是函数指针，便于后面作为参数  
void* say_hello(void* args)
{
    cout << "Hello Runoob！" << endl;
}

int main()
{
    // 定义线程的 id 变量，多个变量使用数组
    pthread_t tids[NUM_THREADS];
    for(int i = 0; i < NUM_THREADS; ++i)
    {
        //参数依次是：创建的线程id，线程参数，调用的函数，传入的函数参数
        int ret = pthread_create(&tids[i], NULL, say_hello, NULL);
        if (ret != 0)
        {
           cout << "pthread_create error: error_code=" << ret << endl;
        }
    }
    //等各个线程退出后，进程才结束，否则进程强制结束了，线程可能还没反应过来；
    pthread_exit(NULL);
}
```
使用 -lpthread 库编译下面的程序：

`$ g++ test.cpp -lpthread -o test.o`

现在，执行程序，将产生下列结果：
```
$ ./test.o
Hello Runoob！
Hello Runoob！
Hello Runoob！
Hello Runoob！
Hello Runoob！
```
以下简单的实例代码使用 pthread_create() 函数创建了 5 个线程，并接收传入的参数。每个线程打印一个 "Hello Runoob!" 消息，并输出接收的参数，然后调用 pthread_exit() 终止线程。
```cpp
//文件名：test.cpp

#include <iostream>
#include <cstdlib>
#include <pthread.h>

using namespace std;

#define NUM_THREADS     5

void *PrintHello(void *threadid)
{  
   // 对传入的参数进行强制类型转换，由无类型指针变为整形数指针，然后再读取
   int tid = *((int*)threadid);
   cout << "Hello Runoob! 线程 ID, " << tid << endl;
   pthread_exit(NULL);
}

int main ()
{
   pthread_t threads[NUM_THREADS];
   int indexes[NUM_THREADS];// 用数组来保存i的值
   int rc;
   int i;
   for( i=0; i < NUM_THREADS; i++ ){      
      cout << "main() : 创建线程, " << i << endl;
      indexes[i] = i; //先保存i的值
      // 传入的时候必须强制转换为void* 类型，即无类型指针        
      rc = pthread_create(&threads[i], NULL, 
                          PrintHello, (void *)&(indexes[i]));
      if (rc){
         cout << "Error:无法创建线程," << rc << endl;
         exit(-1);
      }
   }
   pthread_exit(NULL);
}
```
现在编译并执行程序，将产生下列结果：
```
$ g++ test.cpp -lpthread -o test.o
$ ./test.o
main() : 创建线程, 0
main() : 创建线程, 1
main() : 创建线程, 2
main() : 创建线程, 3
main() : 创建线程, 4
Hello Runoob! 线程 ID, 4
Hello Runoob! 线程 ID, 3
Hello Runoob! 线程 ID, 2
Hello Runoob! 线程 ID, 1
Hello Runoob! 线程 ID, 0
```
## 向线程传递参数

这个实例演示了如何通过结构传递多个参数。您可以在线程回调中传递任意的数据类型，因为它指向 void，如下面的实例所示：
```cpp
#include <iostream>
#include <cstdlib>
#include <pthread.h>

using namespace std;

#define NUM_THREADS     5

struct thread_data{
   int  thread_id;
   char *message;
};

void *PrintHello(void *threadarg)
{
   struct thread_data *my_data;

   my_data = (struct thread_data *) threadarg;

   cout << "Thread ID : " << my_data->thread_id ;
   cout << " Message : " << my_data->message << endl;

   pthread_exit(NULL);
}

int main ()
{
   pthread_t threads[NUM_THREADS];
   struct thread_data td[NUM_THREADS];
   int rc;
   int i;

   for( i=0; i < NUM_THREADS; i++ ){
      cout <<"main() : creating thread, " << i << endl;
      td[i].thread_id = i;
      td[i].message = "This is message";
      rc = pthread_create(&threads[i], NULL,
                          PrintHello, (void *)&td[i]); //传入到参数必须强转为void*类型，即无类型指针
      if (rc){
         cout << "Error:unable to create thread," << rc << endl;
         exit(-1);
      }
   }
   pthread_exit(NULL);
}
```
当上面的代码被编译和执行时，它会产生下列结果：
```
$ g++ -Wno-write-strings test.cpp -lpthread -o test.o
$ ./test.o
main() : creating thread, 0
main() : creating thread, 1
main() : creating thread, 2
main() : creating thread, 3
main() : creating thread, 4
Thread ID : 3 Message : This is message
Thread ID : 2 Message : This is message
Thread ID : 0 Message : This is message
Thread ID : 1 Message : This is message
Thread ID : 4 Message : This is message
```
## 连接和分离线程

我们可以使用以下两个函数来连接或分离线程：
```cpp
pthread_join (threadid, status) 
pthread_detach (threadid) 
```
`pthread_join()` 子程序阻碍调用程序，直到指定的 `threadid` 线程终止为止。当创建一个线程时，它的某个属性会定义它是否是可连接的（joinable）或可分离的（detached）。只有创建时定义为可连接的线程才可以被连接。如果线程创建时被定义为可分离的，则它永远也不能被连接。

这个实例演示了如何使用 pthread_join() 函数来等待线程的完成。
```
#include <iostream>
#include <cstdlib>
#include <pthread.h>
#include <unistd.h>

using namespace std;

#define NUM_THREADS     5

void *wait(void *t)
{
   int i;
   long tid;

   tid = (long)t;

   sleep(1);
   cout << "Sleeping in thread " << endl;
   cout << "Thread with id : " << tid << "  ...exiting " << endl;
   pthread_exit(NULL);
}

int main ()
{
   int rc;
   int i;
   pthread_t threads[NUM_THREADS];
   pthread_attr_t attr;
   void *status;

   // 初始化并设置线程为可连接的（joinable）
   pthread_attr_init(&attr);
   pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

   for( i=0; i < NUM_THREADS; i++ ){
      cout << "main() : creating thread, " << i << endl;
      rc = pthread_create(&threads[i], NULL, wait, (void *)i );
      if (rc){
         cout << "Error:unable to create thread," << rc << endl;
         exit(-1);
      }
   }

   // 删除属性，并等待其他线程
   pthread_attr_destroy(&attr);
   for( i=0; i < NUM_THREADS; i++ ){
      rc = pthread_join(threads[i], &status);
      if (rc){
         cout << "Error:unable to join," << rc << endl;
         exit(-1);
      }
      cout << "Main: completed thread id :" << i ;
      cout << "  exiting with status :" << status << endl;
   }

   cout << "Main: program exiting." << endl;
   pthread_exit(NULL);
}
```
当上面的代码被编译和执行时，它会产生下列结果：
```
main() : creating thread, 0
main() : creating thread, 1
main() : creating thread, 2
main() : creating thread, 3
main() : creating thread, 4
Sleeping in thread 
Thread with id : 4  ...exiting 
Sleeping in thread 
Thread with id : 3  ...exiting 
Sleeping in thread 
Thread with id : 2  ...exiting 
Sleeping in thread 
Thread with id : 1  ...exiting 
Sleeping in thread 
Thread with id : 0  ...exiting 
Main: completed thread id :0  exiting with status :0
Main: completed thread id :1  exiting with status :0
Main: completed thread id :2  exiting with status :0
Main: completed thread id :3  exiting with status :0
Main: completed thread id :4  exiting with status :0
Main: program exiting.
```

## 互斥锁的实现
互斥锁是实现线程同步的一种机制，只要在临界区前后对资源加锁就能阻塞其他进程的访问。
```cpp
#include <iostream>
#include <pthread.h>

using namespace std;

#define NUM_THREADS 5

int sum = 0; //定义全局变量，让所有线程同时写，这样就需要锁机制
pthread_mutex_t sum_mutex; //互斥锁

void* say_hello( void* args )
{
    cout << "hello in thread " << *(( int * )args) << endl;
    pthread_mutex_lock( &sum_mutex ); //先加锁，再修改sum的值，锁被占用就阻塞，直到拿到锁再修改sum;
    cout << "before sum is " << sum << " in thread " << *( ( int* )args ) << endl;
    sum += *( ( int* )args );
    cout << "after sum is " << sum << " in thread " << *( ( int* )args ) << endl;
    pthread_mutex_unlock( &sum_mutex ); //释放锁，供其他线程使用
    pthread_exit( 0 );
}

int main()
{
    pthread_t tids[NUM_THREADS];
    int indexes[NUM_THREADS];

    pthread_attr_t attr; //线程属性结构体，创建线程时加入的参数
    pthread_attr_init( &attr ); //初始化
    pthread_attr_setdetachstate( &attr, PTHREAD_CREATE_JOINABLE ); //是设置你想要指定线程属性参数，这个参数表明这个线程是可以join连接的，join功能表示主程序可以等线程结束后再去做某事，实现了主程序和线程同步功能
    pthread_mutex_init( &sum_mutex, NULL ); //对锁进行初始化

    for( int i = 0; i < NUM_THREADS; ++i )
    {
        indexes[i] = i;
        int ret = pthread_create( &tids[i], &attr, say_hello, ( void* )&( indexes[i] ) ); //5个进程同时去修改sum
        if( ret != 0 )
        {
            cout << "pthread_create error:error_code=" << ret << endl;
        }
    }
    pthread_attr_destroy( &attr ); //释放内存
    void *status;
    for( int i = 0; i < NUM_THREADS; ++i )
    {
        int ret = pthread_join( tids[i], &status ); //主程序join每个线程后取得每个线程的退出信息status
        if( ret != 0 )
        {
            cout << "pthread_join error:error_code=" << ret << endl;
        }
    }
    cout << "finally sum is " << sum << endl;
    pthread_mutex_destroy( &sum_mutex ); //注销锁
}
```
测试结果：
```
hello in thread hello in thread 1hello in thread 3
0
hello in thread 2
before sum is 0 in thread 1
hello in thread 4
after sum is 1 in thread 1

before sum is 1 in thread 3
after sum is 4 in thread 3
before sum is 4 in thread 4
after sum is 8 in thread 4
before sum is 8 in thread 0
after sum is 8 in thread 0
before sum is 8 in thread 2
after sum is 10 in thread 2
finally sum is 10
```
可知，sum的访问和修改顺序是正常的，这就达到了多线程的目的了，但是线程的运行顺序是混乱的，混乱就是正常？

## 信号量的实现
信号量是线程同步的另一种实现机制，信号量的操作有`signal`和`wait`，本例子采用条件信号变量
```cpp
pthread_cond_t tasks_cond;
```
信号量的实现也要给予锁机制。
```cpp
#include <iostream>
#include <pthread.h>
#include <stdio.h>

using namespace std;

#define BOUNDARY 5

int tasks = 10;
pthread_mutex_t tasks_mutex; //互斥锁
pthread_cond_t tasks_cond; //条件信号变量，处理两个线程间的条件关系，当task>5，hello2处理，反之hello1处理，直到task减为0

void* say_hello2( void* args )
{
    pthread_t pid = pthread_self(); //获取当前线程id
    cout << "[" << pid << "] hello in thread " <<  *( ( int* )args ) << endl;

    bool is_signaled = false; //sign
    while(1)
    {
        pthread_mutex_lock( &tasks_mutex ); //加锁
        if( tasks > BOUNDARY )
        {
            cout << "[" << pid << "] take task: " << tasks << " in thread " << *( (int*)args ) << endl;
            --tasks; //modify
        }
        else if( !is_signaled )
        {
            cout << "[" << pid << "] pthread_cond_signal in thread " << *( ( int* )args ) << endl;
            pthread_cond_signal( &tasks_cond ); //signal:向hello1发送信号，表明已经>5
            is_signaled = true; //表明信号已发送，退出此线程
        }
        pthread_mutex_unlock( &tasks_mutex ); //解锁
        if( tasks == 0 )
            break;
    }
}

void* say_hello1( void* args )
{
    pthread_t pid = pthread_self(); //获取当前线程id
    cout << "[" << pid << "] hello in thread " <<  *( ( int* )args ) << endl;

    while(1)
    {
        pthread_mutex_lock( &tasks_mutex ); //加锁
        if( tasks > BOUNDARY )
        {
            cout << "[" << pid << "] pthread_cond_signal in thread " << *( ( int* )args ) << endl;
            pthread_cond_wait( &tasks_cond, &tasks_mutex ); //wait:等待信号量生效，接收到信号，向hello2发出信号，跳出wait,执行后续
        }
        else
        {
            cout << "[" << pid << "] take task: " << tasks << " in thread " << *( (int*)args ) << endl;
            --tasks;
        }
        pthread_mutex_unlock( &tasks_mutex ); //解锁
        if( tasks == 0 )
            break;
    }
}

int main()
{
    pthread_attr_t attr; //线程属性结构体，创建线程时加入的参数
    pthread_attr_init( &attr ); //初始化
    pthread_attr_setdetachstate( &attr, PTHREAD_CREATE_JOINABLE ); //是设置你想要指定线程属性参数，这个参数表明这个线程是可以join连接的，join功能表示主程序可以等线程结束后再去做某事，实现了主程序和线程同步功能
    pthread_cond_init( &tasks_cond, NULL ); //初始化条件信号量
    pthread_mutex_init( &tasks_mutex, NULL ); //初始化互斥量
    pthread_t tid1, tid2; //保存两个线程id
    int index1 = 1;
    int ret = pthread_create( &tid1, &attr, say_hello1, ( void* )&index1 );
    if( ret != 0 )
    {
        cout << "pthread_create error:error_code=" << ret << endl;
    }
    int index2 = 2;
    ret = pthread_create( &tid2, &attr, say_hello2, ( void* )&index2 );
    if( ret != 0 )
    {
        cout << "pthread_create error:error_code=" << ret << endl;
    }
    pthread_join( tid1, NULL ); //连接两个线程
    pthread_join( tid2, NULL );

    pthread_attr_destroy( &attr ); //释放内存
    pthread_mutex_destroy( &tasks_mutex ); //注销锁
    pthread_cond_destroy( &tasks_cond ); //正常退出
}
```

测试结果：
先在线程2中执行say_hello2，再跳转到线程1中执行say_hello1，直到tasks减到0为止。
```
[2] hello in thread 1
[2] pthread_cond_signal in thread 1
[3] hello in thread 2
[3] take task: 10 in thread 2
[3] take task: 9 in thread 2
[3] take task: 8 in thread 2
[3] take task: 7 in thread 2
[3] take task: 6 in thread 2
[3] pthread_cond_signal in thread 2
[2] take task: 5 in thread 1
[2] take task: 4 in thread 1
[2] take task: 3 in thread 1
[2] take task: 2 in thread 1
[2] take task: 1 in thread 1
```
## C++ 11中的多线程技术
C++11 新标准中引入了四个头文件来支持多线程编程，他们分别是 `<atomic>` ,`<thread>`,`<mutex>`,`<condition_variable>`和`<future>`。
- `<atomic>`：提供原子操作功能，该头文主要声明了两个类, std::atomic 和 std::atomic_flag，另外还声明了一套 C 风格的原子类型和与 C 兼容的原子操作的函数。
- `<thread>`：线程模型封装，该头文件主要声明了 std::thread 类，另外 std::this_thread 命名空间也在该头文件中。
- `<mutex>`：互斥量封装，该头文件主要声明了与互斥量(mutex)相关的类，包括 std::mutex 系列类，std::lock_guard, std::unique_lock, 以及其他的类型和函数。
- `<condition_variable>`：条件变量，该头文件主要声明了与条件变量相关的类，包括 std::condition_variable 和 std::condition_variable_any。
- `<future>`：实现了对指定数据提供者提供的数据进行异步访问的机制。该头文件主要声明了 std::promise, std::package_task 两个 Provider 类，以及 std::future 和 std::shared_future 两个 Future 类，另外还有一些与之相关的类型和函数，std::async() 函数就声明在此头文件中。

简单示例：
```cpp
#include <iostream>
#include <thread>

using namespace std;

void thread_1()
{
    cout << "hello from thread_1" << endl;
}

int main(int argc, char **argv)
{
    thread t1(thread_1);
    /**
    join()相当于调用了两个函数：WaitForSingleObject、CloseHandle，事实上，在vc12中也是这么实现的
    */
    t1.join();
    return 0;
}
```

## 注意事项

1. 若线程调用到的函数在一个类中，则必须将该函数声明为静态函数函数

因为静态成员函数属于静态全局区，线程可以共享这个区域，故可以各自调用。
```cpp
    #include <iostream>  
    #include <pthread.h>  
      
    using namespace std;  
      
    #define NUM_THREADS 5  
      
    class Hello  
    {  
    public:  
    	//多线程调用，声明为static
        static void* say_hello( void* args )  
        {  
            cout << "hello..." << endl;  
        }  
    };  
      
    int main()  
    {  
        pthread_t tids[NUM_THREADS];  
        for( int i = 0; i < NUM_THREADS; ++i )  
        {  
            int ret = pthread_create( &tids[i], NULL, Hello::say_hello, NULL );  
            if( ret != 0 )  
            {  
                cout << "pthread_create error:error_code" << ret << endl;  
            }  
        }  
        pthread_exit( NULL );  
    }  
```
测试结果：
```
    hello...  
    hello...  
    hello...  
    hello...  
    hello... 
```

2. 代码中如果没有`pthread_join`主线程会很快结束从而使整个进程结束，从而使创建的线程没有机会开始执行就结束了。加入`pthread_join`后，主线程会一直等待直到等待的线程结束自己才结束，使创建的线程有机会执行。

3. 线程创建时属性参数的设置pthread_attr_t及join功能的使用
线程的属性由结构体pthread_attr_t进行管理。
```cpp
typedef struct
{
    int      detachstate;     	//线程的分离状态
    int      schedpolicy;   	//线程调度策略
    struct sched_param   schedparam;   //线程的调度参数
    int inheritsched; 		//线程的继承性 
    int scope; 				//线程的作用域 
    size_t guardsize; 	//线程栈末尾的警戒缓冲区大小 
    int stackaddr_set; 
    void * stackaddr; 	//线程栈的位置 
    size_t stacksize;	// 线程栈的大小
}pthread_attr_t;
```
示例：
```cpp
    #include <iostream>  
    #include <pthread.h>  
      
    using namespace std;  
      
    #define NUM_THREADS 5  
      
    void* say_hello( void* args )  
    {  
        cout << "hello in thread " << *(( int * )args) << endl;  
        int status = 10 + *(( int * )args); //线程退出时添加退出的信息，status供主程序提取该线程的结束信息  
        pthread_exit( ( void* )status );   
    }  
      
    int main()  
    {  
        pthread_t tids[NUM_THREADS];  
        int indexes[NUM_THREADS];  
          
        pthread_attr_t attr; //线程属性结构体，创建线程时加入的参数  
        pthread_attr_init( &attr ); //初始化  
        pthread_attr_setdetachstate( &attr, PTHREAD_CREATE_JOINABLE ); //是设置你想要指定线程属性参数，这个参数表明这个线程是可以join连接的，join功能表示主程序可以等线程结束后再去做某事，实现了主程序和线程同步功能  
        for( int i = 0; i < NUM_THREADS; ++i )  
        {  
            indexes[i] = i;  
            int ret = pthread_create( &tids[i], &attr, say_hello, ( void* )&( indexes[i] ) );  
            if( ret != 0 )  
            {  
            cout << "pthread_create error:error_code=" << ret << endl;  
        }  
        }   
        pthread_attr_destroy( &attr ); //释放内存   
        void *status;  
        for( int i = 0; i < NUM_THREADS; ++i )  
        {  
        int ret = pthread_join( tids[i], &status ); //主程序join每个线程后取得每个线程的退出信息status  
        if( ret != 0 )  
        {  
            cout << "pthread_join error:error_code=" << ret << endl;  
        }  
        else  
        {  
            cout << "pthread_join get status:" << (long)status << endl;  
        }  
        }  
    }  
```
测试结果：
```
hello in thread hello in thread 1hello in thread 3
hello in thread 4
0
hello in thread 2
pthread_join get status:10

pthread_join get status:11
pthread_join get status:12
pthread_join get status:13
pthread_join get status:14
```