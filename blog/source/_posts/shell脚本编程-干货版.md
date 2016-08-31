---
title: shell脚本编程
tags: 
  - shell
  - linux
---
## 概念
**shell** ： 是一个用C语言编写的程序，它是用户使用Linux的桥梁。Shell既是一种命令语言，又是一种程序设计语言。

**shell 脚本**： Shell脚本（英语：Shell script）是一种电脑程序与文本文件，内容由一连串的shell命令组成，经由Unix Shell直译其内容后运作。被当成是一种脚本语言来设计，其运作方式与直译语言相当，由Unix shell扮演命令行解释器的角色，在读取shell script之后，依序运行其中的shell命令，之后输出结果。利用Shell script可以进行系统管理，文件操作等。
<!-- more -->
**shell 环境**：shell编程跟java、php编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。当前主流的操作系统都支持shell编程，本文档所述的shell编程是指Linux下的shell，讲的基本都是POSIX标准下的功能，所以，也适用于Unix及BSD（如Mac OS）。
Linux：Linux默认安装就带了shell解释器。
Mac OS：Mac OS不仅带了sh、bash这两个最基础的解释器，还内置了ksh、csh、zsh等不常用的解释器。
Windows上的模拟器：windows出厂时没有内置shell解释器，需要自行安装，为了同时能用grep, awk, curl等工具，最好装一个cygwin或者mingw来模拟linux环境。windows10周年纪念版集合了bash。

**脚本解释器：**
sh : 即Bourne shell，POSIX（Portable Operating System Interface）标准的shell解释器，它的二进制文件路径通常是/bin/sh，由Bell Labs开发。
bash : Bash是Bourne shell的替代品，属GNU Project，二进制文件路径通常是/bin/bash。业界通常混用bash、sh、和shell.
在Mac OS上，/bin/sh和/bin/bash是两个不同的文件，尽管它们的大小只相差100字节左右.

**shell 编程语言：** shell只定义了一个非常简单的编程语言，所以，如果你的脚本程序复杂度较高，或者要操作的数据结构比较复杂，那么还是应该使用Python、Perl这样的脚本语言，或者是你本来就已经很擅长的高级语言。因为sh和bash在这方面很弱，比如说：
 - 它的函数只能返回字串，无法返回数组
 - 它不支持面向对象，你无法实现一些优雅的设计模式
 - 它是解释型的，一边解释一边执行，连PHP那种预编译都不是，如果你的脚本包含错误(例如调用了不存在的函数)，只要没执行到这一行，就不会报错

**环境兼容性：** 如果你的脚本是提供给别的用户使用，使用sh或者bash，你的脚本将具有最好的环境兼容性，perl很早就是linux标配了，python这些年也成了一些linux发行版的标配，至于mac os，它默认安装了perl、python、ruby、php、java等主流编程语言。

**shell脚本流**：shell脚本语言就跟和几个人聊天类似。你只需把所有命令想象成能帮你做事的那些人，只要你用正确的方式来请求他们去做。比如说，你想要写文档。首先，你需要纸。然后，你需要把内容说给某个人听，让他帮你写。最后，你想要把它存放到某个地方。或者说，你想要造一所房子，因而你需要请合适的人来清空场地。在他们说"事情干完了"，那么另外一些工程师就可以帮你来砌墙。最后，当这些工程师们也告诉你"事情干完了"的时候，你就可以叫油漆工来给房子粉饰了。如果你让油漆工在墙砌好前就来粉饰，会发生什么呢？我想，他们会开始发牢骚了。几乎所有这些像人一样的命令都会说话，如果它们完成了工作而没有发生什么问题，那么它们就会告诉"标准输出"。如果它们不能做你叫它们做的事——它们会告诉"标准错误"。这样，最后，所有的命令都通过"标准输入"来听你的话。

## 变量
```bash?linenums
your_name="your_value"
echo $your_name
echo ${your_name}
myUrl="http://www.w3cschool.cc"
readonly myUrl
read your_name
unset your_name
```
注意：
- 首字母为（a～z, A~Z）
- **变量名和等号之间不能有空格**
- 不能使用bash中的关键字
- 可以使用下划线，不能使用空格
- 变量外的花括号可选，建议加上，用于帮助解释器识别变量边界
- 使用readonly命令将变量定义为只读变量
- 使用unset命令删除变量，删除后变量不能再次使用，不能删除只读变量。
- 变量类型：
  - 局部变量： 在当前脚本或命令中定义，仅对当前shell实例中有效
  - 环境变量： 所有的程序，包括shell启动的程序都能访问环境变量
  - shell变量： 由shell程序设置的特殊变量。shell变量中由一部分是局部变量一部分时环境变量，这些变量保证了shell的正常运行。

