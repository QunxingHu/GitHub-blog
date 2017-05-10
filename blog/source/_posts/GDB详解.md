---
title: GDB 详解
date: 2017-2-7 5:01:07 PM
categories:
- 术业专攻
tags: 
- GDB
---

GDB(GNU Debugger)是一款功能强大的调试工具，肩负着救死扶伤的重任，是每一位程序员都应该熟练掌握的兵器。下面是我在学习使用GDB过程中所总结的一些资料，涉及GDB的基本使用和基本原理，如有不当之处，欢迎批评指正。
<!-- more -->

## GDB的功能
- **救死扶伤：** 分析排解程序中的 bug
- **庖丁解牛：** 分析程序的运行与结构
- **斗转星移：** 动态改变程序的执行环境，按照自定义的要求运行程序
## GDB的使用
通常情况下，GDB主要用于调试 C/C++ 程序，为了更加方便直观的调试，我们需要在编译 C/C++程序时，使用`-g` 选项，如`gcc -g test.c -o test`,`g++ -g test.cpp -o test`， 如果不使用`-g`选项，程序的函数名、变量名将不可见，取而代之的是它们运行时的地址。

### 启动GDB
- gdb 程序名
  程序名为可执行文件，一般为当前目录下，也可以为包含路径的可执行文件
  
- gdb 程序名 core文件
  程序名为可执行文件，core文件为`core dump`所产生的文件。至于什么是`core文件`,请参考[core dump详解][1]。
  
- gdb 程序名 进程ID
  如果程序为服务程序，则可以通过指定程序运行时的PID，gdb会自动attach并调试，程序名应该存在于PATH环境变量中。
  
### 调试命令
- 查看源码 list(l)
  - `list（或l）` : 列出源代码，接着上次的位置往下列，每次列10行
  - `list 行号`   : 列出从第几行开始的源代码
  - `list 函数名` : 列出某个函数的源代码
  
- 设置断点 break(b)
  - `break 行号`  : 如`break 5` 在第五行处设置断点
  - `break filename:行号` : 如`break main.c:10`　在main.c文件第10行设置断点
  - `break 地址` ： 如`break 0x3400a` 用于在内存0x3400a处设置断点 
  - `break 行号 if 条件` ： 如`break 10 if i==3`　用于设置条件断点，当i==3时暂停  
  - `tbreak 行号或函数名` ：如`tbreak 10`，在第10行设置临时断点，到达后被自动删除，只会暂停一次
  
- 查看断点 info breakpoints（info b）
  - `info break n` 查看n号断点情况
  - `info break` 查看所有断点情况
  
- 运行代码
  - `set args <arg1> <arg2>`: 设置运行参数
  - `show args`: 查看运行参数
  - `run(r)` : 开始运行程序
  - `step(s)`: 单步运行，会进入到子函数中运行
  - `next(n)`: 单步运行，不会进入到子函数中
  - `continue(c)`: 继续执行，执行到下一个断点或程序结束
  - `until(u) 行号`： 运行到行数某一行
  - `return <var>`: 改变程序执行流程，直接结束当前函数，并将指定值返回
  - `call <func>`: 在当前位置执行指定的函数
  - `finish`: 一直运行到函数返回，并答应函数返回时的堆栈地址和返回值及参数值等信息。
  
- 数据命令
  - `p 变量名`: 显示变量值
  - `watch 变量名`: 设置一个观察点，当变量被读出或者写入的时候暂停程序
  - `rwatch 变量名`： 设置一个观察点，当变量被读出时，程序暂停
  - `info watchpoints [n]` : 查看所有观察点/观察点n的情况
  - `display 表达式`: 显示表达式的值，每当程序运行到断点出时都会显示表达式的值。
  - `info dispaly`: 显示所有要显示的表达式的情况
  - `whatis 变量`: 显示某个变量的数据类型
  - `set 变量=变量值`: 改变程序中某个变量的值
  
- 堆栈命令
  - `backtrace(bt)`: 打印帧栈指针，查看程序支持到此时，所有已经调用的函数的列表，包含当前函数。，每个函数及其变量都被分配一个帧，最近调用的函数在0号帧中。
  - `frame 帧号`： 打印指定帧栈
  - `info reg`: 查看寄存器使用情况
  - `info stack`: 查看堆栈使用情况
  - `up/down`： 跳到上一层/下一层函数
  
- 跳转执行（jump）
  - `jump 行号/文件名:行号`：跳转到指定行运行
  
- 信号命令
  - `signal SIGXXX`： 生成xxx信号，`kill -l`查看linux的信号
  - `handle SIGXXX` : 处理信号，一旦被调试的程序接受到信号，运行程序会停住。
  - `info signals` : 查看信号的信息
  - `info handle` : 查看那些信号可以被GDB处理
  `signals`命令和`shell kill`产生的命令不同，系统个kill命令发给调试程序由GDB截获，而`signals`命令所发出的信号则是直接发给调试程序。

- shell命令
  - `shell <命令>`： 运行shell命令
  - `cd 目录` ： 切换目录
  - `pwd` : 显示当前目录

