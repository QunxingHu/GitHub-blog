---
title: CMake入门指南
tags: 
 - CMake
 - C++
categories:
 - 术业专攻
---
“CMake”这个名字是“cross platform make”的缩写。虽然名字中含有“make”，但是CMake和Unix上常见的“make”系统是分开的，而且更为高阶。本篇博客主要介绍cmake的相关知识，从CMakeList.txt的基本语法讲起，总结了cmake一些相关的命令以及如何编译32位，64位程序。
<!-- more -->
##  基本步骤 
1.  新建源文件main.c
2.  新建CMakeLists.txt
      > PROJECT (HELLO)      #项目名称 HELLO
      SET(SRC_LIST main.c)   #源文件 main.c
      ADD_EXECUTABLE(hello ${SRC_LIST})    #生成可执行文件 hello
3.  新建目录build 
	> 目录名可自定义，用于外部构建 (编译所生成的文件都生成在build目录下)
4.  进入build, 依次执行  `cmake ..`  `make`   `./hello` 

## CMakeLists.txt语法
CMakeLists.txt 的语法比较简单,**由命令、注释和空格**组成
命令是**不区分大小写的**。
符号"#"后面的内容被认为是注释。
命令由**命令名称、小括号和参数**组成,参数之间使用空格进行间隔。
> **`set(VAR a b c)`**
    >>  通过**SET**命令建立变量列表
    >> 变量名区分大小写，使用 `${变量名}` 访问变量值

> **`message("VAR = ${VAR}")`**
> >输出 VAR = a;b;c

>**`$ENV{变量名}`**
>>访问环境变量的值（ENV{变量名}表示环境变量本身）
     
>**`message($ENV{PATH})`**
 >>输出环境变量PATH的值

>**`if(expresssion1)   elseif(expression2)   else()    endif()`**
>>条件控制命令
>>对于 if(string) 来说：如果 string 为（不区分大小写）1、ON、YES、TRUE、Y、非 0 的数则表示真；
>>如果 string 为（不区分大小写）0、OFF、NO、FALSE、N、IGNORE、空字符串、以 -NOTFOUND 结尾的字符串则表示假；
>>如果 string 不符合上面两种情况，则 string 被认为是一个变量的名字。变量的值为第二条所述的各值则表示假，否则表示真。

>expression表达式中可以包含操作符，操作符包括：一元操作符，例如：EXISTS、COMMAND、DEFINED 等; 二元操作符，例如：EQUAL、LESS、GREATER、STRLESS、STRGREATER 等; NOT（非操作符）AND（与操作符）、OR（或操作符）。
>操作符优先级：一元操作符 > 二元操作符 > NOT > AND、OR
>常用操作符介绍：
>>`if(NOT expression)`为真的前提是 expression 为假
>>`if(expr1 AND expr2)`为真的前提是 expr1 和 expr2 都为真
>>`if(expr1 OR expr2)`为真的前提是 expr1 或者 expr2 为真
>>`if(COMMAND command-name)`为真的前提是存在 command-name 命令、宏或函数且能够被调用
>>`if(EXISTS name)`为真的前提是存在 name 的文件或者目录（应该使用绝对路径）
>>`if(file1 IS_NEWER_THAN file2)`为真的前提是 file1 比 file2 新或者 file1、file2 中有一个文件不存在（应该使用绝对路径）
>>`if(IS_DIRECTORY directory-name)`为真的前提是 directory-name 表示的是一个目录（应该使用绝对路径）
>>`if(variable|string MATCHES regex)`为真的前提是变量值或者字符串匹配 regex 正则表达式
>>`if(variable|string LESS variable|string)`为真的前提是变量值或者字符串为有效的数字且满足小于的条件
>>`if(variable|string GREATER variable|string)`为真的前提是变量值或者字符串为有效的数字且满足大于的条件
>>`if(variable|string EQUAL variable|string)`为真的前提是变量值或者字符串为有效的数字且满足等于的条件
>>`if(variable|string STRLESS variable|string)`为真的前提是变量值或者字符串以字典序满足小于的条件
>>`if(variable|string STRGREATERvariable|string)`为真的前提是变量值或者字符串以字典序满足大于的条件
>>`if(variable|string STREQUALvariable|string)`为真的前提是变量值或者字符串以字典序满足等于的条件
>>`if(DEFINED variable)`为真的前提是 variable 表示的变量被定义了。

