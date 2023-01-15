---
title: "C++函数后的关键字"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "整理部分现代C++作用在函数后的关键字"
lightgallery: true
tags: [cpp]
categories: [cpp]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [C++函数后面加const关键字](#c函数后面加const关键字)
- [C++函数后面加throw关键字](#c函数后面加throw关键字)
- [C++11引入的关键字=default、=delete、override、final](#c11引入的关键字defaultdeleteoverridefinal)
  - [1. =default](#1-default)
  - [2. =delete](#2-delete)
  - [3. override](#3-override)

<!-- /TOC -->

# C++函数后面加const关键字

1. 非静态成员函数后面加const（加到非成员函数或静态成员后面会产生编译错误）,表示成员函数隐含传入的this指针为const指针，决定了在该成员函数中，任意修改它所在的类的成员的操作都是不允许的（因为隐含了对this指针的const引用）
2. 唯一的例外是对于mutable修饰的成员。
   加了const的成员函数可以被非const对象和const对象调用,但不加const的成员函数只能被非const对象调用

```c++
char getData() const
{
	return this->letter;
}
```



# C++函数后面加throw关键字

void fun() throw() 表示fun不允许抛出任何异常，即fun是异常安全的。

void fun() throw(...) 表示fun可以抛出任何形式的异常。

void fun() throw(exceptionType) 表示fun只能抛出exceptionType类型的异常。



void GetTag() throw(int); 表示只抛出int类型异常

void GetTag() throw(int，char); 表示抛出in，char类型异常

void GetTag() throw(); 表示不会抛出任何类型异常

void GetTag() throw(...); 表示抛出任何类型异常



那么，void GetTag() throw(int); 表示只抛出int类型异常 这句解释怎么理解呢？

并不表示一定会抛出异常，但是一旦抛出异常只会抛出int类型。

如果抛出非int类型异常，调用unexsetpion()函数，退出程序。



# C++11引入的关键字=default、=delete、override、final



## 1. =default

在C++11中，如果需要使用默认的构造、析构函数，需要在参数列表后面加上`=default`关键字，与之对应，如果你不想使用默认的构造、析构函数，就在参数列表后面加上`=delete`，这样编译器就会删除对应的默认构造、析构函数。

```cpp
//demo.h
#pragma once
class CobjectA {
public:

    CobjectA(int i);
    CobjectA() = default;

};
```

再次编译输出：

```c
Test Con !
```

这样可以看出，当我们需要使用编译器默认生成的构造函数，只需要在头文件声明的时候，在参数列表后加上关键字`=default`，不需要在对应的cpp文件中进行定义。



## 2. =delete

为了不让一个类被拷贝，可以将其拷贝构造函数声明为`private`属性，但是有了`delete`关键字后，可以直接在拷贝构造函数的参数列表后面加上`=delete`，对于一些不需要构造、析构函数的类，可以在其默认的构造、析构函数参数列表后加上`=delete`，减少生成可执行文件的体积。

## 3. override

在继承机制中会用到`override`关键字，使用该关键字可以避免一些低级错误的出现。例如，定义类A与类B，并让B继承A。

```cpp
#include <iostream>

class A {
public:
    virtual void func1()
    {
        std::cout << "class A func1" << std::endl;
    }
};

class B : A {
public:
    virtual void func1() override
    {
        std::cout << "class B func1" << std::endl;
    }
};

int main()
{
    A a;
    a.func1();
    B b;
    b.func1();
    return 0;
}
```

A中有虚函数func1，在B中继承后重新实现了一下，输出结果：

```c
class A func1
class B func1
```

在B中改写func1，其后增加了`override`关键字，如果存在这样一种情况，本想改写从A中继承来的func1，由于疏忽，修改了函数的签名：

```cpp
#include <iostream>

class A {
public:
    virtual void func1()
    {
        std::cout << "class A func1" << std::endl;
    }
};

class B : A {
public:
    virtual void func2() override   //将func1写成了func2
    {
        std::cout << "class B func2" << std::endl;
    }
};

int main()
{
    A a;
    a.func1();
    B b;
    b.func2();
    return 0;
}
```

结果编译出错：

```c
test2.cpp:15:18: error: ‘virtual void B::func2()’ marked ‘override’, but does not override
   15 |     virtual void func2() override //将func1写成了func2
      |
```

提示没有重写基类的方法，所以，有了`override`关键字，一定要重写基类的方法，这里的func2相当于是在类B中重新定义的一个函数。

```cpp
#include <iostream>

class A {
public:
    virtual void func1()
    {
        std::cout << "class A func1" << std::endl;
    }
};

class B : A {
public:
    virtual void func2()   //这里去掉override关键字，相当于自己定义了一个func2函数
    {
        std::cout << "class B func2" << std::endl;
    }
};

int main()
{
    A a;
    a.func1();
    B b;
    b.func2();
    return 0;
}
```

输出结果：

```c
class A func1
class B func2
```

