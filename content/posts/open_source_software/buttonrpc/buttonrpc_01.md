---
title: "开源软件:buttonrpc (代码分析)"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [buttonrpc]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [1. 开源地址](#1-开源地址)
- [2. 编译运行](#2-编译运行)
	- [2.1. buttonrpc](#21-buttonrpc)
	- [2.2. buttonrpc_cpp14](#22-buttonrpc_cpp14)
- [3. 原理](#3-原理)
	- [3.1. RPC](#31-rpc)
	- [3.2. buttonrpc 分析](#32-buttonrpc-分析)
		- [3.2.1. **Call ID映射**：buttonrpc.hpp](#321-call-id映射buttonrpchpp)
		- [3.2.2. **序列化和反序列化**：Serializer.hpp](#322-序列化和反序列化serializerhpp)
		- [3.2.3. **网络传输**：ZeroMQ](#323-网络传输zeromq)

<!-- /TOC -->

# 1. 开源地址

[buttonrpc](https://github.com/button-chen/buttonrpc)

[buttonrpc_cpp14](https://github.com/button-chen/buttonrpc_cpp14)

# 2. 编译运行

## 2.1. buttonrpc

1. 依赖安装：

```shell
sudo apt-get install libzmq3-dev
```

2. 编译：

ubuntu 下修改文件

```shell
diff --git a/example/main_client.cpp b/example/main_client.cpp
index abdec37..fab93c5 100644
--- a/example/main_client.cpp
+++ b/example/main_client.cpp
@@ -3,7 +3,11 @@
 #include <ctime>
 #include "buttonrpc.hpp"
 
+#ifdef _WIN32
 #include <Windows.h>  // use sleep
+#else
+ #include <unistd.h>
+#endif
 
 
 #define buttont_assert(exp) { \
@@ -64,7 +68,11 @@ int main()
                buttonrpc::value_t<void> xx = client.call<void>("foo_7", 666);
                buttont_assert(!xx.valid());
 
+#ifdef _WIN32
                Sleep(1000);
+#else
+                        sleep(1);
+#endif
        }
 
        return 0;
```

创建CMakeLists 并编译

```shell
cd buttonrpc
touch CMakeLists.txt # 内容见下
mkdir build
cd build
cmake ..
make 
```

```cmake
# 这个是cmake最小版本要求
cmake_minimum_required(VERSION 3.1)

# Enable C++11，这一段是源码里的
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

# 填写你的项目名称Project
project( RPC )

aux_source_directory(${CMAKE_SOURCE_DIR}/src/ DIR_SRCS)
aux_source_directory(${CMAKE_SOURCE_DIR}/example DIR_SRCS)

include_directories(
    ${CMAKE_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/src
)

message("${CMAKE_CURRENT_SOURCE_DIR}")

# Find all *.cpp files and store in list cpps
# GLOB这个参数不支持子目录， 用GLOB_RECURSE可以支持子目录。
file(GLOB cpps RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}/example" "${CMAKE_CURRENT_SOURCE_DIR}/example/*.cpp")

foreach(mainfile IN LISTS cpps)
    # Get file name without directory
    get_filename_component(mainname ${mainfile} NAME_WE)
    add_executable(${mainname} ${CMAKE_CURRENT_SOURCE_DIR}/example/${mainfile})
    target_link_libraries(${mainname} zmq)
endforeach()
```

3. 运行demo 

```shell
./main_server
./main_client
```

## 2.2. buttonrpc_cpp14

参考 buttonrpc



# 3. 原理

## 3.1. RPC

RPC（Remote Procedure Call）远程过程调用。要像调用本地的函数一样去调远程函数。

RPC 是需要有三个主要模块：**Call ID映射**，**序列化和反序列化**，**网络传输**。

**Call ID映射**。我们怎么告诉远程机器我们要调用Multiply，而不是Add或者FooBar呢？在本地调用中，函数体是直接通过函数指针来指定的，我们调用Multiply，编译器就自动帮我们调用它相应的函数指针。但是在远程调用中，函数指针是不行的，因为两个进程的地址空间是完全不一样的。所以，在RPC中，所有的函数都必须有自己的一个ID。这个ID在所有进程中都是唯一确定的。客户端在做远程过程调用时，必须附上这个ID。然后我们还需要在客户端和服务端分别维护一个 {函数 <--> Call ID} 的对应表。两者的表不一定需要完全相同，但相同的函数对应的Call ID必须相同。当客户端需要进行远程调用时，它就查一下这个表，找出相应的Call ID，然后把它传给服务端，服务端也通过查表，来确定客户端需要调用的函数，然后执行相应函数的代码。

**序列化和反序列化**。客户端怎么把参数值传给远程的函数呢？在本地调用中，我们只需要把参数压到栈里，然后让函数自己去栈里读就行。但是在远程过程调用时，客户端跟服务端是不同的进程，不能通过内存来传递参数。甚至有时候客户端和服务端使用的都不是同一种语言（比如服务端用C++，客户端用Java或者Python）。这时候就需要客户端把参数先转成一个字节流，传给服务端后，再把字节流转成自己能读取的格式。这个过程叫序列化和反序列化。同理，从服务端返回的值也需要序列化反序列化的过程。

**网络传输**。远程调用往往用在网络上，客户端和服务端是通过网络连接的。所有的数据都需要通过网络传输，因此就需要有一个网络传输层。网络传输层需要把Call ID和序列化后的参数字节流传给服务端，然后再把序列化后的调用结果传回客户端。只要能完成这两者的，都可以作为传输层使用。因此，它所使用的协议其实是不限的，能完成传输就行。尽管大部分RPC框架都使用TCP协议，但其实UDP也可以，而gRPC干脆就用了HTTP2。Java的Netty也属于这层的东西。

## 3.2. buttonrpc 分析

### 3.2.1. **Call ID映射**：buttonrpc.hpp

**server**

server 中主要完成被调用函数的注册,在收到网络发送的数据进行序列化还原后找到对应的调用函数和参数,执行调用再将结果返回.

```c++
// server
template<typename F>
void bind(std::string name, F func);

template<typename F, typename S>
void bind(std::string name, F func, S* s);
```

bind 函数两种模板的实现是为了兼容多参数函数的设计,其中 name 为函数名,同时也是对应的函数的索引,func 为函数指针,s 为函数参数.bind 的实现如下:

```c++
template<typename F>
void buttonrpc::bind( std::string name, F func )
{
	m_handlers[name] = std::bind(&buttonrpc::callproxy<F>, this, func, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3);
}

template<typename F, typename S>
inline void buttonrpc::bind(std::string name, F func, S* s)
{
	m_handlers[name] = std::bind(&buttonrpc::callproxy<F, S>, this, func, s, std::placeholders::_1, std::placeholders::_2, std::placeholders::_3);
}
```

函数最终被放到m_handlers 中索引,m_handlers的类型如下:

```c++
std::map<std::string, std::function<void(Serializer*, const char*, int)>> m_handlers;
```

映射关系为 函数名(string) --- 函数指针

```c++
template<typename F>
void buttonrpc::callproxy( F fun, Serializer* pr, const char* data, int len )
{
	callproxy_(fun, pr, data, len);
}

template<typename F, typename S>
inline void buttonrpc::callproxy(F fun, S * s, Serializer * pr, const char * data, int len)
{
	callproxy_(fun, s, pr, data, len);
}
```

callproxy_ 实现如下:

```c++
template<typename R>
void buttonrpc::callproxy_(std::function<R()> func, Serializer* pr, const char* data, int len)
{
	typename type_xx<R>::type r = call_helper<R>(std::bind(func));

	value_t<R> val;
	val.set_code(RPC_ERR_SUCCESS);
	val.set_val(r);
	(*pr) << val;
}
/*-------------------------------------------------------------------------------------------*/
template<typename F>
void callproxy(F fun, Serializer* pr, const char* data, int len);

template<typename F, typename S>
void callproxy(F fun, S* s, Serializer* pr, const char* data, int len);

// PROXY FUNCTION POINT
template<typename R>
void callproxy_(R(*func)(), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R()>(func), pr, data, len);
}

template<typename R, typename P1>
void callproxy_(R(*func)(P1), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1)>(func), pr, data, len);
}

template<typename R, typename P1, typename P2>
void callproxy_(R(*func)(P1, P2), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2)>(func), pr, data, len);
}

template<typename R, typename P1, typename P2, typename P3>
void callproxy_(R(*func)(P1, P2, P3), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3)>(func), pr, data, len);
}

template<typename R, typename P1, typename P2, typename P3, typename P4>
void callproxy_(R(*func)(P1, P2, P3, P4), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3, P4)>(func), pr, data, len);
}

template<typename R, typename P1, typename P2, typename P3, typename P4, typename P5>
void callproxy_(R(*func)(P1, P2, P3, P4, P5), Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3, P4, P5)>(func), pr, data, len);
}

// PROXY CLASS MEMBER
template<typename R, typename C, typename S>
void callproxy_(R(C::* func)(), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R()>(std::bind(func, s)), pr, data, len);
}

template<typename R, typename C, typename S, typename P1>
void callproxy_(R(C::* func)(P1), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1)>(std::bind(func, s, std::placeholders::_1)), pr, data, len);
}

template<typename R, typename C, typename S, typename P1, typename P2>
void callproxy_(R(C::* func)(P1, P2), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2)>(std::bind(func, s, std::placeholders::_1, std::placeholders::_2)), pr, data, len);
}

template<typename R, typename C, typename S, typename P1, typename P2, typename P3>
void callproxy_(R(C::* func)(P1, P2, P3), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3)>(std::bind(func, s, 
                                                      std::placeholders::_1, std::placeholders::_2, std::placeholders::_3)), pr, data, len);
}

template<typename R, typename C, typename S, typename P1, typename P2, typename P3, typename P4>
void callproxy_(R(C::* func)(P1, P2, P3, P4), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3, P4)>(std::bind(func, s,
                                                          std::placeholders::_1, std::placeholders::_2, std::placeholders::_3, std::placeholders::_4)), pr, data, len);
}

template<typename R, typename C, typename S, typename P1, typename P2, typename P3, typename P4, typename P5>
void callproxy_(R(C::* func)(P1, P2, P3, P4, P5), S* s, Serializer* pr, const char* data, int len) {
    callproxy_(std::function<R(P1, P2, P3, P4, P5)>(std::bind(func, s,
                                                              std::placeholders::_1, std::placeholders::_2, std::placeholders::_3, std::placeholders::_4, std::placeholders::_5)), pr, data, len);
}

// PORXY FUNCTIONAL
template<typename R>
void callproxy_(std::function<R()>, Serializer* pr, const char* data, int len);

template<typename R, typename P1>
void callproxy_(std::function<R(P1)>, Serializer* pr, const char* data, int len);

template<typename R, typename P1, typename P2>
void callproxy_(std::function<R(P1, P2)>, Serializer* pr, const char* data, int len);

template<typename R, typename P1, typename P2, typename P3>
void callproxy_(std::function<R(P1, P2, P3)>, Serializer* pr, const char* data, int len);

template<typename R, typename P1, typename P2, typename P3, typename P4>
void callproxy_(std::function<R(P1, P2, P3, P4)>, Serializer* pr, const char* data, int len);

template<typename R, typename P1, typename P2, typename P3, typename P4, typename P5>
void callproxy_(std::function<R(P1, P2, P3, P4, P5)>, Serializer* pr, const char* data, int len);
```

call_helper 用来辅助判断调用函数是否需要返回值,如果调用函数是void 类型,返回值 默认为 0 ,其实现如下:

```c++
template<typename R, typename F>
typename std::enable_if<std::is_same<R, void>::value, typename type_xx<R>::type >::type call_helper(F f) {
	f();
	return 0;
}

template<typename R, typename F>
typename std::enable_if<!std::is_same<R, void>::value, typename type_xx<R>::type >::type call_helper(F f) {
	return f();
}
```



**client**

```c++
// client
template<typename R>
value_t<R> call(std::string name);
	
template<typename R>
inline buttonrpc::value_t<R> buttonrpc::call(std::string name)
{
	Serializer ds;
	ds << name;
	return net_call<R>(ds);
}
```

这里的 调用函数为了兼容有多参数函数的设计,重载了很多的类型,如下:

```c++
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

其实现的目的都是一样:将函数名(string)和参数进行序列化,然后调用网络发送模块.

启动调用函数的索引为 函数名 (string). 如下:

```c++
// 处理函数相关
Serializer* buttonrpc::call_(std::string name, const char* data, int len)
{
	Serializer* ds = new Serializer();
	if (m_handlers.find(name) == m_handlers.end()) {
		(*ds) << value_t<int>::code_type(RPC_ERR_FUNCTIION_NOT_BIND);
		(*ds) << value_t<int>::msg_type("function not bind: " + name);
		return ds;
	}
	auto fun = m_handlers[name];
	fun(ds, data, len);
	ds->reset();
	return ds;
}
```

m_handlers 中存储了所有的函数映射.




### 3.2.2. **序列化和反序列化**：Serializer.hpp

**重载  `<<`  `>>`** 

```c++
template<typename T>
Serializer &operator >> (T& i){
    output_type(i); 
    return *this;
}

template<typename T>
Serializer &operator << (T i){
    input_type(i);
    return *this;
}
```

重载 << >> 并调用 input_type  output_type 将数据转化为数据流(char类型)用于网络传输

**output_type & input_type**

```c++
template<typename T>
inline void Serializer::output_type(T& t)
{
...
}

template<>
inline void Serializer::output_type(std::string& in)
{
...
}

template<typename T>
inline void Serializer::input_type(T t)
{
...
}

template<>
inline void Serializer::input_type(std::string in)
{
...
}

template<>
inline void Serializer::input_type(const char* in)
{
...
}
```

这里的 input_type  output_type  提供了多种的特化和偏特化的实现同样是为了有多参数函数的序列化的需求.

### 3.2.3. **网络传输**：ZeroMQ

```c++
// network
void buttonrpc::as_client( std::string ip, int port )
{
	m_role = RPC_CLIENT;
	m_socket = new zmq::socket_t(m_context, ZMQ_REQ);
	ostringstream os;
	os << "tcp://" << ip << ":" << port;
	m_socket->connect (os.str());
}

void buttonrpc::as_server( int port )
{
	m_role = RPC_SERVER;
	m_socket = new zmq::socket_t(m_context, ZMQ_REP);
	ostringstream os;
	os << "tcp://*:" << port;
	m_socket->bind (os.str());
}

void buttonrpc::send( zmq::message_t& data )
{
	m_socket->send(data);
}

void buttonrpc::recv( zmq::message_t& data )
{
	m_socket->recv(&data);
}

inline void buttonrpc::set_timeout(uint32_t ms)
{
	// only client can set
	if (m_role == RPC_CLIENT) {
		m_socket->setsockopt(ZMQ_RCVTIMEO, ms);
	}
}

void buttonrpc::run()
{
	// only server can call
	if (m_role != RPC_SERVER) {
		return;
	}
	while (1){
		zmq::message_t data;
		recv(data);
		StreamBuffer iodev((char*)data.data(), data.size());
		Serializer ds(iodev);

		std::string funname;
		ds >> funname;
		Serializer* r = call_(funname, ds.current(), ds.size()- funname.size());

		zmq::message_t retmsg (r->size());
		memcpy (retmsg.data (), r->data(), r->size());
		send(retmsg);
		delete r;
	}
}
```

网络传输使用的 ZeroMQ .