>`foreach 循环范例` 
``` shell
set(VAR a b c)
foreach(f ${VAR})
 message(${f})
endforeach()
```
>`while 循环范例：`
```shell
set(VAR 5)
while(${VAR} GREATER 0)
   message(${VAR})
   math(EXPR VAR "${VAR} - 1")
endwhile()
```
**宏定义**
函数会为变量创建一个局部作用域，而宏则使用全局作用域。范例：
1. 定义一个宏 hello
``` shell
macro(hello MESSAGE)
 message(${MESSAGE})
endmacro()
```
2. 调用宏hello `hello("hello world")`


**函数**
1. 定义一个函数 hello
``` shell
function(hello MESSAGE)
   message(${MESSAGE})
endfunction()
```
2. 函数和宏可以通过命令 return() 返回，但是函数和宏的返回值必须通过参数传递出去。例如：

函数：
``` shell
cmake_minimum_required(VERSION 2.8)
function(get_func RESULT)
    #RESULT 的值为实参的值，因此需要使用 ${RESULT}
    #这里使用 PARENT_SCOPE 是因为函数会构建一个局部作用域
   set(${RESULT} "Hello Function" PARENT_SCOPE)
endfunction()
```
宏定义：
```shell
macro(get_macro RESULT)
   set(${RESULT} "Hello Macro")
endmacro()
```
``` shell
get_func(V1)  # 输出 Hello Function
message(${V1})

get_macro(V2) # 输出 Hello Macro
message(${V2})
```

字符串相关
字符串可跨行且支持转移字符，例如：
``` shell
set(VAR "hello
world")# 输出结果为：
# ${VAR} = hello
# world
message("\${VAR} = ${VAR}")
```
## Cmake常用命令
1. project 命令
命令语法：project(<projectname> [languageName1 languageName2 … ] )
命令简述：用于指定项目的名称
使用范例：project(Main)

2. cmake_minimum_required命令
命令语法：cmake_minimum_required(VERSION major[.minor[.patch[.tweak]]][FATAL_ERROR])
命令简述：用于指定需要的 CMake 的最低版本
使用范例：cmake_minimum_required(VERSION 2.8)

3. aux_source_directory命令
命令语法：aux_source_directory(<dir> <variable>)
命令简述：用于将 dir 目录下的所有源文件的名字保存在变量 variable 中
使用范例：aux_source_directory(. DIR_SRCS)

4. add_executable 命令
命令语法：add_executable(<name> [WIN32] [MACOSX_BUNDLE][EXCLUDE_FROM_ALL] source1 source2 … sourceN)
命令简述：用于指定从一组源文件 source1 source2 … sourceN 编译出一个可执行文件且命名为 name
使用范例：add_executable(Main ${DIR_SRCS})

5. add_library 命令
命令语法：add_library([STATIC | SHARED | MODULE] [EXCLUDE_FROM_ALL] source1source2 … sourceN)
命令简述：用于指定从一组源文件 source1 source2 … sourceN 编译出一个库文件且命名为 name
使用范例：add_library(Lib ${DIR_SRCS})

6. add_dependencies 命令
命令语法：add_dependencies(target-name depend-target1 depend-target2 …)
命令简述：用于指定某个目标（可执行文件或者库文件）依赖于其他的目标。这里的目标必须是 add_executable、add_library、add_custom_target 命令创建的目标

7. add_subdirectory 命令
命令语法：add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
命令简述：用于添加一个需要进行构建的子目录
使用范例：add_subdirectory(Lib)

8. target_link_libraries命令
命令语法：target_link_libraries(<target> [item1 [item2 […]]][[debug|optimized|general] ] …)
命令简述：用于指定 target 需要链接 item1 item2 …。这里 target 必须已经被创建，链接的 item 可以是已经存在的 target（依赖关系会自动添加）
使用范例：target_link_libraries(Main Lib)

9. set 命令
命令语法：set(<variable> <value> [[CACHE <type><docstring> [FORCE]] | PARENT_SCOPE])
命令简述：用于设定变量 variable 的值为 value。如果指定了 CACHE 变量将被放入 Cache（缓存）中。
使用范例：set(ProjectName Main)