- 程序的输入输出
  - `info terminal` : 显示程序用到的终端模式
  - `run > outfile` : 重定向输出到文件
  - `tty /dev/tty*` : 指定输入输出设备

- 运行环境设定
  - `path xxx` : 设定程序的运行路径
  - `show paths ` : 查看程序的运行路径
  - `set env varname=<val>` : 设置环境变量
  - `show env [varname]` : 查看环境变量

- 清除断点
  - `delete 断点号` ： 清除对应断点
  - `clear 行号` ： 清除对应行号的断点
  - `disable/enable 断点编号` : 让断点失效/生效
  - `disable/enable display 编号` ： 让一个要显示的表达式暂时失效/生效
  
- 停止退出
  - `quit(q)` ： 退出gdb 调试环境

- 反汇编
  - `disassemble <func>` : 反汇编

### 牛刀小试
编译下面的程序，使用gdb进行调试。尝试在不改动源码的情况下，使用GDB使程序正常运行，正常结束，并输出func1~func5 中的输出内容。

```cpp
#include <iostream>
#include <stdio.h>
#include <stdlib.h>

using namespace std;

void func1()
{
	cout << "Nice try. Work harder." << endl;
	return;
}

int func2()
{
	cout << "Um, Great. So what is next." << endl;
	return 2;
}

int func3(int a, int b)
{
	if(a != b)
	{
		cout << "Half way." << endl;
		return 4;
	}
	return 3;
}

void func4(int a)
{
	while(1)
	{
		cout << "One more. One more. Stop me please..." << endl;
		a++;
		if (a >= 10)
		{
			cout << "Game over." << endl;
			exit(-1);
		}
	}
}

int func5(int a, int b, int c)
{
	cout << "Wonderful, Give me five." << endl;
	exit(0);
}

int main()
{
	int i = 0;
	scanf("%d",i); 
	printf("let's begin. %d\n", i);
	int k = i;
	if(k != i)
	{
		func1();
		if(k==2)
		{
			int res2=func2();
			if(res2 == 3)
			{
				int res3 = func3(1,1); 
				if (res3 == 4)
					func4(12);
			}
		}
	}
	cout << "Anyway, you have done well." << endl;
	return 0;
}
```
编译: `g++ gdbPractice.cpp -g -o gdbPractice`
TIPS: 阅读源码，使用gdb改变运行环境，到达目的，使用 bt, set， jump, call，return 等命令到达目的。可以简单粗暴，也可以慢工细活，总之条条大路通罗马，解决此问题的方法有多种，大家可以各显神通。

这段小程序旨在让大家熟悉下gdb的基本命令，逻辑故意混淆了下，实际中gdb的使命更偏向于分析程序的问题并通过修正源码使之正确运行，而不是上述的修改运行环境。

## 多线程调试
在多线程编程时，当我们需要调试时，有时需要控制某些线程停在断点，有些线程继续执行。有时需要控制线程的运行顺序。有时需要中断某个线程，切换到其他线程。这些都可以通过gdb实现。

多线程下常用的GDB命令：
  - `info threads`：显示可以调试的所有线程。gdb会为每个线程分配一个ID（和tid不同），编号一般从1开始。后面的ID是指这个ID。
  - `thread ID`: 切换当前调试的线程为指定ID的线程。
  - `break FileName.cpp:LinuNum thread all`: 所有线程都在文件FileName.cpp的第LineNum行有断点。
  - `thread apply ID1 ID2 IDN command`: 多个线程执行gdb命令command。
  - `thread apply all command`： 所有线程都执行command命令。
  - `set scheduler-locking off|on|step`： 在调式某一个线程时，其他线程是否执行。off，不锁定任何线程，默认值。on，锁定其他线程，只有当前线程执行。step，在step（单步）时，只有被调试线程运行。
  - `set non-stop on/off`: 当调式一个线程时，其他线程是否运行。
  - `set pagination on/off`: 在使用backtrace时，在分页时是否停止。
  - `set target-async on/ff`: 同步和异步。同步，gdb在输出提示符之前等待程序报告一些线程已经终止的信息。而异步的则是直接返回。

示例请参考：[GDB多线程调试][2]

## 多进程调试
在2.5.60版本的Linux内核后，GDB对fork/vfork 创建的子进程提供了调试支持。对于没有亲缘关系的进程，目前并不支持。
对于有亲缘关系的进程，调试方法为：
1. 进入GDB， 如 `$gdb ./test`
2. 设置调试选项，`$set follow-fork-mode [parent|child]`
	- `parent` : fork之后继续调试父进程，子进程不受影响
	- `child` : fork之后调试子进程，父进程不受影响
3. detach选项，`$set detach-on-fork [on|off]`
	- `on`: 断开调试follow-fork-mode中指定的进程，只调试父进程或子进程中的一个，默认模式。
	- `off`: 父子进程都在gdb的控制之下，另一个进程会被设置为暂停状态

   如果设置了set detach-on-fork off且follow-fork-mode为parent，fork后子进程并不运行，而是处于暂停状态。

