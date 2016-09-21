---
title: C++ 文件流
date: 2016-9-13 2:38:58 PM
categories:
- 术业专攻
tags: 
- C++
---
本文主要介绍C++ 中文件操作的相关知识。
<!-- more -->
## 概述
C++ 为字符，字符串，文件提供了一套输入输出的操作机制，称之为流。下图为 C++ 中输入输出流的关系图。

![C++流结构][1]

对于文件操作，我们需要用到`<fstream>`,与之相关的流有`ifstream，ofstream, fstream, filebuf`。下面对这四种流进行介绍。

```cpp?linenums
#include <fstream> 
ifstream         //文件读操作，存储设备读区到内存中  (由istream引申而来)
fstream          //读写操作，对打开的文件可进行读写操作 (由iostream引申而来)
ofstream         //文件写操作 内存写入存储设备  (由ostream引申而来)
filebuf  		//读写操作，对打开的文件可进行读写操作 (由streambuf引申而来)
```

C++ 文件操作的基本顺序：
1. 打开文件
2. 读写操作文件
3. 关闭文件

## 打开文件
在fstream类中，使用成员函数open()实现打开文件的操作，从而将数据流和文件进行关联，通过ofstream, ifstream, fstream, filebuf 对象进行对文件的读写操作。
fstream::open()函数形式：
```cpp?linenums
/**
* 参数说明: 
* @filename: 文件名
* @openmode: 打开方式
**/
void open (const char* filename, ios_base::openmode mode = ios_base::in | ios_base::out);
void open (const string& filename, ios_base::openmode mode = ios_base::in | ios_base::out);
```
### 文件打开方式
------------------------
|打开方式|  说明  |
|:-----------|:---------|
|ios::in	| 为读文件而打开|
|ios:out	| 为写文件而打开|
|ios::ate   | 初始位置为文件尾|
|ios::app | 所有输出附加在文件尾|
|ios::trunc|如果文件已存在则先删除该文件|
|ios::binary|二进制方式打开文件|
---------------------------------
说明：
文件打开方式可以组合使用，通过`|`隔开。

### 其他相关函数
`fstream::is_open()`: 检查文件是否打开
`filebuf::open()`: 打开文件与 file stream buffer object关联

## 操作文件
读写文件分为文本文件和二进制文件的读取，对于文本文件的读取比较简单，用插入器和析取器就可以了；而对于二进制的读取就要复杂些。
### 文本文件的读写
文本文件的读写很简单：用插入器`<<`向文件输出；用析取器`>>`从文件输入。假设file1是以输入方式打开，file2以输出打开。示例如下：
```
file2<<"I Love You";//向文件写入字符串"I Love You"
int i;
file1>>i;//从文件输入一个整数值。
```
示例：
```cpp?linenums
// writing on a text file
#include <fiostream.h>
int main () {
    ofstream out("out.txt");
    if (out.is_open()) 
    {
        out << "This is a line.\n";
        out << "This is another line.\n";
        out.close();
	}
	return 0;
}
//结果: 在out.txt中写入：
This is a line.
This is another line 
```
从文件中读入数据也可以用与 cin>>的使用同样的方法：
```cpp?linenums
 // reading a text file
 #include <iostream.h>
 #include <fstream.h>
 #include <stdlib.h>

int main () {
	char buffer[256];
	ifstream in("test.txt");
	if (! in.is_open())
		{ cout << "Error opening file"; exit (1); }
	while (!in.eof() )
	{
		in.getline (buffer,100);
		cout << buffer << endl;
	}
	return 0;
}
//结果 在屏幕上输出
This is a line.
This is another line
```

这种方式还有一种简单的格式化能力，比如可以指定输出为16进制等等，具体的格式有以下一些

-----------------------
|操纵符 |功能| 输入/输出|
|:---------|:-----|:-------------|
|dec |格式化为十进制数值数据 |输入和输出|
|endl| 输出一个换行符并刷新此流| 输出|
|ends| 输出一个空字符 |输出|
|hex |格式化为十六进制数值数据 |输入和输出|
|oct |格式化为八进制数值数据 |输入和输出|
|setpxecision(int p)| 设置浮点数的精度位数 |输出|
----------------------------------------------

