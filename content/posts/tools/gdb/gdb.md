---
title: "gdb:使用 gdb 调试"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "使用 gdb 调试"
lightgallery: true
tags: [gdb]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [GDB 概述](#gdb-%E6%A6%82%E8%BF%B0)
- [调试示例](#%E8%B0%83%E8%AF%95%E7%A4%BA%E4%BE%8B)
- [使用GDB](#%E4%BD%BF%E7%94%A8gdb)
    - [1. 编译程序](#1-%E7%BC%96%E8%AF%91%E7%A8%8B%E5%BA%8F)
    - [2. 启动GDB的方法](#2-%E5%90%AF%E5%8A%A8gdb%E7%9A%84%E6%96%B9%E6%B3%95)
- [常用指令](#%E5%B8%B8%E7%94%A8%E6%8C%87%E4%BB%A4)
    - [1. 查看代码](#1-%E6%9F%A5%E7%9C%8B%E4%BB%A3%E7%A0%81)
    - [2. 断点设置](#2-%E6%96%AD%E7%82%B9%E8%AE%BE%E7%BD%AE)
        - [2.1. 设置断点](#21-%E8%AE%BE%E7%BD%AE%E6%96%AD%E7%82%B9)
        - [2.2. 查看断点](#22-%E6%9F%A5%E7%9C%8B%E6%96%AD%E7%82%B9)
        - [2.3. 删除断点：](#23-%E5%88%A0%E9%99%A4%E6%96%AD%E7%82%B9)
        - [2.4. 禁用与启用断点](#24-%E7%A6%81%E7%94%A8%E4%B8%8E%E5%90%AF%E7%94%A8%E6%96%AD%E7%82%B9)
        - [2.5. 断点命令列表](#25-%E6%96%AD%E7%82%B9%E5%91%BD%E4%BB%A4%E5%88%97%E8%A1%A8)
    - [3. 调试](#3-%E8%B0%83%E8%AF%95)
        - [3.1. 启动](#31-%E5%90%AF%E5%8A%A8)
        - [3.2. 查看栈帧](#32-%E6%9F%A5%E7%9C%8B%E6%A0%88%E5%B8%A7)
        - [3.3. 输出当前值](#33-%E8%BE%93%E5%87%BA%E5%BD%93%E5%89%8D%E5%80%BC)
        - [3.4. 浏览以前的GDB命令](#34-%E6%B5%8F%E8%A7%88%E4%BB%A5%E5%89%8D%E7%9A%84gdb%E5%91%BD%E4%BB%A4)
        - [3.5. 运行](#35-%E8%BF%90%E8%A1%8C)
- [参考](#%E5%8F%82%E8%80%83)

<!-- /TOC -->

# GDB 概述

GDB是GNU开源组织发布的一个强大的UNIX下的程序调试工具。或许，各位比较喜欢那种图形界面方式的，像VC、BCB等IDE的调试，但如果你是在UNIX平台下做软件，你会发现GDB这个调试工具有比VC、BCB的图形化调试器更强大的功能。所谓“寸有所长，尺有所短”就是这个道理。

一般来说，GDB主要帮忙你完成下面四个方面的功能：

  1、启动你的程序，可以按照你的自定义的要求随心所欲的运行程序。
  2、可让被调试的程序在你所指定的调置的断点处停住。（断点可以是条件表达式）
  3、当程序被停住时，可以检查此时你的程序中所发生的事。
  4、动态的改变你程序的执行环境。

# 调试示例

**原程序** ：
gdb_test.c

```c
#include <stdio.h>

int func(int n)
{
    int sum = 0, i;
    for (i = 0; i < n; i++)
    {
        sum += i;
    }
    return sum;
}

main()
{
    int i;
    long result = 0;
    for (i = 1; i <= 100; i++)
    {
        result += i;
    }

    printf("result[1-100] = %d /n", result);
    printf("result[1-250] = %d /n", func(250));
}
```
编译 ： gcc -g gdb_test.c -o test

**使用GDB调试**：

```c
zyh@zyh gdb (master) $ gdb test  <---------- 启动GDB
GNU gdb (Ubuntu 8.1-0ubuntu3.2) 8.1.0.20180409-git
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from test...done.
(gdb) l     <-------------------- l命令相当于list，从第一行开始例出原码。
1        #include <stdio.h>
2
3        int func(int n)
4        {
5                int sum=0,i;
6                for(i=0; i<n; i++)
7                {
8                        sum+=i;
9                }
10               return sum;
(gdb)       <-------------------- 直接回车表示，重复上一次命令
11       }
12
13
14       main()
15       {
16               int i;
17               long result = 0;
18               for(i=1; i<=100; i++)
19               {
20                       result += i;
(gdb) break 16    <-------------------- 设置断点，在源程序第16行处。
Breakpoint 1 at 0x8048496: file tst.c, line 16.
(gdb) break func  <-------------------- 设置断点，在函数func()入口处。
Breakpoint 2 at 0x8048456: file tst.c, line 5.
(gdb) info break  <-------------------- 查看断点信息。
Num Type           Disp Enb Address    What
1   breakpoint     keep y   0x08048496 in main at tst.c:16
2   breakpoint     keep y   0x08048456 in func at tst.c:5
(gdb) r           <--------------------- 运行程序，run命令简写
Starting program: /home/hchen/test/tst
Breakpoint 1, main () at tst.c:17    <---------- 在断点处停住。
17               long result = 0;
(gdb) n          <--------------------- 单条语句执行，next命令简写。
18               for(i=1; i<=100; i++)
(gdb) n
20                       result += i;
(gdb) n
18               for(i=1; i<=100; i++)
(gdb) n
20                       result += i;
(gdb) c          <--------------------- 继续运行程序，continue命令简写。
Continuing.
result[1-100] = 5050       <----------程序输出。
Breakpoint 2, func (n=250) at tst.c:5
5                int sum=0,i;
(gdb) n
6                for(i=1; i<=n; i++)
(gdb) p i        <--------------------- 打印变量i的值，print命令简写。
$1 = 134513808
(gdb) n
8                        sum+=i;
(gdb) n
6                for(i=1; i<=n; i++)
(gdb) p sum
$2 = 1
(gdb) n
8                        sum+=i;
(gdb) p i
$3 = 2
(gdb) n
6                for(i=1; i<=n; i++)
(gdb) p sum
$4 = 3
(gdb) bt        <--------------------- 查看函数堆栈。
#0  func (n=250) at tst.c:5
#1  0x080484e4 in main () at tst.c:24
#2  0x400409ed in __libc_start_main () from /lib/libc.so.6
(gdb) finish    <--------------------- 退出函数。
Run till exit from #0  func (n=250) at tst.c:5
0x080484e4 in main () at tst.c:24
24              printf("result[1-250] = %d /n", func(250) );
Value returned is $6 = 31375
(gdb) c     <--------------------- 继续运行。
Continuing.
result[1-250] = 31375    <----------程序输出。
Program exited with code 027. <--------程序退出，调试结束。
(gdb) q     <--------------------- 退出gdb。
```



# 使用GDB

## 编译程序

一般来说GDB主要调试的是C/C++的程序。要调试C/C++的程序，首先在编译时，我们必须要把调试信息加到可执行文件中。使用编译器（cc/gcc/g++）的 -g 参数可以做到这一点。如：

```bash
> gcc -g hello.c -o hello
> g++ -g hello.cpp -o hello
```

如果没有-g，你将看不见程序的函数名、变量名，所代替的全是运行时的内存地址。当你用-g把调试信息加入之后，并成功编译目标代码以后，让我们来看看如何用gdb来调试他。



## 启动GDB的方法

1. gdb + ProgramName
   说明：ProgramName 也就是你的执行文件，一般在当前目录下。

2. gdb + ProgramName +core
   说明：用gdb同时调试一个运行程序和core文件，core是程序非法执行后core dump后产生的文件。

3. gdb + ProgramName +PID
   说明：如果你的程序是一个服务程序，那么你可以指定这个服务程序运行时的进程ID。gdb会自动attach上去，并调试他。program应该在PATH环境变量中搜索得到。

4. gdb -command=z x
    说明：在调用GDB时可以指定启动文件，如：$gdb-command=z x
    表示要在可执行文件x上运行GDB，首先要从文件z中读取命令。

    > gdb启动文件
>
    > 有时候我们在完成调试前可能需要退出GDB，比如需要离开比较长的一段时间而且不能保持登录在计算机中。为了不丢失某些信息，可以将断点和设置的其他命令放在一个GDB启动文件中，然后每次启动GDB时会自动加载它们。
>
    > GDB启动文件默认名为.gdbinit。可以将一个文件放在主目录中用于一般用途，另一个文件放在特定项目专用的目录中。例如，可以将设置断点的命令放在后一个目录的启动文件中，在主目录的.gdbinit文件中存储开发的一些通用的宏。最好不要将编程项目放在主目录中，因为不能将项目特有的信息放在.gdbinit中。

   GDB启动时，可以加上一些GDB的启动开关，详细的开关可以用gdb -help查看。下面只列举一些比较常用的参数：

   --symbols=SYMFILE
   从指定文件中读取符号表。

   --se=FILE
   从指定文件中读取符号表信息，并把他用在可执行文件中。

   --core=COREFILE
   调试时core dump的core文件。

   --directory=DIR
   加入一个源文件的搜索路径。默认搜索路径是环境变量中PATH所定义的路径。



# 常用指令

## 查看代码

list 命令可用于显示指定位置处的源代码。list命令会影响当前行和当前文件。list命令有多种方式指定要显示的源代码范围，可以是行号，函数名，甚至是指令地址。常用的如下：

> list linenum：显示指定行数附近的代码。
>
> list function：显示指定函数附近的代码。
>
> list + 列出当前行的后面代码行。
>
> list - 列出当前行的前面代码行。
>
> list ＊addr：显示指定地址附近的代码。默认情况下，GDB显示指定位置处以及其前后的10行代码，但是这是一个可设置的值。
>
> set listsize count：设置list命令显示的源代码数量最多为count行，0表示不限制行数。
>
> show listsize：显示listsize的值。
>

## 断点设置

### 设置断点

> break line_number：在某一行设置断点。
>
> break function（函数名）：在某个函数的入口（第一行可执行代码）处设置断点。
>
> break filename:function：在源代码文件filename的函数function入口处处设置断点。
>
> conditionbreak_p_num（断点编号） cond（条件）：将正常断点转变为条件断点
>
> 举例：condition 30  index == 5
>
> break line_num（行号） if cond（条件）：用break if可以将break和condition命令组合成一个步骤：举例：(gdb) break 30 if index == 5
>

### 查看断点

> info break：显示所有断点信息
>

### 删除断点：

> deletebreak_point_num:删除编号为break_point_num的断点；
>
> delete:删除所有断点；
>
> clear function:删除在function函数处设定的断点
>
> clearfilename:funtion删除在filename文件的function函数处设定的断点
>
> clear line_number:删除在line_number行设定的断点
>
> clearfilename:line_number:删除在filename文件的line_number行设定的断点
>

### 禁用与启用断点

> disablebreakpoint-list（是用空格分隔开的多个断点标识符）：禁用断点
>
> disable：禁用所有现存断点
>
> enablebreakpoint-list：启用断点
>
> enable oncebreakpoint-list：在下次引起GDB暂停执行后禁用
>

### 断点命令列表

让GDB在每次到达某个断点时自动执行一组命令，从而自动完成某一任务。

使用commands命令设置命令列表：

> commands breakpoint_number
>
> ...
>
> commands
>
> ...
>
> end
>

如果嫌GDB输出太冗长，可以使用silent命令，只需将其添加到设置的命令列表最开始处即可。

## 调试

### 启动
> run
>

### 查看栈帧

> frame num（栈帧编号）
>

注意栈帧编号规则，当前正在执行的函数的帧被编号为0，其父帧（即该函数的调用者的栈帧）被编号为1，父帧的父帧被编号为2，以此类推。

跳到调用栈中的下一个父帧：up

引向相反方向：down

显示整个栈，即当前存在的所有帧的集合 ：backtrace

### 输出当前值
> print或者p
>

### 浏览以前的GDB命令
> 上一个Ctrl+P、下一个Ctrl+N

可以直接按下回车再次执行最近执行过的那条命令

### 运行

- 单步执行：

> step（s）、next（n），仅执行到代码的下一行后再次暂停。
>

注意二者区别：在函数调用时step会进入函数，next导致下一次暂停出现在调用函数之后。next被称为单步越过（stepping over）函数，而step被称为单步进入（stepping into）函数。next和step都可以采用一个可选的数值参数，来表示要使用next或step执行的额外行数。

- 无条件恢复程序的执行：

> continue（c）
>

直到遇到另一个断点或者程序结束。continue可以接受一个可选的数值参数n，要求GDB忽略下面n个断点。

- 用finish（fin）或until（u）命令恢复

finish命令指示GDB恢复执行，直到恰好在当前帧完成之后为止。

until命令通常用来在不进一步在循环中暂停（除了循环中的中间断点）的情况下完成正在执行的循环。until会执行循环的其余部分（如果遇到断点，还是会暂停），让GDB在循环后面的第一行代码处暂停。

until命令也可以接受源代码中的位置作为参数，其用法与break命令同。


如果GDB触发了main函数入口处的一个断点，那么可以使用下面这些命令方便地使程序一直执行到swap()的入口：

> until 13
>
> until swap
>
> until swapflaw.c:13
>
> until swapflaw.c:swap
>

跳出循环

until NUM   执行到一个比当前行号大的行，或者也可以指定在当前frame（我理解成函数）中的某一行

跳出函数
finish      执行，直到选定的frame执行结束，然后打印返回值，将其放入历史值中，停止

- 退出

> quit(q)



# 参考

[【Linux】GDB用法详解(5小时快速教程)](http://witmax.cn/gdb-usage.html)