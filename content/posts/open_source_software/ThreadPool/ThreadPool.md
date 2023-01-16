---
title: "开源软件:ThreadPool C++ 线程池"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [ThreadPool]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


# 源码及编译使用
源码地址：[ThreadPool](https://github.com/progschj/ThreadPool)
源码及编译cmake 文件 ： [ThreadPool]()

编译 
```
mkdir build
cd build
cmake ..
make 
```
运行：
```
./example
```

# 源码分析

## std::future

[future](http://www.cplusplus.com/reference/future/future/?kw=future)

std::future类模板定义头文件<future>,其函数声明如下：
```cpp
template <class T>  future;
template <class R&> future<R&>;     // specialization : T is a reference type (R&)
template <>         future<void>;   // specialization : T is void
```

对于future补充说明如下：

- std::async 、 std::packaged_task 或 std::promise 能提供一个std::future对象给该异步操作的创建者
- 异步操作的创建者能用各种方法查询、等待或从 std::future 提取值。若异步操作仍未提供值，则这些方法可能阻塞。
- 异步操作准备好发送结果给创建者时，它能通过接口（eg,std::promise::set_value std::future） 修改共享状态的值。


std::future使用
下面就用std::future对象来获取异步操作的结果，没有使用到全局变量，逻辑非常清晰，代码如下：
```cpp
// future example
#include <iostream>       // std::cout
#include <future>         // std::async, std::future
#include <chrono>         // std::chrono::milliseconds

// a non-optimized way of checking for prime numbers:
bool is_prime (int x) {
  for (int i=2; i<x; ++i) if (x%i==0) return false;
  return true;
}

int main ()
{
  // call function asynchronously:
  std::future<bool> fut = std::async (is_prime,444444443); 

  // do something while waiting for function to set future:
  std::cout << "checking, please wait";
  std::chrono::milliseconds span (100);
  while (fut.wait_for(span)==std::future_status::timeout)
    std::cout << '.' << std::flush;

  bool x = fut.get();     // retrieve return value

  std::cout << "\n444444443 " << (x?"is":"is not") << " prime.\n";

  return 0;
}

```