10. unset 命令
命令语法：unset(<variable> [CACHE])
命令简述：用于移除变量 variable。如果指定了 CACHE 变量将被从 Cache 中移除。
使用范例：unset(VAR CACHE)

11. message 命令
命令语法：message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] “message todisplay”…)
命令简述：用于输出信息
使用范例：message(“Hello World”)

12. include_directories 命令
命令语法：include_directories([AFTER|BEFORE] [SYSTEM] dir1 dir2 …)
命令简述：用于设定目录，这些设定的目录将被编译器用来查找 include 文件
使用范例：include_directories(${PROJECT_SOURCE_DIR}/lib)

13. find_path 命令
命令语法：find_path(<VAR> name1 [path1 path2 …])
命令简述：用于查找包含文件 name1 的路径，如果找到则将路径保存在 VAR 中（此路径为一个绝对路径），如果没有找到则结果为 <VAR>-NOTFOUND。默认的情况下，VAR 会被保存在 Cache 中，这时候我们需要清除 VAR 才可以进行下一次查询（使用 unset 命令）。
使用范例：
find_path(LUA_INCLUDE_PATH lua.h${LUA_INCLUDE_FIND_PATH})
if(NOT LUA_INCLUDE_PATH)
   message(SEND_ERROR "Header file lua.h not found")
endif()

14. find_library 命令
命令语法：find_library(<VAR> name1 [path1 path2 …])
命令简述：用于查找库文件 name1 的路径，如果找到则将路径保存在 VAR 中（此路径为一个绝对路径），如果没有找到则结果为 <VAR>-NOTFOUND。一个类似的命令 link_directories 已经不太建议使用了

15. add_definitions 命令
命令语法：add_definitions(-DFOO -DBAR …)
命令简述：用于添加编译器命令行标志（选项），通常的情况下我们使用其来添加预处理器定义
使用范例：add_definitions(-D_UNICODE -DUNICODE)

16. execute_process 命令
命令语法：
execute_process(COMMAND <cmd1>[args1...]]
                  [COMMAND <cmd2>[args2...] [...]]
                  [WORKING_DIRECTORY<directory>]
                  [TIMEOUT <seconds>]
                  [RESULT_VARIABLE<variable>]
                  [OUTPUT_VARIABLE<variable>]
                  [ERROR_VARIABLE<variable>]
                  [INPUT_FILE <file>]
                  [OUTPUT_FILE <file>]
                  [ERROR_FILE <file>]
                  [OUTPUT_QUIET]
                  [ERROR_QUIET]
                 [OUTPUT_STRIP_TRAILING_WHITESPACE]
                 [ERROR_STRIP_TRAILING_WHITESPACE])
命令简述：用于执行一个或者多个外部命令。每一个命令的标准输出通过管道转为下一个命令的标准输入。WORKING_DIRECTORY 用于指定外部命令的工作目录，RESULT_VARIABLE 用于指定一个变量保存外部命令执行的结果，这个结果可能是最后一个执行的外部命令的退出码或者是一个描述错误条件的字符串，OUTPUT_VARIABLE 或者 ERROR_VARIABLE 用于指定一个变量保存标准输出或者标准错误，OUTPUT_QUIET 或者 ERROR_QUIET 用于忽略标准输出和标准错误。
使用范例：execute_process(COMMAND ls)

17. file 命令

命令简述：此命令提供了丰富的文件和目录的相关操作（这里仅说一下比较常用的）
使用范例：
``` shell
# 目录的遍历
# GLOB 用于产生一个文件（目录）路径列表并保存在variable 中
# 文件路径列表中的每个文件的文件名都能匹配globbing expressions（非正则表达式，但是类似）
# 如果指定了 RELATIVE 路径，那么返回的文件路径列表中的路径为相对于 RELATIVE 的路径
# file(GLOB variable [RELATIVE path][globbing expressions]...)
# 获取当前目录下的所有的文件（目录）的路径并保存到 ALL_FILE_PATH 变量中
file(GLOB ALL_FILE_PATH ./*)
# 获取当前目录下的 .h 文件的文件名并保存到ALL_H_FILE 变量中
# 这里的变量CMAKE_CURRENT_LIST_DIR 表示正在处理的 CMakeLists.txt 文件的所在的目录的绝对路径（2.8.3 以及以后版本才支持）
file(GLOB ALL_H_FILE RELATIVE${CMAKE_CURRENT_LIST_DIR} ${CMAKE_CURRENT_LIST_DIR}/*.h)
```
## 构建类型
CMake 为我们提供了四种构建类型：
> Debug
Release
MinSizeRel
RelWithDebInfo

