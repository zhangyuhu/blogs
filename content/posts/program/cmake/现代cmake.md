---
title: "现代CMAKE"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "cmake 编程"
lightgallery: true
tags: [cmake]
categories: [cmake]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。



[Modern CMake 简体中文版](https://modern-cmake-cn.github.io/Modern-CMake-zh_CN/)

[CMake菜谱（CMake Cookbook中文版）](https://www.bookstack.cn/books/CMake-Cookbook)

[ARM SIMD 指令集：NEON 简介](https://blog.csdn.net/mzpmzk/article/details/114686930)



#  现代的 CMake

 现代的 CMake 。是 CMake 3.4+ ，甚至是 CMake 3.21+ ！ 它简洁、强大、优雅，所以你能够花费你的大部分时间在编写代码上，而不是在一个不可读、不可维护的 Make （或 CMake 2） 文件上浪费时间。 并且 CMake 3.11+ 的构建速度应该也会更加的快！！！



# 运行 CMake

## 设置选项

在 CMake 中，你可以使用 `-D` 设置选项。你能使用 `-L` 列出所有选项，或者用 `-LH` 列出人类更易读的选项列表。



## 详细和部分的构建

我们已经提到了在构建时可以有详细输出，但你也可以看到详细的 CMake 配置输出。`--trace` 选项能够打印出运行的 CMake 的每一行。由于它过于冗长，CMake 3.7 添加了 `--trace-source="filename"` 选项，这让你可以打印出你想看的特定文件运行时执行的每一行。如果你选择了要调试的文件的名称（在调试一个 CMakeLists.txt 时通常选择父目录，因为它们名字都一样），你就会只看到这个文件里运行的那些行。这很实用！

```cmake
cmake --trace --trace-source="filename" ..
make VERBOSE=1
```



## 选项

CMake 支持缓存选项。CMake 中的变量可以被标记为 "cached"，这意味着它会被写入缓存（构建目录中名为 `CMakeCache.txt` 的文件）。你可以在命令行中用 `-D` 预先设定（或更改）缓存选项的值。CMake 查找一个缓存的变量时，它就会使用已有的值并且不会覆盖这个值。

### 标准选项

大部分软件包中都会用到以下的 CMake 选项：

- `-DCMAKE_BUILD_TYPE=` 从 Release， RelWithDebInfo， Debug， 或者可能存在的更多参数中选择。
- `-DCMAKE_INSTALL_PREFIX=` 这是安装位置。UNIX 系统默认的位置是 `/usr/local`，用户目录是 `~/.local`，也可以是你自己指定的文件夹。
- `-DBUILD_SHARED_LIBS=` 你可以把这里设置为 `ON` 或 `OFF` 来控制共享库的默认值（不过，你也可以明确选择其他值而不是默认值）
- `-DBUILD_TESTING=` 这是启用测试的通用名称，当然不会所有软件包都会使用它，有时这样做确实不错。



## CMake 行为准则(Do's and Don'ts)



# 基础知识简介

## 最低版本要求

```cmake
cmake_minimum_required(VERSION 3.1)
```

从 CMake 3.12 开始，版本号可以声明为一个范围，例如 `VERSION 3.1...3.15`；这意味着这个工程最低可以支持 `3.1` 版本，但是也最高在 `3.15` 版本上测试成功过。

```cmake
cmake_minimum_required(VERSION 3.7)

if(${CMAKE_VERSION} VERSION_LESS 3.21)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else()
    cmake_policy(VERSION 3.21)
endif()
```



## 设置一个项目

```cmake
project(MyProject VERSION 1.0
                  DESCRIPTION "Very nice project"
                  LANGUAGES CXX)
```

这里的字符串是带引号的，因此内容中可以带有空格。项目名称是这里的第一个参数。所有的关键字参数都可选的。`VERSION` 设置了一系列变量，例如 `MyProject_VERSION` 和 `PROJECT_VERSION`。语言可以是 `C`,`CXX`,`Fortran`,`ASM`,`CUDA`(CMake 3.8+),`CSharp`(3.8+),`SWIFT`(CMake 3.15+ experimental)，默认是`C CXX`。在 CMake 3.9，可以通过`DESCRIPTION` 关键词来添加项目的描述。这个关于 [`project`](https://cmake.org/cmake/help/latest/command/project.html) 的文档可能会有用。





## 生成一个可执行文件

```cmake
add_executable(one two.cpp three.h)
```

`one` 既是生成的可执行文件的名称，也是创建的 `CMake` 目标(target)的名称。紧接着的是源文件的列表，你想列多少个都可以。CMake 很聪明 ，它根据拓展名只编译源文件。在大多数情况下，头文件将会被忽略；列出他们的唯一原因是为了让他们在 IDE 中被展示出来，目标文件在许多 IDE 中被显示为文件夹。你可以在 [buildsystem](https://cmake.org/cmake/help/latest/manual/cmake-buildsystem.7.html) 中找到更多关于一般构建系统与目标的信息。



## 生成一个库

```cmake
add_library(one STATIC two.cpp three.h)
```

你可以选择库的类型，可以是 `STATIC`,`SHARED`, 或者`MODULE`.如果你不选择它，CMake 将会通过`BUILD_SHARED_LIBS` 的值来选择构建 STATIC 还是 SHARED 类型的库。



## 目标时常伴随着你

```cmake
target_include_directories(one PUBLIC include)
```

[`target_include_directories`](https://cmake.org/cmake/help/latest/command/target_include_directories.html) 为目标添加了一个目录。 `PUBLIC` 对于一个二进制目标没有什么含义；但对于库来说，它让 CMake 知道，任何链接到这个目标的目标也必须包含这个目录。其他选项还有 `PRIVATE`（只影响当前目标，不影响依赖），以及 `INTERFACE`（只影响依赖）。

接下来我们可以将目标之间链接起来：

```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

[`target_link_libraries`](https://cmake.org/cmake/help/latest/command/target_link_libraries.html) 可能是 CMake 中最有用也最令人迷惑的命令。它指定一个目标，并且在给出目标的情况下添加一个依赖关系。如果不存在名称为 `one` 的目标，那他会添加一个链接到你路径中 `one` 库（这也是命令叫 `target_link_libraries` 的原因）。或者你可以给定一个库的完整路径，或者是链接器标志。最后再说一个有些迷惑性的知识：），经典的 CMake 允许你省略 `PUBLIC` 关键字，但是你在目标链中省略与不省略混用，那么 CMake 会报出错误。

只要记得在任何使用目标的地方都指定关键字，那么就不会有问题。

目标可以有包含的目录、链接库（或链接目标）、编译选项、编译定义、编译特性等等。正如你将在之后的两个项目章节中看到的，你经常可以得到目标（并且经常是指定目标）来代表所有你使用的库。甚至有些不是真正的库，像 `OpenMP`，就可以用目标来表示。这也是为什么现代 CMake 如此的棒！



## 更进一步

```cmake
cmake_minimum_required(VERSION 3.8)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)
```



# 为 CMake 项目添加特性

## 默认的构建类型

```cmake
set(default_build_type "Release")
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
  set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
      STRING "Choose the type of build." FORCE)
  # Set the possible values of build type for cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
    "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()
```





# 为 CMake 项目添加选项

## 地址无关代码(Position independent code)

用标志 `-fPIC` 来设置[这个](https://cmake.org/cmake/help/latest/variable/CMAKE_POSITION_INDEPENDENT_CODE.html)是最常见的。大部分情况下，你不需要去显式的声明它的值。CMake 将会在 `SHARED` 以及 `MODULE` 类型的库中自动的包含此标志。如果你需要显式的声明，可以这么写：

```cmake
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
```

这样会对全局的目标进行此设置，或者可以这么写：

```cmake
set_target_properties(lib1 PROPERTIES POSITION_INDEPENDENT_CODE ON)
```

来对某个目标进行设置是否开启此标志。

## Little libraries

如果你需要链接到 `dl` 库，在 Linux 上可以使用 `-ldl` 标志，不过在 CMake 中只需要在 `target_link_libraries` 命令中使用内置的 CMake 变量 [`${CMAKE_DL_LIBS}` ](https://cmake.org/cmake/help/latest/variable/CMAKE_DL_LIBS.html)。这里不需要模组或者使用 `find_package` 来寻找它。（这个命令包含了调用 `dlopen` 与 `dlclose` 的一切依赖）

不幸的是，想要链接到数学库没这么简单。如果你需要明确地链接到它，你可以使用 `target_link_libraries(MyTarget PUBLIC m)`，但是使用 CMake 通用的 [`find_library`](https://cmake.org/cmake/help/latest/command/find_library.html) 可能更好，如下是一个例子：

```cmake
find_library(MATH_LIBRARY m)
if(MATH_LIBRARY)
    target_link_libraries(MyTarget PUBLIC ${MATH_LIBRARY})
endif()
```

通过快速搜索，你可以很容易地找到这个和其他你需要的库的 `Find*.cmake` 文件，大多数主要软件包都具有这个 CMake 模组的辅助库



## 程序间优化(Interprocedural optimization)

[`INTERPROCEDURAL_OPTIMIZATION`](https://cmake.org/cmake/help/latest/prop_tgt/INTERPROCEDURAL_OPTIMIZATION.html)，最有名的是 *链接时间优化* 以及 `-flto` 标志，这在最新的几个 CMake 版本中可用。你可以通过变量 [`CMAKE_INTERPROCEDURAL_OPTIMIZATION`](https://cmake.org/cmake/help/latest//CMAKE_INTERPROCEDURAL_OPTIMIZATION.html)（ CMake 3.9+ 可用）或对目标指定 [`INTERPROCEDURAL_OPTIMIZATION`](https://cmake.org/cmake/help/latest/prop_tgt/INTERPROCEDURAL_OPTIMIZATION.html) 属性来打开它。在 CMake 3.8 中添加了对 GCC 及 Clang 的支持。如果你设置了 `cmake_minimum_required(VERSION 3.9)` 或者更高的版本（参考 [CMP0069](https://cmake.org/cmake/help/latest/policy/CMP0069.html)），当在编译器不支持 [`INTERPROCEDURAL_OPTIMIZATION`](https://cmake.org/cmake/help/latest/prop_tgt/INTERPROCEDURAL_OPTIMIZATION.html) 时，通过变量或属性启用该优化会产生报错。你可以使用内置模块 [CheckIPOSupported](https://cmake.org/cmake/help/latest/module/CheckIPOSupported.html) 中的 `check_ipo_supported()` 来检查编译器是否支持 IPO 。下面是基于 CMake 3.9 的一个例子：

```cmake
include(CheckIPOSupported)
check_ipo_supported(RESULT result)
if(result)
  set_target_properties(foo PROPERTIES INTERPROCEDURAL_OPTIMIZATION TRUE)
endif()
```



# CCache 和一些其他的实用工具



# CMake 中一些有用的模组