示例:
```cpp?linenums
//将123当作十六进制输出：
file1<<hex<<123;
//将3.1415926以5位精度输出：
file1<<setpxecision(5)<<3.1415926
```
### 二进制文件的读写
- **`put()`函数**
put()函数向流写入一个字符，其原型是ofstream &put(char ch)，使用也比较简单，如file1.put('c');就是向流写一个字符'c'。

- **`get()`函数**
get()函数比较灵活，有3种常用的重载形式：
  - `ifstream &get(char &ch);` 功能:从流中读取一个字符，结果保存在引用ch中，如果到文件尾，返回空字符。如`file2.get(x);`表示从文件中读取一个字符，并把读取的字符保存在x中。
  - `int get();`这种形式是从流中返回一个字符，如果到达文件尾，返回EOF，如`x=file2.get();`和上例功能是一样的。
  - `ifstream &get(char *buf,int num,char delim='n')；`这种形式把字符读入由 buf 指向的数组，直到读入了 num 个字符或遇到了由 delim 指定的字符，如果没使用 delim 这个参数，将使用缺省值换行符'n'。例如：`
file2.get(str1,127,'A');`从文件中读取字符到字符串str1，当遇到字符'A'或读取了127个字符时终止。

- 读写数据块
要读写二进制数据块，使用成员函数read()和write()成员函数，它们原型如下：
```cpp?linenums
read(unsigned char *buf,int num);
write(const unsigned char *buf,int num);
```
  -  `read()`函数
   从文件中读取 num 个字符到 buf 指向的缓存中，如果在还未读入 num 个字符时就到了文件尾，可以用成员函数 int gcount();来取得实际读取的字符数；
  - `write()` 函数
   从buf 指向的缓存写 num 个字符到文件中，值得注意的是缓存的类型是 `unsigned char *`，有时可能需要类型转换。
示例：
```cpp?linenums
unsigned char str1[]="I Love You";
int n[5];
ifstream in("xxx.xxx");
ofstream out("yyy.yyy");
out.write(str1,strlen(str1));//把字符串str1全部写到yyy.yyy中
in.read((unsigned char*)n,sizeof(n));//从xxx.xxx中读取指定个整数，注意类型转换
in.close();out.close();
```

示例：
二进制文件读写
```cpp?linenums
// reading binary file
#include <iostream>
#include <fstream.h>

const char * filename = "test.txt";

int main () {
    char * buffer;
    long size;
    ifstream in (filename, ios::in|ios::binary|ios::ate);
    size = in.tellg();
    in.seekg (0, ios::beg);
    buffer = new char [size];
    in.read (buffer, size);
    in.close();

    cout << "the complete file is in a buffer";

    delete[] buffer;
    return 0;
}
//运行结果：
The complete file is in a buffer
```

获取二进制文件大小
```cpp?linenums
// obtaining file size  
#include <iostream.h>  
#include <fstream.h>  

const char * filename = "test.txt";  

int main () {  
    long l,m;  
    ifstream in(filename, ios::in|ios::binary);  
    l = in.tellg();  
    in.seekg (0, ios::end);  
    m = in.tellg();  
    in.close();  
    cout << "size of " << filename;  
    cout << " is " << (m-l) << " bytes.\n";  
    return 0;  
}  

//结果:  
size of example.txt is 40 bytes.  
```
### 检测EOF
成员函数eof()用来检测是否到达文件尾，如果到达文件尾返回非0值，否则返回0。原型是`int eof();`
示例：
```cpp?linenums
if(in.eof())ShowMessage("已经到达文件尾！");
```
除了`eof()`外，还有一些验证流的状态的成员函数（所有都返回bool型返回值）：
- `bad()`
如果在读写过程中出错，返回 true 。例如：当我们要对一个不是打开为写状态的文件进行写入时，或者我们要写入的设备没有剩余空间的时候。
- `fail()`
除了与bad() 同样的情况下会返回 true 以外，加上格式错误时也返回true ，例如当想要读入一个整数，而获得了一个字母的时候。
- `eof()`
如果读文件到达文件末尾，返回true。
- `good()`
这是最通用的：如果调用以上任何一个函数返回true 的话，此函数返回 false 。
- `clear()`
要想重置以上成员函数所检查的状态标志，你可以使用成员函数clear()。