## 字符串
  - 单引号：`str='this is a string'` 
    -  单引号里的任何字符都会**原样输出** ，单引号字符串中的**变量是无效**的
    -  单引号字串中**不能出现单引号**（对单引号使用转义符后也不行）。
  -  双引号： `str="hello,${your_name}"`
      -  双引号可以有变量
      -  双引号可以出现转义字符 

字符串拼接：
```bash?linenums
your_name="hi"
greeting=""${your_name}", jack !"
```
获取字符串长度：
```bash?linenums
str=”abcd“
echo ${#str} #输出长度为4
```
提取子字符串
```bash?linenums
str="rubys is cool"
echo ${str:1:4} #输出ubys
```
查找子字符串
```bash?linenums
str="just do it!"
echo `expr index "${str}" do` #输出字符d或o的位置：6
```
## 数组
数组中可以存放多个值。Bash Shell **只支持一维数组**（不支持多维数组），初始化时不需要定义数组大小（与 PHP 类似）。与大部分编程语言类似，数组元素的下标由0开始。
定义：
`array_name=(value0 value1 value2 .... valuen)`
或者
```bash?linenums
array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen
echo ${array_name[@]}   #输出数组中所有元素
echo ${array_name[index]} #输出指定下标的数组元素
length=${#array_name[@]}  #获取数组的长度，也可使用（length=${#array_name[*]}）
lengthn=${#array_name[n]} #获取数组中
```
## 参数
在执行shell脚本时，可以向shell脚本传递参数，脚本内获取参数的格式为：`$n` n 为参数的序号，从0开始，第0个参数为执行的文件名
```bash?linenums
#!/bin/bash
echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```
输出结果为
```
$ ./test.sh 1 2 3
Shell 传递参数实例！
执行的文件名：test.sh
第一个参数为：1
第二个参数为：2
第三个参数为：3
```
**处理参数的特殊字符**

| 参数| 说明  |
|-------------|-------------|
| $#    | 传递到脚本的参数个数|
| $\*    | 以一个单字符串显示所有向脚本传递的参数。如"$\*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。|
|$@   |与$\*相同，但是使用时加引号，并在引号中返回每个参数。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数
|$$ | 脚本运行的当前进程的ID号|
|$!| 后台运行的最后一个进程的ID号|
|$-|  显示Shell使用的当前选项，与set命令功能相同。|
|$?|  显示最后命令的退出状态。0表示没有错误，其他任何值表明有错误。|

`$*` 与 `$@` 区别：
- 相同点：都是引用所有参数。
- 不同点：只有在双引号中体现出来。假设在脚本运行时写了三个参数 1、2、3，，则 " * " 等价于 "1 2 3"（传递了一个参数），而 "@" 等价于 "1" "2" "3"（传递了三个参数）。

## 基本运算符
Shell 和其他编程语言一样，支持多种运算符，包括：
- 算数运算符
- 关系运算符
- 布尔运算符
- 字符串运算符
- 文件测试运算符