如果使用 CMake 为 Windows MSVC 生成 projects/workspaces 那么我们将得到上述的 4 种解决方案配置。
如果使用 CMake 生成 Makefile 时，我们需要做一些不同的工作。CMake 中存在一个变量CMAKE_BUILD_TYPE 用于指定构建类型，此变量只用于基于 make 的生成器。我们可以这样指定构建类型：
`$ CMake -DCMAKE_BUILD_TYPE=Debug`
这里的 CMAKE_BUILD_TYPE 的值为上述的 4 种构建类型中的一种。

## 编译和链接标志
C 编译标志相关变量：
CMAKE_C_FLAGS
CMAKE_C_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]

C++ 编译标志相关变量：
CMAKE_CXX_FLAGS
CMAKE_CXX_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]

CMAKE_C_FLAGS 或CMAKE_CXX_FLAGS 可以指定编译标志
CMAKE_C_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]或 CMAKE_CXX_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO] 则指定特定构建类型的编译标志，这些编译标志将被加入到 CMAKE_C_FLAGS 或 CMAKE_CXX_FLAGS 中去，例如，如果构建类型为 DEBUG，那么 CMAKE_CXX_FLAGS_DEBUG 将被加入到 CMAKE_CXX_FLAGS中去


链接标志相关变量：
CMAKE_EXE_LINKER_FLAGS
CMAKE_EXE_LINKER_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]
CMAKE_MODULE_LINKER_FLAGS
CMAKE_MODULE_LINKER_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]
CMAKE_SHARED_LINKER_FLAGS
CMAKE_SHARED_LINKER_FLAGS_[DEBUG|RELEASE|MINSIZEREL|RELWITHDEBINFO]
它们类似于编译标志相关变量

## 生成Debug和Release版本
在 Visual Studio 中我们可以生成 debug 版和 release 版的程序,使用 CMake 我们也可以达到上述效果。debug 版的项目生成的可执行文件需要有调试信息并且不需要进行优化,而 release 版的不需要调试信息但需要优化。这些特性在 gcc/g++ 中是通过编译时的参数来决定的,如果将优化程度调到最高需要设置参数-O3,最低是 -O0 即不做优化;添加调试信息的参数是 -g -ggdb ,如果不添加这个参数,调试信息就不会被包含在生成的二进制文件中。

CMake 中有一个变量CMAKE_BUILD_TYPE ,可以的取值是 Debug、Release、RelWithDebInfo 和 MinSizeRel。当这个变量值为 Debug 的时候,CMake 会使用变量 CMAKE_CXX_FLAGS_DEBUG 和 CMAKE_C_FLAGS_DEBUG中的字符串作为编译选项生成Makefile ,当这个变量值为 Release 的时候,工程会使用变量 CMAKE_CXX_FLAGS_RELEASE 和CMAKE_C_FLAGS_RELEASE 选项生成 Makefile。

示例：
```shell
PROJECT(main)
CMAKE_MINIMUM_REQUIRED(VERSION 2.6)
SET(CMAKE_SOURCE_DIR .)
SET(CMAKE_CXX_FLAGS_DEBUG"$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
SET(CMAKE_CXX_FLAGS_RELEASE"$ENV{CXXFLAGS} -O3 -Wall")
AUX_SOURCE_DIRECTORY(. DIR_SRCS)
ADD_EXECUTABLE(main ${DIR_SRCS})
```
第 5 和 6 行设置了两个变量 CMAKE_CXX_FLAGS_DEBUG 和CMAKE_CXX_FLAGS_RELEASE, 这两个变量是分别用于 debug 和 release 的编译选项。编辑 CMakeList.txt 后需要执行 ccmake 命令生成 Makefile 。在进入项目的根目录,输入 "ccmake ." 进入一个图形化界面。

