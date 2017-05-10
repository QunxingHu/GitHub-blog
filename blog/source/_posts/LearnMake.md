---
title: Learn Makefile
date: 2017-2-13 3:09:22 PM
categories:
- 术业专攻
tags: 
- 学习
- makefile
---
本文主要总结makefile的基本用法及注意事项。内容参考总结自[跟我一起写makefile][1]
<!-- more -->

## 程序的编译和链接过程
### 编译
将源文件编译成中间代码文件，（window下 .obj文件， linux下 .o文件。
编译时，编译器判断语法的正确性，每个源文件都应该对应一个中间目标文件。

### 链接
将object file合成为可执行文件，成为链接link。
链接时，主要是链接函数和全局变量。
大多数时候，由于源文件太多，生产的中间文件太多，而在链接时需要明显地指出中间文件名，所以一般采用对中间目标文件打包，在windows下称为lib文件，在unix下称为.a文件。

### 总结
 <font size=4 color=red>**源文件---(编译)--->目标文件（.o .obj文件）-----(链接)----->可执行文件**</font>
  

## makefile
### 简介
**Makefile**是一种配置文件。一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为 makefile就像一个Shell脚本一样，其中也可以执行操作系统的命令。
**make**是一个命令工具，它解释Makefile 中的指令(应该说是规则)。

make命令执行时，需要一个 Makefile 文件，以告诉make命令需要怎么样的去编译和链接程序。
示例：一个工程含有8个c文件，3个头文件。我们需要编写一个makefile告诉make如何编译和链接这些文件，规则如下：
- 若该工程没有编译过，则所有C文件都会都会被编译并被链接。
- 若其中几个C文件被修改，则只编译修改过的文件
- 若头文件被修改过，则编译引用过头文件的C文件，并链接目标程序。

通过编写makefile,这些都可以只用一个make命令实现。

### makefile规则
**格式**:
```
  target ... : prerequisites ...
  command
   ...
   ...
```
**说明**:
- target: 目标文件，可以是Object File，也可以是执行文件。还可以是一个标签（Label）
- prerequisites: 要生成那个target所需要的文件或是目标,即依赖的文件。
- command： make需要执行的命令。（任意的Shell命令）。 

**target**这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中。说白一点就是说，prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。这就是Makefile的规则。也就是Makefile中最核心的内容。 

targets是文件名，以空格分开，可以使用通配符。一般来说，我们的目标基本上是一个文件，但也有可能是多个文件。

注意： command是命令行，如果其不与“targets:prerequisites”在一行，那么，必须以[Tab键]开头，如果和prerequisites在一行，那么可以用分号做为分隔。

如果命令太长，你可以使用反斜框（‘\’）作为换行符。make对一行上有多少个字符没有限制。规则告诉make两件事，文件的依赖关系和如何生成目标文件。一般来说，make会以UNIX的标准Shell，也就是/bin/sh来执行命令。

**示例分析**
```
edit : main.o kbd.o command.o display.o /              
       insert.o search.o files.o utils.o 
       cc -o edit main.o kbd.o command.o display.o / 
       insert.o search.o files.o utils.o 

main.o : main.c defs.h
		cc -c main.c
kbd.o : kbd.c defs.h command.h
		cc -c kbd.c
command.o : command.c defs.h command.h
		cc -c command.c
display.o : display.c defs.h buffer.h
		cc -c display.c
insert.o : insert.c defs.h buffer.h
		cc -c insert.c
search.o : search.c defs.h buffer.h
		cc -c search.c
files.o : files.c defs.h buffer.h command.h
		cc -c files.c
utils.o : utils.c defs.h
		cc -c utils.c
clean :
		rm edit main.o kbd.o command.o display.o /
		  insert.o search.o files.o utils.o
```
**注：**
- 反斜杠（/）表示换行符。
- 在定义好依赖关系后，后续的那一行定义了如何生成目标文件的操作系统命令，**一定要以一个Tab键作为开头**。
- make并不管命令是怎么工作的，他只管执行所定义的命令。make会比较targets文件和prerequisites文件的修改日期，如果prerequisites文件的日期要比targets文件的日期要新，或者target不存在的话，那么，make就会执行后续定义的命令。
- clean不是一个文件，它只不过是一个动作名字，有点像C语言中的lable一样，其冒号后什么也没有，那么，make就不会自动去找文件的依赖性，也就不会自动执行其后所定义的命令。要执行其后的命令，就要在make命令后明显得指出这个lable的名字。这样的方法非常有用，我们可以在一个makefile中定义不用的编译或是和编译无关的命令，比如程序的打包，程序的备份，等等。 

### make是如何工作的
在默认的方式下，也就是我们只输入make命令。紧接着，下列过程会依次执行
1. make会在当前目录下找名字叫“Makefile”或“makefile”的文件。
2. 如果找到，它会找文件中的第一个目标文件（target），在上面的例子中，他会找到“edit”这个文件，并把这个文件作为最终的目标文件。
3. 如果edit文件不存在，或是edit所依赖的后面的 .o 文件的文件修改时间要比edit这个文件新，那么，他就会执行后面所定义的命令来生成edit这个文件。
4. 如果edit所依赖的.o文件也不存在，那么make会在当前文件中找目标为.o文件的依赖性，如果找到则再根据那一个规则生成.o文件。（这有点像一个堆栈的过程）
5. 当然，你的C文件和H文件是存在的啦，于是make会生成 .o 文件，然后再用 .o 文件生命make的终极任务，也就是执行文件edit了。
    