原生bash不支持简单的数学运算，但是可以通过其他命令来实现，例如 awk 和 expr，expr 最常用。
expr 是一款表达式计算工具，使用它能完成表达式的求值操作。
```bash?linenums
#!/bin/bash
val=`expr 2 + 2`  #注意使用的是反引号`，而不是单引号‘
echo "2 + 2 = ${val}"
```
注意：
- 表达式和运算符之间要有空格，例如 2+2 是不对的，必须写成 2 + 2，这与我们熟悉的大多数编程语言不一样。
- 完整的表达式要被 \` \` 包含，注意这个字符不是常用的单引号，在 Esc 键下边。

### 算术运算符
(假定变量 a 为 10，变量 b 为 20)

|运算符  |说明   |举例 |
| ----------- |------------|--------|
|+  |   加法 |  \`expr $a + $b/=\` 结果为 30。|
|-  |减法|  \`expr $a - $b\` 结果为 10。|
|* |  乘法 |  \`expr $a \\* $b\` 结果为  200。
|/ |  除法  |\`expr $b / $a\` 结果为 2。
|%|   取余 |  \`expr $b % $a\` 结果为 0。
|=|   赋值 |  a=$b 将把变量 b 的值赋给 a。
|==|  相等。用于比较两个数字，相同则返回 true。|  [ $a == $b ] 返回 false。
|!=|  不相等。用于比较两个数字，不相同则返回 true。|  [ $a != $b ] 返回 true。
注意：
- 条件表达式要放在方括号之间，并且要有空格，例如: [$a==$b] 是错误的，必须写成 [ $a == $b ]。
- 乘号(*)前边必须加反斜杠(\)才能实现乘法运算；

### 关系运算符
 关系运算符**只支持数字**，不支持字符串，除非字符串的值是数字。(假定变量 a 为 10，变量 b 为 20)

|运算符 |  说明 |  举例|
|----------|--------|----------|
|-eq  |检测两个数是否相等，相等返回 true。 | [ $a -eq $b ] 返回 false。
|-ne  |检测两个数是否相等，不相等返回 true。|   [ $a -ne $b ] 返回 true。
|-gt  |检测左边的数是否大于右边的，如果是，则返回 true。 |  [ $a -gt $b ] 返回 false。
|-lt  |检测左边的数是否小于右边的，如果是，则返回 true。 |  [ $a -lt $b ] 返回 true。
|-ge  |检测左边的数是否大等于右边的，如果是，则返回 true。 | [ $a -ge $b ] 返回 false。
|-le  |检测左边的数是否小于等于右边的，如果是，则返回 true。|   [ $a -le $b ] 返回 true。

### 布尔运算符
(假定变量 a 为 10，变量 b 为 20：)

|运算符|   说明 |  举例|
|---------|----------|--------|
|!  |非运算，表达式为 true 则返回 false，否则返回 true。|  [ ! false ] 返回 true。|
|-o | 或运算，有一个表达式为 true 则返回 true。  |[ $a -lt 20 -o $b -gt 100 ] 返回 true。|
|-a | 与运算，两个表达式都为 true 才返回 true。 |  [ $a -lt 20 -a $b -gt 100 ] 返回 false。|

### 逻辑运算符
(假定变量 a 为 10，变量 b 为 20)

|运算符 |  说明 |  举例|
|---------|--------|---------|
|&&  | 逻辑的 AND  | [ [ \$a -lt 100 && \$b -gt 100 ] ] 返回 false|
| &#124;&#124;  | 逻辑的 OR | [ [ \$a -lt 100 &#124;&#124; \$b -gt 100 ] ] 返回 true|

### 字符串运算符
（假设变量a为”abc“, 变量b为”efg“）

|运算符|   说明|   举例|
|--------|---------|-----------|
|=      |检测两个字符串是否相等，相等返回 true。   |[ $a = $b ] 返回 false。
|!=   |检测两个字符串是否相等，不相等返回 true。 |  [ $a != $b ] 返回 true。
|-z   |检测字符串长度是否为0，为0返回 true。   |[ -z $a ] 返回 false。
|-n   |检测字符串长度是否为0，不为0返回 true。 |  [ -n $a ] 返回 true。
|str  |检测字符串是否为空，不为空返回 true。  |[ $a ] 返回 true。

### 文件测试运算符
文件测试运算符用于检测 Unix 文件的各种属性。

|操作符  |说明   |举例|
|------------|----------|------|
|-b file  |检测文件是否是块设备文件，如果是，则返回 true。   |[ -b $file ] 返回 false。
|-c file  |检测文件是否是字符设备文件，如果是，则返回 true。  |[ -c $file ] 返回 false。
|-d file  |检测文件是否是目录，如果是，则返回 true。 |  [ -d $file ] 返回 false。
|-f file  |检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。|  [ -f $file ] 返回 true。|
|-g file  |检测文件是否设置了 SGID 位，如果是，则返回 true。 | [ -g $file ] 返回 false。
|-k file  |检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。 | [ -k $file ] 返回 false。
|-p file  |检测文件是否是具名管道，如果是，则返回 true。 |  [ -p $file ] 返回 false。
|-u file  |检测文件是否设置了 SUID 位，如果是，则返回 true。   |[ -u $file ] 返回 false。
|-r file  |检测文件是否可读，如果是，则返回 true。 | [ -r $file ] 返回 true。
|-w file  |检测文件是否可写，如果是，则返回 true。 | [ -w $file ] 返回 true。
|-x file  |检测文件是否可执行，如果是，则返回 true。|   [ -x $file ] 返回 true。
|-s file  |检测文件是否为空（文件大小是否大于0），不为空返回 true。 |  [ -s $file ] 返回 true。
|-e file  |检测文件（包括目录）是否存在，如果是，则返回 true。 | [ -e $file ] 返回 true。

## 输入输出重定向
当bash无法执行你的命令时，回通过命令行输出提示信息。在bash中每个留都有一个自己的文件句柄号（流索引号）
- 标准输入（stdin）： 0
- 标准输出（stdout）：1
- 标准错误（stderr）： 2
我们可以通过重定向流带到文件（`<命令> 流索引号> 文件名`），如`command 1> output.txt 2> error.txt`
可以使用">>"替代">"。它允许我们附加到文件，而不是覆盖文件。
也可以重定向一个流到另一个流`command 1>out_error,txt 2>&1`
管道重定向：我们可以使用管道重定向，将一个命令的输出流重定向到另一个命令的输入流，管道重定向总是从左至右的。

**重定向命令列表：**

|命令   |说明|
|----------|------|
|command > file | 将输出重定向到 file。|
|command < file | 将输入重定向到 file。
|command >> file  |将输出以追加的方式重定向到 file。
|n > file   |将文件描述符为 n 的文件重定向到 file。
|n >> file  |将文件描述符为 n 的文件以追加的方式重定向到 file。
|n >& m   |将输出文件 m 和 n 合并。
|n <& m   |将输入文件 m 和 n 合并。
|<< tag   |将开始标记 tag 和结束标记 tag 之间的内容作为输入。

`command1 < infile > outfile`：同时替换输入和输出，执行command1，从文件infile读取内容，然后将输出写入到outfile中。
`command > file 2>&1`：将 stdout 和 stderr 合并后重定向到 file，可以这样写

### Here Document
 Here Document 是 Shell 中的一种特殊的重定向方式，用来将输入重定向到一个交互式 Shell 脚本或程序。
它的基本的形式如下：
```bash?linenums
command << delimiter
    document
