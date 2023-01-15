---
title: "拷贝构造函数和拷贝赋值函数"
date: 2023-01-15T17:10:42+08:00
draft: false
description: ""
lightgallery: true
tags: [cpp]
categories: [cpp]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [拷贝构造函数](#%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)
- [拷贝构造函数的调用时机](#%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E7%9A%84%E8%B0%83%E7%94%A8%E6%97%B6%E6%9C%BA)
    - [1. 当函数的参数为类的对象:以值传递的方式传入函数体](#1-%E5%BD%93%E5%87%BD%E6%95%B0%E7%9A%84%E5%8F%82%E6%95%B0%E4%B8%BA%E7%B1%BB%E7%9A%84%E5%AF%B9%E8%B1%A1%E4%BB%A5%E5%80%BC%E4%BC%A0%E9%80%92%E7%9A%84%E6%96%B9%E5%BC%8F%E4%BC%A0%E5%85%A5%E5%87%BD%E6%95%B0%E4%BD%93)
    - [2. 函数的返回值是类的对象:以值传递的方式从函数返回](#2-%E5%87%BD%E6%95%B0%E7%9A%84%E8%BF%94%E5%9B%9E%E5%80%BC%E6%98%AF%E7%B1%BB%E7%9A%84%E5%AF%B9%E8%B1%A1%E4%BB%A5%E5%80%BC%E4%BC%A0%E9%80%92%E7%9A%84%E6%96%B9%E5%BC%8F%E4%BB%8E%E5%87%BD%E6%95%B0%E8%BF%94%E5%9B%9E)
    - [3. 对象需要通过另外一个对象进行初始化 : 赋值初始化](#3-%E5%AF%B9%E8%B1%A1%E9%9C%80%E8%A6%81%E9%80%9A%E8%BF%87%E5%8F%A6%E5%A4%96%E4%B8%80%E4%B8%AA%E5%AF%B9%E8%B1%A1%E8%BF%9B%E8%A1%8C%E5%88%9D%E5%A7%8B%E5%8C%96--%E8%B5%8B%E5%80%BC%E5%88%9D%E5%A7%8B%E5%8C%96)
- [浅拷贝与深拷贝](#%E6%B5%85%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B7%B1%E6%8B%B7%E8%B4%9D)
    - [1. 默认拷贝构造函数](#1-%E9%BB%98%E8%AE%A4%E6%8B%B7%E8%B4%9D%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0)
- [拷贝赋值函数](#%E6%8B%B7%E8%B4%9D%E8%B5%8B%E5%80%BC%E5%87%BD%E6%95%B0)
- [copy constructor与copy assignment的区别与联系](#copy-constructor%E4%B8%8Ecopy-assignment%E7%9A%84%E5%8C%BA%E5%88%AB%E4%B8%8E%E8%81%94%E7%B3%BB)

<!-- /TOC -->


# 拷贝构造函数

拷贝构造函数(复制构造函数)是构造函数的一种，它只有一个参数，参数类型是本类的引用。它由编译器调用来完成一些基于同一类的其他对象的构建及初始化。

复制构造函数的参数可以是 const 引用，也可以是非 const 引用。 一般使用前者，这样既能以常量对象（初始化后值不能改变的对象）作为参数，也能以非常量对象作为参数去初始化其他对象。一个类中写两个复制构造函数，一个的参数是 const 引用，另一个的参数是非 const 引用，也是可以的。

如果类的设计者不写复制构造函数，编译器就会自动生成复制构造函数。大多数情况下，其作用是实现从源对象到目标对象逐个字节的复制，即使得目标对象的每个成员变量都变得和源对象相等。编译器自动生成的复制构造函数称为“默认复制构造函数”。

默认构造函数（即无参构造函数）不一定存在，但是复制构造函数总是会存在。

```c++
#include<iostream>
using namespace std;
class CExample
{
private:
    int a;
public:
    //构造函数
    CExample(int b)
    {
        a=b;
        printf("constructor is called\n");
    }
    //拷贝构造函数
    CExample(const CExample & c)
    {
        a=c.a;
        printf("copy constructor is called\n");
    }
    //析构函数
    ~CExample()
    {
        cout<<"destructor is called\n";
    }
    void Show()
    {
        cout<<a<<endl;
    }
};
int main()
{
    CExample A(100);
    CExample B=A;
    B.Show();
    return 0;
}
```
执行结果：

    constructor is called
    copy constructor is called
    100
    destructor is called
    destructor is called

# 拷贝构造函数的调用时机

## 1. 当函数的参数为类的对象:以值传递的方式传入函数体

如果函数 F 的参数是类 A 的对象，那么当 F 被调用时，类 A 的复制构造函数将被调用。换句话说，作为形参的对象，是用复制构造函数初始化的，而且调用复制构造函数时的参数，就是调用函数时所给的实参。

``` c++
#include<iostream>
using namespace std;
class CExample
{
private:
    int a;
public:
    CExample(int b)
    {
        a=b;
        printf("constructor is called\n");
    }
    CExample(const CExample & c)
    {
        a=c.a;
        printf("copy constructor is called\n");
    }
    ~CExample()
    {
     cout<<"destructor is called\n";
    }
    void Show()
    {
     cout<<a<<endl;
    }
};
void g_fun(CExample c)
{
    cout<<"g_func"<<endl;
}
int main()
{
    CExample A(100);
    CExample B=A;
    B.Show();
    g_fun(A);
    return 0;
}
```
执行结果：

    constructor is called
    copy constructor is called
    100
    copy constructor is called
    g_func
    destructor is called
    destructor is called
    destructor is called

调用g_fun()时，会产生以下几个重要步骤：
1. A对象传入形参时，会先会产生一个临时变量，就叫 C 吧。
2. 然后调用拷贝构造函数把A的值给C。 整个这两个步骤有点像：CExample C(A);
3. 等g_fun()执行完后, 析构掉 C 对象。

## 2. 函数的返回值是类的对象:以值传递的方式从函数返回

如果函数的返冋值是类 A 的对象，则函数返冋时，类 A 的复制构造函数被调用。换言之，作为函数返回值的对象是用复制构造函数初始化 的，而调用复制构造函数时的实参，就是 return 语句所返回的对象。

``` c++
#include<iostream>
using namespace std;
class CExample
{
private:
    int a;
public:
    //构造函数
    CExample(int b)
    {
     a=b;
        printf("constructor is called\n");
    }
    //拷贝构造函数
    CExample(const CExample & c)
    {
     a=c.a;
        printf("copy constructor is called\n");
    }
    //析构函数
    ~CExample()
    {
     cout<<"destructor is called\n";
    }
    void Show()
    {
     cout<<a<<endl;
    }
};
CExample g_fun()
{
    CExample temp(0);
    return temp;
}

int main()
{
    CExample example = g_fun();
    example.Show();
    return 0;
}
```
运行结果：

    constructor is called
    copy constructor is called
    destructor is called
    copy constructor is called
    destructor is called
    10
    destructor is called

当g_Fun()函数执行到return时，会产生以下几个重要步骤：
1. 先会产生一个临时变量，就叫XXXX吧。
2. 然后调用拷贝构造函数把temp的值给XXXX。整个这两个步骤有点像：CExample XXXX(temp);
3. 在函数执行到最后先析构temp局部变量。
4. 等g_fun()执行完后再析构掉XXXX对象。

说明，由于g++ 编译是会进行优化，这里需要使用 ： g++ -fno-elide-constructors xx 进行编译
[C++返回值为对象时复制构造函数不执行怎么破](https://blog.csdn.net/sxhelijian/article/details/50977946)
[命名返回值优化](http://blog.sina.com.cn/s/blog_4ab8464c0100kybj.html)

## 3. 对象需要通过另外一个对象进行初始化 : 赋值初始化

当用一个对象去初始化同类的另一个对象时，会引发复制构造函数被调用。

```c++
CExample A(100);
CExample B=A;
CExample C(A);
```

注意：赋值语句的等号左边是一个早已有定义的变量，赋值语句不会引发复制构造函数的调用。

```c++
CExample c1, c2; c1 = c2 ;
c1=c2;
```

这条语句不会引发复制构造函数的调用，因为 c1 早已生成，已经初始化过了。



# 浅拷贝与深拷贝

## 1. 默认拷贝构造函数

很多时候在我们都不知道拷贝构造函数的情况下，传递对象给函数参数或者函数返回对象都能很好的进行，这是因为编译器会给我们自动产生一个拷贝构造函数，这就是“默认拷贝构造函数”，这个构造函数很简单，仅仅使用“老对象”的数据成员的值对“新对象”的数据成员一一进行赋值，它一般具有以下形式：
```c++
Rect::Rect(const Rect& r)
{
    width=r.width;
    height=r.height;
}
```
当然，以上代码不用我们编写，编译器会为我们自动生成。但是如果认为这样就可以解决对象的复制问题，那就错了。
<!-- <div align=center> -->

![xxx](../images/拷贝构造.png)

<!-- </div> -->


[c++拷贝构造函数详解](https://www.cnblogs.com/alantu2018/p/8459250.html)

[C++拷贝构造函数（复制构造函数）详解](http://c.biancheng.net/view/151.html)



# 拷贝赋值函数

```c++
A& operator= (const A& a);		//赋值构造函数
```

返回值的对象为引用是为了可以连续赋值

赋值函数中可以既可以使用引用也可以使用值传递，不过值传递会多生成一个对象，造成资源的浪费

**1.对象以值传递方式从函数返回，且接受返回值的对象已经初始化过**

```c++
    A c;
    c = f();
```



**2.对象直接赋值给另一个对象，且接受值的对象已经初始化过**

```c++
    A a(1);
    A c;
    c = a;
```



# copy constructor与copy assignment的区别与联系

copy constructor：

***从一个已有的对象来构造另一个对象；***

包括：

用已有对象来初始化新声明的对象；

将对象按值传递给函数作为参数；

函数按值返回对象。

copy assignment：

***将已有的对象赋值个另一个已有的对象；***

实例：

```cpp
Person A(B); // copy constructor

Person C=B; //copy constructor

Function1(D); //copy constructor

B= Function2(…); //copy constructor

Person D;

D=B; //copy assignment
```

注意事项：编译器默认的copy constructor和copy assignment操作，是按照member-wise copy的方式逐个copy每个member，这种浅拷贝操作在有些情况下可能造成资源泄漏/指向重叠。

如果的确需要deep copy，需要自定义相应操作。这时需要清楚哪些地方用了copy constructor，哪些地方用了copy assignment，从而分别自定义copy constructor和copy assignment。一般来说，自定义的copy constructor、destructor和copy assignment操作常常同时出现。

区别与联系：

copy constructor不用检测是否是用一个对象来初始化它自己；

copy constructor不用对被构造对象做资源清理操作，如delete操作；