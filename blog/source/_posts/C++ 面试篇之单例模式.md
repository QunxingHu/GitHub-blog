---
title: C++面试篇之单例模式
date: 2017-05-08 11:44:39
categories:
	- 术业专攻
tags:
  - C++
  - 面试
---

在面试过程中，无论是C++还是JAVA面试，涉及到设计模式的问题时，单例模式模式是被提及次数最多的一种设计模式，本篇博客主要是对懒汉式与饿汉式的单例模式的一些混淆点和知识点进行小结。
<!-- more -->

# 单例模式

## 懒汉式： 第一次使用的时候创建
```cpp
class Student
{
public:
static Student* GetInstance(){
if(m_pInstance == NULL)
m_pInstance = new Student();
reutn m_pInstance;
}
private:
Student(){} //私有构造函数
static Student *m_pInstance;
}

Student* Student::m_pInstance = NULL; //初始化 static

```
**懒汉式不是线程安全的**，线程安全实现版本如下：
```cpp
class Student
{
protected:
Student()
{
pthread_mutex_init(&mutex);
}
public:
static pthread_mutex_t mutex;
static Studnet* GetInsance();
int a;
};

pthread_mutex_t Student::mutex;
Student* Student::GetInstance()
{
pthread_mutex_lock(&mutex);
static Student obj;
pthread_mutex_unlock(&mutex);
return &obj;
}
```

## 饿汉式： 定义的时候初始化
```cpp
class Student
{
public:
static Student* GetInstance(){
reutn m_pInstance;
}
private:
Student(){} //私有构造函数
static Student *m_pInstance;
}

Student* Student::m_pInstance = new Student(); //初始化并创建

```

饿汉式是线程安全的


**总结：**

**懒汉：**故名思义，不到万不得已就不会去实例化类，也就是说在第一次用到类实例的时候才会去实例化，所以上边的经典方法被归为懒汉实现；
**饿汉：**饿了肯定要饥不择食。所以在单例类定义的时候就进行实例化。
**特点与选择：**
由于要进行线程同步，所以在访问量比较大，或者可能访问的线程比较多时，采用饿汉实现，可以实现更好的性能。这是以空间换时间。
在访问量较小时，采用懒汉实现。这是以时间换空间。