delimiter
```
它的作用是将两个 delimiter 之间的内容(document) 作为输入传递给 command。
**注意：**
- 结尾的delimiter 一定要顶格写，前面不能有任何字符，后面也不能有任何字符，包括空格和 tab 缩进。
- 开始的delimiter前后的空格会被忽略掉。
```bash?linenums
wc -l << EOF
    欢迎来到
    菜鸟教程
    www.runoob.com
EOF

3          # 输出结果为 3 行
```
### dev/null 文件
如果希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null：
`$ command > /dev/null`
**说明：**
- /dev/null 是一个特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。
- /dev/null 文件非常有用，将命令的输出重定向到它，会起到 **"禁止输出"** 的效果。
- 如果希望屏蔽 stdout 和 stderr，可以这样写：`$ command > /dev/null 2>&1`

## 读取输入
```bash?linenums
#!/bin/bash
now=`date "+%Y.%m.%d %H:%M:%S"`
echo "name is ?"
read name
echo "phone number ?"
read phone
echo "info: name is $name, phone number is $phone"
echo "[$now] name:$name, tel: $phone">>data.txt
```
bash shell中使用 “$" 符号表示变脸，使用read从标准输入读取值, 使用重定向`<<`输出信息到文件，使用`date "+%Y.%m.%d %H:%M:%S"` 命令获取指定格式的时间。

## 循环
```bash?linenums
#!/bin/bash
while true
do
  now=`date "+%Y.%m.%d %H:%M:%S"`
  read -p "name is ?" name
  read -p "phone number ?" number
  echo "info: name is $name, phone number is $phone"
  echo "[$now] name:$name, tel: $phone">>data.txt
done
```
read时使用‘-p’选项输出提示信息
使用ctrl+c退出循环

## 输出
### echo命令
1. 显示普通字符 `echo hello world`   ==> hello world
2. 显示转义字符 `echo "\"hi\""`          ==> "hi"
3. 显示变量 `echo $var`
4. 显示换行 `echo -e "OK! \n`    #-e 开启转义 
5. 显示不换行 `echo -e "OK! \c "`  \c不换行
5. 显示原字符（单引号） `echo ‘$name\"’`   ==> $name\"
6. 显示命令执行结果(反引号) ``echo `date` ``