**Attach 子进程**
众所周知，GDB有附着（attach）到正在运行的进程的功能，即attach <pid>命令。因此我们可以利用该命令attach到子进程然后进行调试。假设调试主进程，fork后，子进程已经不知道运行到何处了，所以这就需要让子进程先暂停一下。暂停的方法有很多
-最简单的就是sleep一下
```cpp
else if(fpid == 0)  {// child process
     sleep(10); // 给你足够的时间去find子进程id（pstree -ap |  grep a.out），然后attach 
}
```  
- 加一段等待代码
```cpp
void debug_wait(char *tag)  
{  
    while(1)  
    {  
        if (tag存在) <span style="color:#ff0000;">// tag可以是一个环境变量，也可以是一个文件等</span>  
            睡眠一段时间;  
        else  
            break;  
    }  
}  
```

使用eclipse调试工具时，需要在在debug选项中开启 **自动调试fork生成的进程** 的选项。

## 远程调试
某些时候由于模拟环境的限制，调试必须要在目标板上进行。由于嵌入式系统资源比较有限，一般不能在目标板上直接构建GDB的调试环境，这时我们通常采用 **gdb+gdbserver**的远程调试方法：**gdbserver在目标板中运行，而gdb则在主机上运行**。

### 安装对应版本的gdb

构建gdb+gdbserver调试环境的在于，**要将gdb和gdbserver都编译成适用于目标板的版本**。比如我们用x86的主机和ARM目标板，平时在主机上直接调试的时候都使用用于x86调试的gdb，但这个gdb不能用于远程调试中，需要针对ARM平台进行配置之后重新编译才行；而gdbserver要运行在目标板上，则需要用arm-linux-gcc编译才行。

我们可以从http://ftp.gnu.org/gnu/gdb/ 或其他站点下载GDB的源代码来进行编译。得到源代码包gdb-6.6.tar.gz之后，将target配置成arm-linux，然后进行编译：
  
```
$ tar xzvf gdb-6.6.tar.gz
$ cd gdb-6.6
$ ./configure --target=arm-linux
$ make
$ make install
```

### 编译目标文件
注意这时我们编译的是用于主机上的gdb程序，因此仍然用x86版本的gcc编译，而不是用arm-gcc。而接下来我们要编译的gdbserver程序则是运行在目标板上的，需要用arm-gcc来编译了，用`CC=<your_arm-linux-gcc_path>`来指定arm-linux-gcc编译器：
```
$ cd gdb/gdbserver/
$ ./configure --target=arm-linux --host=arm-linux
$ make CC=/opt/toolchain/bin/arm-linux-gcc
$ make install
```
得到gdb和gdbserver之后，将gdbserver下载到目标板上就可以进行远程调试了。我们还是以前面用过的overflow程序为例来说明，注意overflow程序也需要重新用arm-linux-gcc编译得到ARM版本的overflow程序，并下载到目标板上。

### 检查gdb以及gdbserver格式
完成这些之后可以用file命令来检查所准备gdb和gdbserver及overflow程序的格式是否正确：
```
$ file arm-linux-gdb
arm-linux-gdb: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), for GNU/Linux 2.4.17, dynamically linked (uses shared libs), not stripped

$ file overflow
overflow: ELF 32-bit LSB executable, ARM, version 1 (SYSV), for GNU/Linux 2.4.17, dynamically linked (uses shared libs), not stripped

$ file gdbserver
gdbserver: ELF 32-bit LSB executable, ARM, version 1 (SYSV), for GNU/Linux 2.4.17, dynamically linked (uses shared libs), not stripped
```
注意确保在目标板上运行的gdbserver及overflow程序被编译成ARM ELF格式，而gdb由于是运行在主机上，还是x86格式的。

### 远程链接GDB
gdb和gdbserver之间可以通过TCP(格式为host:port)、UDP(格式为udp: host:port)或者串口（比如/dev/ttyb）来通信，我们以TCP方式为例来说明。

假设目标板的IP为192.168.2.1，主机为192.168.2.100，使用端口5678来调试
1. 首先在目标板上运行**gdbserver：**

```
# gdbserver 192.168.16.1:5678 ./overflow
Process ./overflow created; pid = 618
Listening on port 5678
```

2. 然后在**主机上运行gdb**，并运行gdb命令`target remote 192.168.2.1:5678`：

```
$ arm-linux-gdb ./overflow
GNU gdb 6.6
Copyright (C) 2006 Free Software Foundation, Inc.
GDB is free software, covered by the GNU General Public License, and you are
welcome to change it and/or distribute copies of it under certain conditions.
Type "show copying" to see the conditions.
There is absolutely no warranty for GDB. Type "show warranty" for details.
This GDB was configured as "--host=i686-pc-linux-gnu --target=arm-linux"...

(gdb) target remote 192.168.2.1:5678
Remote debugging using 192.168.2.1:5678
0x28556080 in ??()

(gdb)
```

接下来你就可以象前面所介绍的那样使用gdb命令了，比如设置断点及查看变量单步执行等。

## GDB的原理
参考：http://www.cnblogs.com/xsln/p/ptrace.html

  [1]: http://blog.csdn.net/tenfyguo/article/details/8159176/
  [2]: http://blog.csdn.net/lhl_blog/article/details/8888010