## 编译32位和64位程序
对于 Windows MSVC，我们可以设定 CMake Generator 来确定生成 Win32 还是 Win64 工程文件，例如：
```shell
# 用于生成 Visual Studio 10Win64 工程文件
CMake -G "Visual Studio 10 Win64"
# 用于生成 Visual Studio 10Win32 工程文件
CMake -G "Visual Studio 10"
```
我们可以通过 CMake --help 来查看当前平台可用的 Generator。
```shell
CMake .. -DUSE_32BITS=1
if(USE_32BITS)
 message(STATUS "Using 32bits")
 set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}-m32")
 set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}-m32")
else()
endif(USE_32BITS)
```
对于 UNIX 和类 UNIX 平台，我们可以通过编译器标志（选项）来控制进行 32 位还是 64 位构建。
GCC命令行参数
32位版：加上 -m32 参数，生成32位的代码。
64位版：加上 -m64 参数，生成64位的代码。
debug版：加上 -g 参数，生成调试信息。
release版：加上 -static 参数，进行静态链接，使程序不再依赖动态库。加上 -O3 参数，进行最快速度优化。加上-DNDEBUG参数，定义NDEBUG宏，屏蔽断言。
当没有-m32或-m64参数时，一般情况下会生成跟操作系统位数一致的代码，但某些编译器存在例外，例如
32位Linux下的GCC，默认是编译为32位代码。
64位Linux下的GCC，默认是编译为64位代码。
Window系统下的MinGW，总是编译为32位代码。因为MinGW只支持32位代码。
Window系统下的MinGW-w64（例如安装了TDM-GCC，选择MinGW-w64），默认是编译为64位代码，包括在32位的Windows系统下。

Makefile文件中的示例：
```shell
# [args] 生成模式. 0代表debug模式, 1代表release模式. makeRELEASE=1.
ifeq ($(RELEASE),0)
   CFLAGS += -g
else
    #release
   CFLAGS += -static -O3 -DNDEBUG
   LFLAGS += -static
endif
# [args] 程序位数. 32代表32位程序, 64代表64位程序, 其他默认. makeBITS=32.
ifeq ($(BITS),32)
   CFLAGS += -m32
   LFLAGS += -m32
else
    ifeq($(BITS),64)
       CFLAGS += -m64
       LFLAGS += -m64
   else
   endif
endif


## 多源文件目录的处理方式
我们在每一个源码目录中都会放置一个 CMakeLists.txt 文件。我们现在假定有这么一个工程：
HelloWorld
|
+------- Main.cpp
|
+------- CMakeLists.txt
|
+------- Lib
        |
        +------- Lib.cpp
        |
        +------- Lib.h
        |
        +------- CMakeLists.txt

这里 Lib 目录下的文件将被编译为一个库。首先，我们看一下 Lib 目录下的 CMakeLists.txt 文件：
``` shell
aux_source_directory(. DIR_SRCS)
add_library(Lib ${DIR_SRCS})
```
然后，看一下 HelloWorld 目录下的 CMakeLists.txt 文件：
```shell
project(Main)
cmake_minimum_required(VERSION 2.8)
add_subdirectory(Lib)
aux_source_directory(. DIR_SRCS)
add_executable(Main ${DIR_SRCS})
target_link_libraries(Main Lib)
```
这里使用了 add_subdirectory 指定了需要进行构建的子目录，并且使用了 target_link_libraries 命令，表示 Main 可执行文件需要链接 Lib库。我们执行 CMake . 命令，首先会执行 HelloWorld 目录下的 CMakeLists.txt 中的命令，当执行到 add_subdirectory(Lib) 命令的时候会进入 Lib 子目录并执行其中的CMakeLists.txt 文件。

## 外部构建（out of source builds）
我们在 CMakeLists.txt 所在目录下执行 CMake . 会生成大量的文件，这些文件和我们的源文件混在一起不好管理，我们采用外部构建的方式来解决这个问题。以上面的 Hello World 工程来做解释：
在 HelloWorld 目录下建立一个build 目录（build目录可以建立在如何地方）
进入 build 目录并进行外部构建 CMake ..（语法为 CMake <CMakeLists.txt 的路径>，这里使用 CMake.. 表明了 CMakeLists.txt 在 Build 目录的父目录中）。这样 CMake 将在 Build 目录下生成文件。