### printf
**语法：**`printf format-string [arguments...]`
实例：
```bash?linenums
#!/bin/bash
printf "%-10s %-8s %-4s\n" 姓名 性别 体重kg  
printf "%-10s %-8s %-4.2f\n" 郭靖 男 66.1234 
printf "%-10s %-8s %-4.2f\n" 杨过 男 48.6543 
printf "%-10s %-8s %-4.2f\n" 郭芙 女 47.9876 
```
输出结果：
```bash
姓名     性别   体重kg
郭靖     男      66.12
杨过     男      48.65
郭芙     女      47.99
```
**说明：**
 %s %c %d %f都是格式替代符
%-10s 指一个宽度为10个字符（-表示左对齐，没有则表示右对齐），任何字符都会被显示在10个字符宽的字符内，如果不足则自动以空格填充，超过也会将内容全部显示出来。
%-4.2f 指格式化为小数，其中.2指保留2位小数。

**printf的转义序列**

|序列 |说明|
|------|------|
|\a |警告字符，通常为ASCII的BEL字符|
|\b |后退|
|\c |抑制（不显示）输出结果中任何结尾的换行字符（只在%b格式指示符控制下的参数字符串中有效），而且，任何留在参数里的字符、任何接下来的参数以及任何留在格式字符串中的字符，都被忽略|
|\f |换页（formfeed）|
|\n   |换行
|\r |回车（Carriage return）
|\t |水平制表符
|\v |垂直制表符
|\\ |一个字面上的反斜杠字符
|\ddd   |表示1到3位数八进制值的字符。仅在格式字符串中有效|
|\0ddd  |表示1到3位的八进制值字符|

## test命令
shell中的test命令用于检查某个条件是否成立，它可以进行数值，字符和文件三个方面的测试。
### 数值测试： 
用法示例： `if test ${num1} -eq ${num2}`

|参数|  说明|
|------|---------|
|-eq  |等于则为真
|-ne  |不等于则为真
|-gt  |大于则为真
|-ge  |大于等于则为真
|-lt  |小于则为真
|-le  |小于等于则为真|

### 字符串测试
用法示例：`if test str1=str2`

|参数   |说明|
|----------|------|
|=  |等于则为真|
|!=   |不相等则为真
|-z 字符串|  字符串的长度为零则为真
|-n 字符串   |字符串的长度不为零则为真|

### 文件测试
用法示例：`if test -e ./bash`

|参数|  说明|
|------|---------|
|-e 文件名   |如果文件存在则为真
|-r 文件名   |如果文件存在且可读则为真
|-w 文件名   |如果文件存在且可写则为真
|-x 文件名   |如果文件存在且可执行则为真
|-s 文件名   |如果文件存在且至少有一个字符则为真
|-d 文件名   |如果文件存在且为目录则为真
|-f 文件名   |如果文件存在且为普通文件则为真
|-c 文件名   |如果文件存在且为字符型特殊文件则为真
|-b 文件名   |如果文件存在且为块特殊文件则为真|

## 流程控制
### if-else
1. if-else
```bash?linenums
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi
```
2. if else-if else
```bash?linenums
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
```
### for循环
```bash?linenums
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

### while循环
```bash?linenums
while condition
do
    command
done
```
### until循环
```bash?linenums
until condition
do
    command
done
```

### case循环
```bash?linenums
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
esac
```
示例：
```bash?linenums
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```
**说明：** 
- 一旦模式匹配，则执行完匹配模式相应命令后不再继续其他模式。如果无一匹配模式，使用星号 * 捕获该值，再执行后面的命令。 
- 每个case分支用右圆括号，用两个分号表示break。

### 跳出循环
- `break`：跳出所有循环
- `continu`：跳出当前循环
- `esac`：(case反过来)，退出case


## 函数
**定义格式：**
```bash?linenums
[ function ] funname [()]
{
    action;
    [return int;]
}
```
示例：
```bash?linenums
funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
     return $(($1+$2))
}
funWithParam 1 2 3 4 5 6 7 8 9 34 73
echo "输入的两个数字之和为 $? !"
```
输出结果：
```bash
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
输入的两个数字之和为3
```
**说明：**
- 可以使用`function func()`定义，也可直接使用`func()`定义
- 参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return后跟数值n(0-255)
- 函数返回值在调用该函数后通过 $? 来获得。
- 所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。调用函数仅使用其函数名即可。 


## 文件包含
和其他语言一样，Shell 也可以包含外部脚本。这样可以很方便的封装一些公用的代码作为一个独立的文件。
**基本语法：**
```bash?linenums
. filename   # 注意点号(.)和文件名中间有一空格
或
source filename
```
被包含的文件 filename 不需要可执行权限。