### 文件定位
和 C的文件操作方式不同的是，C++ I/O系统管理**两个**与一个文件相联系的指针。一个是**读指针**，它说明输入操作在文件中的位置；另一个是**写指针**，它下次写操作的位置。每次执行输入或输出时， 相应的指针自动变化。所以，C++的文件定位分为读位置和写位置的定位，对应的成员函数是 seekg()和 seekp()，seekg()是设置读位置，seekp是设置写位置。它们最通用的形式如下：

#### 设置指针位置
```cpp?linenums
istream &seekg ( pos_type position );
ostream &seekp ( pos_type position ); 
istream &seekg(streamoff offset,seek_dir origin);		//设置读位置
ostream &seekp(streamoff offset,seek_dir origin);	  //设置写位置
```
`streamoff`定义于 iostream.h 中，定义有偏移量 offset 所能取得的最大值，seek_dir 表示移动的基准位置，是一个有以下值的枚举：
```cpp?linenums
ios::beg： 文件开头
ios::cur： 文件当前位置
ios::end： 文件结尾
```
这两个函数**一般用于二进制文件**，因为文本文件会因为系统对字符的解释而可能与预想的值不同。
示例：
```cpp?linenums
file1.seekg(1234,ios::cur);//把文件的读指针从当前位置向后移1234个字节
file2.seekp(1234,ios::beg);//把文件的写指针从文件开头向后移1234个字节
```

#### 获取指针位置
`tellg()` 和 `tellp()` 这两个成员函数不用传入参数，返回pos_type 类型的值(根据ANSI-C++ 标准) ，就是一个整数，代表当前 `get 流`指针的位置 (用tellg) 或 `put 流`指针的位置(用tellp)。

流指针 get 和 put 的值对文本文件(text file)和二进制文件(binary file)的计算方法都是不同的，因为文本模式的文件中某些特殊字符可能被修改。由于这个原因，建议**对以文本文件模式打开的文件总是使用seekg 和 seekp的第一种原型，而且不要对tellg 或 tellp 的返回值进行修改**。对二进制文件，你可以任意使用这些函数，应该不会有任何意外的行为产生。

## 关闭文件
当文件读写操作完成之后，我们必须将文件关闭以使文件重新变为可访问的。关闭文件需要调用成员函数`close()`，它负责将缓存中的数据排放出来并关闭文件。它的格式很简单：`void close ();`
这个函数一旦被调用，原先的流对象(stream object)就可以被用来打开其它的文件了，这个文件也就可以重新被其它的进程(process)所有访问了。
为防止流对象被销毁时还联系着打开的文件，析构函数(destructor)将会自动调用关闭函数close。

代码示例：
```cpp?linenums
/**
*读写二进制文件
*/
#include "stdafx.h"
#include <iostream>
#include <fstream>
#include <string>
using namespace std;
struct plant
{
	char name[20];
	int age;
	char num[10];
};
int _tmain(int argc, _TCHAR* argv[])
{
    plant p;
	p.name[20]=(char)"yao";
	p.age=21;
	p.num[10]=(char)"0950420011";
	ofstream pf("data.dat",ios_base::out|ios_base::app|ios_base::binary);
	pf.write((char*)&p,sizeof(p));
	pf.close();
	ifstream in("data.dat",ios_base::in|ios_base::binary);
	plant p1;
	in.read((char*)&p1,sizeof(p1));
	in.close();
	cout<<p1.age<<endl<<p1.name<<endl<<p1.num<<endl;
           return 0;
}
```


  [1]: https://github.com/QunxingHu/images/raw/master/iostream.jpg