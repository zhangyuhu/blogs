
---
title: "cmake note"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "cmake 编程"
lightgallery: true
tags: [cmake]
categories: [cmake]
---

[TOC]



# CMake -D 定义

-DCMAKE_INSTALL_PREFIX= 指定安装路径，相当于 --prefix=

-DCMAKE_C_COMPILER= 指定 C 编译器，相当于 CC=

-DCMAKE_CXX_COMPILER=指定 CXX 编译器，相当于 CXX=



# CMake中条件判断语句

```cmake
if(<constant>)
　　...
else()
　　...
endif()
```

`<constant>`表示一个常量，其取值与对应逻辑值如下（不区分大小写）：

| 逻辑值 | 常量值                                                       |
| ------ | ------------------------------------------------------------ |
| true   | 1、ON、YES、TRUE、Y、非0数字                                 |
| false  | 0、OFF、NO、FALSE、N、IGNORE、NOTFOUNT、空字符串、-NOTFOUND结尾字符串 |

**变量**

```cmake
if(<variable>)
```

`<variable>`表示一个变量，变量取值不是上述定义的`false常量`时其逻辑值为`true`，否则逻辑值为`false`。

注：
- 宏参数不是变量，环境变量不可作为条件。
- 使用变量时，不要用${}表示变量，直接使用变量名即可。

**字符串**

  ```cmake
  if(<string>)
  ```
  `<string>`表示带引号的字符串。以下情况其逻辑值为`true`:

- 字符串内容为上述定义的true常量
- 策略 CMP0054 未设置为 NEW，并且字符串的值恰好是受 CMP0054 行为影响的变量名称。

## 逻辑运算


```cmake
if(NOT <condition>) # 非运算
if(<cond1> AND <cond2>) # 与运算
if(<cond1> OR <cond2>) #或运算
if((condition) AND (condition OR (condition))) # 有括号时，先计算括号的逻辑值
```

## 存在性判断



```cmake
if(COMMAND command-name) # true 给定参数是可调用的命令、宏、函数时
if(POLICY policy-id) # true 给定的策略存在时
if(TARGET target-name) # true 给定的目标参数是通过add_executable()、add_library() 或 add_custom_target() 命令创建并存在的
if(TEST test-name) # true 给定参数是通过add_test()创建并存在的
if(DEFINED <name>|CACHE{<name>}|ENV{<name>}) # true 如果变量、缓存、环境变量存在（其取值无关）
if(<variable|string> IN_LIST <variable>) # true 如果<variable|string>存在于列表变量<variable>中
```



```cmake
# 以下路径参数可以为相对路径或绝对路径，相对是指相对于当前执行cmake的路径。
if(EXISTS path-to-file-or-directory) # true 如果文件/目录存在，当参数是连接时表示连接指向的实体存在。
if(file1 IS_NEWER_THAN file2) # true file1比file2新
if(IS_DIRECTORY path-to-directory) # true 如果参数是目录
if(IS_SYMLINK file-name)# true 如果参数是一个链接
if(IS_ABSOLUTE path) # true 如果参数是绝对路径
```

## 正则匹配

如果`<variable|string>`匹配了正则表达式`regex`，则为`true`

`<variable|string>`可以是字符串，或者内容为字符串的变量。

```cmake
if(<variable|string> MATCHES regex)
```

## 数字比较

`<variable|string>`表示存储数字的变量，或合法的数字字符串（如 “512”）

```cmake
if(<variable|string> LESS <variable|string>)       # <
if(<variable|string> GREATER <variable|string>)     # >
if(<variable|string> EQUAL <variable|string>)      # ==
if(<variable|string> LESS_EQUAL <variable|string>)   # <=
if(<variable|string> GREATER_EQUAL <variable|string>) # >=
```

## 字符串比较

`<variable|string>`可以是字符串，或者内容为字符串的变量。

```cmake
if(<variable|string> STRLESS <variable|string>)       # <
if(<variable|string> STRGREATER <variable|string>)     # >
if(<variable|string> STREQUAL <variable|string>)      # ==
if(<variable|string> STRLESS_EQUAL <variable|string>)   # <=
if(<variable|string> STRGREATER_EQUAL <variable|string>) # >=
```



## 版本号比较

`<variable|string>`表示存储`版本号`的变量，或合法的`版本号`字符串（如 “3.1.4”）

```cmake
if(<variable|string> VERSION_LESS <variable|string>)       # <
if(<variable|string> VERSION_GREATER <variable|string>)     # >
if(<variable|string> VERSION_EQUAL <variable|string>)      # ==
if(<variable|string> VERSION_LESS_EQUAL <variable|string>)   # <=
if(<variable|string> VERSION_GREATER_EQUAL <variable|string>) # >=
```