这就是整个make的依赖性，make会一层又一层地去找文件的依赖关系，直到最终编译出第一个目标文件。在找寻的过程中，如果出现错误，比如最后被依赖的文件找不到，那么make就会直接退出，并报错，而对于所定义的命令的错误，或是编译不成功，make根本不理。make只管文件的依赖性，即，如果在我找了依赖关系之后，冒号后面的文件还是不在，那么对不起，我就不工作啦。
 
通过上述分析，我们知道，像clean这种，没有被第一个目标文件直接或间接关联，那么它后面所定义的命令将不会被自动执行，不过，我们可以显示要make执行。即命令——“make clean”，以此来清除所有的目标文件，以便重编译。
 
于是在我们编程中，如果这个工程已被编译过了，当我们修改了其中一个源文件，比如file.c，那么根据我们的依赖性，我们的目标file.o会被重编译（也就是在这个依性关系后面所定义的命令），于是file.o的文件也是最新的啦，于是file.o的文件修改时间要比edit要新，所以edit也会被重新链接了（详见edit目标文件后定义的命令）。 而如果我们改变了“command.h”，那么，kdb.o、command.o和files.o都会被重编译，并且，edit会被重链接。


## makefile使用变量
我们声明一个变量，叫objects。我们在makefile一开始就这样定义：
```
 objects = main.o kbd.o command.o display.o /
 insert.o search.o files.o utils.o
``` 
于是，我们就可以很方便地在我们的makefile中以“$(objects)”的方式来使用这个变量了，于是我们的改良版makefile就变成下面这个样子：
``` 
objects = main.o kbd.o command.o display.o /
insert.o search.o files.o utils.o
 
edit : $(objects)
cc -o edit $(objects)
main.o : main.c defs.h
cc -c main.c
kbd.o : kbd.c defs.h command.h
cc -c kbd.c
command.o : command.c defs.h command.h
cc -c command.c
display.o : display.c defs.h buffer.h
cc -c display.c
insert.o : insert.c defs.h buffer.h
cc -c insert.c
search.o : search.c defs.h buffer.h
cc -c search.c
files.o : files.c defs.h buffer.h command.h
cc -c files.c
utils.o : utils.c defs.h
cc -c utils.c
clean :
rm edit $(objects)
 ```
于是如果有新的 .o 文件加入，我们只需简单地修改一下 objects 变量就可以了。




## makefile自动推导
只要make看到一个[.o]文件，它就会自动的把[.c]文件加在依赖关系中，如果make找到一个whatever.o，那么whatever.c，就会是whatever.o的依赖文件。并且 cc -c whatever.c 也会被推导出来，于是，我们的makefile再也不用写得这么复杂。
```
objects = main.o kbd.o command.o display.o /
insert.o search.o files.o utils.o
 
edit : $(objects)
cc -o edit $(objects)
 
main.o : defs.h
kbd.o : defs.h command.h
command.o : defs.h command.h
display.o : defs.h buffer.h
insert.o : defs.h buffer.h
search.o : defs.h buffer.h
files.o : defs.h buffer.h command.h
utils.o : defs.h
 
.PHONY : clean
clean :
rm edit $(objects)
```


## 另类风格的makefile
即然我们的make可以自动推导命令，那么我看到那堆[.o]和[.h]的依赖就有点不爽，那么多的重复的[.h]，能不能把其收拢起来，好吧，没有问题，这个对于make来说很容易，谁叫它提供了自动推导命令和文件的功能呢？来看看最新风格的makefile吧。
```
objects = main.o kbd.o command.o display.o /
insert.o search.o files.o utils.o

edit : $(objects)
cc -o edit $(objects)

$(objects) : defs.h
kbd.o command.o files.o : command.h
display.o insert.o search.o files.o : buffer.h

clean :
rm edit $(objects)
```
这种风格，让我们的makefile变得很简单，但我们的文件依赖关系就显得有点凌乱了。鱼和熊掌不可兼得。还看你的喜好了。我是不喜欢这种风格的，一是文件的依赖关系看不清楚，二是如果文件一多，要加入几个新的.o文件，那就理不清楚了。


## 清空目标文件的规则
每个Makefile中都应该写一个清空目标文件（.o和执行文件）的规则，这不仅便于重编译，也很利于保持文件的清洁。一般的风格都是：
```   
clean :
	rm edit $(objects)
```
更为稳健的做法是：
```
 .PHONY : clean
 clean :
 	-rm edit $(objects)
 ```
 H.PHONY意思表示clean是一个“伪目标”，。而在rm命令前面加了一个小减号的意思就是，也许某些文件出现问题，但不要管，继续做后面的事。当然，clean的规则不要放在文件的开头，不然，这就会变成make的默认目标，不成文的规矩是——“clean从来都是放在文件的最后”。


  [1]: https://github.com/seisman/how-to-write-makefile