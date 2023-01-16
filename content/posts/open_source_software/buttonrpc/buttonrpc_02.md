---
title: "开源软件:buttonrpc (C++语法分析)"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [buttonrpc]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [C++ 语法分析](#c-语法分析)
  - [template](#template)
  - [typename](#typename)
  - [std::bind](#stdbind)
  - [std::is_same<R, void>::value](#stdis_samer-voidvalue)
  - [std::enable_if](#stdenable_if)
  - [std::decay](#stddecay)
  - [std::reverse和std::reverse_copy](#stdreverse和stdreverse_copy)
- [可变参数模板消除冗余代码](#可变参数模板消除冗余代码)

<!-- /TOC -->


# C++ 语法分析

##  template
```cpp
template<typename T>

struct type_xx{ typedef T type; };
```
将T 类型置换为 type 

使用：typename type_xx<R>::type ，实际为类型R



## typename

"typename"是一个C++程序设计语言中的关键字。当用于泛型编程时是另一术语"class"的同义词。这个关键字用于指出模板声明（或定义）中的非独立名称（dependent names）是类型名，而非变量名。

我们经常会这么用 typename，这是一项C++编程语言的泛型编程（或曰“模板编程”）的功能，typename关键字用于引入一个模板参数。

```cpp
template <typename T>
const T& max(const T& x, const T& y)
{
  if (y < x) {
    return x;
  }
  return y;
}
```


在模板定义语法中关键字 class 与 typename 的作用完全一样

```cpp
template<class T>
const T& max(const T& x, const T& y)
{
  if (y < x) {
    return x;
  }
  return y;
}
```
这里 class 关键字表明T是一个类型，后来为了避免 class 在这两个地方的使用可能给人带来混淆，所以引入了 typename 这个关键字，它的作用同 class 一样表明后面的符号为一个类型。

那class使用就够了，为什么又引入了新的关键词 typename ，关于这个问题，Stan Lippman 曾在其博客中表示，最早 Stroustrup 使用 class 来声明模板参数列表中的类型是为了避免增加不必要的关键字；后来委员会认为这样混用可能造成概念上的混淆才加上了 typename 关键字。

而使用 typename 的作用就是告诉 cpp 编译器，typename 后面的字符串为一个类型名称，而不是成员函数或者成员变量，这个时候如果前面没有 typename，编译器没有任何办法知道 T::LengthType 是一个类型还是一个成员名称(静态数据成员或者静态函数)，所以编译不能够通过。

**解决的问题**

```cpp
template<typename T>
void fun(const T& proto){
         T::const_iterator it(proto.begin());
}
```
发生编译错误是因为编译器不知道T::const_iterator是个类型。万一它是个变量呢？ T::const_iterator的解析有着逻辑上的矛盾： 直到确定了T是什么东西，编译器才会知道T::const_iterator是不是一个类型； 然而当模板被解析时，T还是不确定的。这时我们声明它为一个类型才能通过编译：

而且在模板实例化之前，完全没有办法来区分它们，这绝对是滋生各种bug的温床。这时C++标准委员会再也忍不住了，与其到实例化时才能知道到底选择哪种方式来解释以上代码，委员会决定引入一个新的关键字，这就是typename。

**C++标准**
对于用于模板定义的依赖于模板参数的名称，只有在实例化的参数中存在这个类型名，或者这个名称前使用了 typename 关键字来修饰，编译器才会将该名称当成是类型。除了以上这两种情况，绝不会被当成是类型。

因此，如果你想直接告诉编译器 T::const_iterator 是类型而不是变量，只需用 typename修饰：

```cpp
typename    T::const_iterator it(proto.begin());
```


这样编译器就可以确定T::const_iterator是一个类型，而不再需要等到实例化时期才能确定，因此消除了前面提到的歧义。

**嵌套从属类型**

事实上类型T::const_iterator依赖于模板参数T， 模板中依赖于模板参数的名称称为从属名称（dependent name）， 当一个从属名称嵌套在一个类里面时，称为嵌套从属名称（nested dependent name）。 其实T::const_iterator还是一个嵌套从属类型名称（nested dependent type name）。

嵌套从属名称是需要用typename声明的，其他的名称是不可以用typename声明的。比如下面是一个合法的声明：

```cpp
template<typename T>
void fun(const T& proto ,typename  T::const_iterator it);
```



## std::bind

参考: [当 std::bind 遇上 this](https://blog.csdn.net/lqw198421/article/details/115087355)

bind本身是一种延迟计算的思想，它本身可以绑定普通函数、全局函数、静态函数、类静态函数甚至是类成员函数.

bind能够在绑定时候就同时绑定一部分参数，未提供的参数则使用占位符表示，然后在运行时传入实际的参数值。PS：绑定的参数将会以值传递的方式传递给具体函数，占位符将会以引用传递。众所周知，静态成员函数其实可以看做是全局函数，而非静态成员函数则需要传递this指针作为第一个参数，所以std::bind能很容易地绑定成员函数。

**std::bind 遇上 this** : **非静态成员函数则需要传递this指针作为第一个参数.**



##  std::is_same<R, void>::value 

头文件 ： `#include <type_traits>` 

如果两个一样的类型会返回true



## std::enable_if

`td::enable_if` 顾名思义，满足条件时类型有效。作为选择类型的小工具，其广泛的应用在 C++ 的模板元编程（meta programming）中。它的定义也异常的简单：

```cpp
template <bool, typename T=void>
struct enable_if {
};
template <typename T>
struct enable_if<true, T> {
  using type = T;
};
```

由上可知，只有当第一个模板参数为 `true` 时，`type` 才有定义，否则使用 `type` 会产生编译错误，并且默认模板参数可以让你不必指定类型。

```cpp
// help call return value type is void function
template<typename R, typename F>
typename std::enable_if<std::is_same<R, void>::value, typename type_xx<R>::type >::type call_helper(F f) {
	f();
	return 0;
}
// 这里的意思是 如果 

template<typename R, typename F>
typename std::enable_if<!std::is_same<R, void>::value, typename type_xx<R>::type >::type call_helper(F f) {
	return f();
}
```

```cpp
std::enable_if<
    std::is_same<R, void>::value, 
    typename type_xx<R>::type 
        >::type
```

```cpp
template<typename R>
void buttonrpc::callproxy_(std::function<R()> func, Serializer* pr, const char* data, int len)
{
	typename type_xx<R>::type r = call_helper<R>(std::bind(func));
	value_t<R> val;
	val.set_code(RPC_ERR_SUCCESS);
	val.set_val(r);
	(*pr) << val;
}

```

```cpp
template<typename F>
void buttonrpc::callproxy( F fun, Serializer* pr, const char* data, int len )
{
	callproxy_(fun, pr, data, len);
}
```

```cpp
template<typename F>
void buttonrpc::bind( std::string name, F func )
{
	m_handlers[name] = std::bind(&buttonrpc::callproxy<F>, this, func, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3);
}
```



## std::decay

头文件 ： `#include <type_traits>` 

对一个类型进行退化处理，如 ： cosnt int&退化为int



## std::reverse和std::reverse_copy

  std::reverse：反转排序容器内指定范围中的元素。std::reverse_copy与std::reverse唯一的区别是：reverse_copy会将结果拷贝到另外一个容器中，而不影响原容器的内容。

>  std::reverse: defined in header <algorithm>,  reverses the order of the elements in the range [first, last).
>
>  std::reverse_copy: defined in header <algorithm>, copies the elements in the range[first,last) to the range beginning at result, but in reverse order.
>



# 可变参数模板消除冗余代码

```cpp
// client
template<typename R>
value_t<R> call(std::string name);

template<typename R, typename P1>
value_t<R> call(std::string name, P1);

template<typename R, typename P1, typename P2>
value_t<R> call(std::string name, P1, P2);

template<typename R, typename P1, typename P2, typename P3>
value_t<R> call(std::string name, P1, P2, P3);

template<typename R, typename P1, typename P2, typename P3, typename P4>
value_t<R> call(std::string name, P1, P2, P3, P4);

template<typename R, typename P1, typename P2, typename P3, typename P4, typename P5>
value_t<R> call(std::string name, P1, P2, P3, P4, P5);
```



```cpp
// client
template<typename R, typename... Params>
value_t<R> call(std::string name, Params... ps) {
    using args_type = std::tuple<typename std::decay<Params>::type...>;
    args_type args = std::make_tuple(ps...);

    Serializer ds;
    ds << name;
    package_params(ds, args);
    return net_call<R>(ds);
}

template<typename R>
value_t<R> call(std::string name) {
    Serializer ds;
    ds << name;
    return net_call<R>(ds);
}
```

