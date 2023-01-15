---
title: "bash 编程笔记 (一)"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "bash 编程笔记"
lightgallery: true
tags: [bash]
categories: [bash]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [第一部分 bash简介](#%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86-bash%E7%AE%80%E4%BB%8B)
- [第二部分 bash示例和书写流程](#%E7%AC%AC%E4%BA%8C%E9%83%A8%E5%88%86-bash%E7%A4%BA%E4%BE%8B%E5%92%8C%E4%B9%A6%E5%86%99%E6%B5%81%E7%A8%8B)
- [第三部分 bash基础语法](#%E7%AC%AC%E4%B8%89%E9%83%A8%E5%88%86-bash%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95)
    - [1. 条件判断](#1-%E6%9D%A1%E4%BB%B6%E5%88%A4%E6%96%AD)
        - [1.1. test判断语句](#11-test%E5%88%A4%E6%96%AD%E8%AF%AD%E5%8F%A5)
        - [1.2. []条件判断](#12-%E6%9D%A1%E4%BB%B6%E5%88%A4%E6%96%AD)
        - [1.3. 测试逻辑表达式](#13-%E6%B5%8B%E8%AF%95%E9%80%BB%E8%BE%91%E8%A1%A8%E8%BE%BE%E5%BC%8F)
    - [2. if then else语句](#2-if-then-else%E8%AF%AD%E5%8F%A5)
    - [3. case语句](#3-case%E8%AF%AD%E5%8F%A5)
    - [4. for循环](#4-for%E5%BE%AA%E7%8E%AF)
    - [5. until循环](#5-until%E5%BE%AA%E7%8E%AF)
    - [6. while循环](#6-while%E5%BE%AA%E7%8E%AF)
    - [7. 使用break和continue控制循环](#7-%E4%BD%BF%E7%94%A8break%E5%92%8Ccontinue%E6%8E%A7%E5%88%B6%E5%BE%AA%E7%8E%AF)
- [第四部分 bash数组](#%E7%AC%AC%E5%9B%9B%E9%83%A8%E5%88%86-bash%E6%95%B0%E7%BB%84)
    - [1. 数组定义](#1-%E6%95%B0%E7%BB%84%E5%AE%9A%E4%B9%89)
    - [2. 数组操作](#2-%E6%95%B0%E7%BB%84%E6%93%8D%E4%BD%9C)
- [第五部分 函数](#%E7%AC%AC%E4%BA%94%E9%83%A8%E5%88%86-%E5%87%BD%E6%95%B0)
    - [1. 函数定义](#1-%E5%87%BD%E6%95%B0%E5%AE%9A%E4%B9%89)
    - [2. 函数调用和传参](#2-%E5%87%BD%E6%95%B0%E8%B0%83%E7%94%A8%E5%92%8C%E4%BC%A0%E5%8F%82)
    - [3. 函数返回值](#3-%E5%87%BD%E6%95%B0%E8%BF%94%E5%9B%9E%E5%80%BC)
    - [4. 应用实例](#4-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B)
- [第六部分 数值运算](#%E7%AC%AC%E5%85%AD%E9%83%A8%E5%88%86-%E6%95%B0%E5%80%BC%E8%BF%90%E7%AE%97)
    - [1. 应用实例一：分别用上面四种方式实现"3*5+2"。](#1-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B%E4%B8%80%E5%88%86%E5%88%AB%E7%94%A8%E4%B8%8A%E9%9D%A2%E5%9B%9B%E7%A7%8D%E6%96%B9%E5%BC%8F%E5%AE%9E%E7%8E%B0352)
    - [2. 应用实例二：分别勇上面四种方式实现“数值+1”。](#2-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B%E4%BA%8C%E5%88%86%E5%88%AB%E5%8B%87%E4%B8%8A%E9%9D%A2%E5%9B%9B%E7%A7%8D%E6%96%B9%E5%BC%8F%E5%AE%9E%E7%8E%B0%E6%95%B0%E5%80%BC1)
    - [3. 应用实例三：计算1/3,保留3位小数。](#3-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B%E4%B8%89%E8%AE%A1%E7%AE%9713%E4%BF%9D%E7%95%993%E4%BD%8D%E5%B0%8F%E6%95%B0)
- [第七部分 字符运算](#%E7%AC%AC%E4%B8%83%E9%83%A8%E5%88%86-%E5%AD%97%E7%AC%A6%E8%BF%90%E7%AE%97)
    - [1. 字符串定义](#1-%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%AE%9A%E4%B9%89)
    - [2. 字符串操作](#2-%E5%AD%97%E7%AC%A6%E4%B8%B2%E6%93%8D%E4%BD%9C)
        - [2.1. string操作公式表](#21-string%E6%93%8D%E4%BD%9C%E5%85%AC%E5%BC%8F%E8%A1%A8)
        - [2.2. 应用实例](#22-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B)
- [第八部分 bash自带参数](#%E7%AC%AC%E5%85%AB%E9%83%A8%E5%88%86-bash%E8%87%AA%E5%B8%A6%E5%8F%82%E6%95%B0)
- [第九部分 bash调试](#%E7%AC%AC%E4%B9%9D%E9%83%A8%E5%88%86-bash%E8%B0%83%E8%AF%95)
    - [1. bash命令调试](#1-bash%E5%91%BD%E4%BB%A4%E8%B0%83%E8%AF%95)
    - [2. echo调试](#2-echo%E8%B0%83%E8%AF%95)
    - [3. printf](#3-printf)
- [第十部分 bash注释](#%E7%AC%AC%E5%8D%81%E9%83%A8%E5%88%86-bash%E6%B3%A8%E9%87%8A)
    - [1. 单行注释](#1-%E5%8D%95%E8%A1%8C%E6%B3%A8%E9%87%8A)
    - [2. 多行注释](#2-%E5%A4%9A%E8%A1%8C%E6%B3%A8%E9%87%8A)
- [第十一部分 bash内建指令](#%E7%AC%AC%E5%8D%81%E4%B8%80%E9%83%A8%E5%88%86-bash%E5%86%85%E5%BB%BA%E6%8C%87%E4%BB%A4)
    - [1. 内建命令查看方法](#1-%E5%86%85%E5%BB%BA%E5%91%BD%E4%BB%A4%E6%9F%A5%E7%9C%8B%E6%96%B9%E6%B3%95)
    - [2. 常用内建命令说明](#2-%E5%B8%B8%E7%94%A8%E5%86%85%E5%BB%BA%E5%91%BD%E4%BB%A4%E8%AF%B4%E6%98%8E)

<!-- /TOC -->


# 第一部分 bash简介

在介绍bash之前，需要先了解它的起源——shell。

shell俗称壳，它是指UNIX系统下的一个命令解析器；主要用于用户和系统的交互。
UNIX系统上有很多种Shell。首个shell，即Bourne Shell，于1978年在V7(AT&T的第7版)UNIX上推出。后来，又演变出C shell、bash等不同版本的shell。

bash，全称为Bourne-Again Shell。它是一个为GNU项目编写的Unix shell。bash脚本功能非常强大，尤其是在处理自动循环或大的任务方面可节省大量的时间。bash是许多Linux平台的内定Shell，这也是我们介绍它主要的原因。


# 第二部分 bash示例和书写流程

**1 新建文件test.sh**

    $ touch test.sh

**2 添加可执行权限**

    $ chmod +x test.sh

**3 编辑test.sh，test.sh内容如下：**

    #!/bin/bash

    echo "hello bash"

    exit 0

说明：
(01) #!/bin/bash : 它是bash文件声明语句，表示是以/bin/bash程序执行该文件。它必须写在文件的第一行！
(02) echo "hello bash" : 表示在终端输出“hello bash”
(03) exit 0 : 表示返回0。在bash中，0表示执行成功，其他表示失败。

**4 执行bash脚本**

    $ ./test.sh

在终端输出“bash hello”


# 第三部分 bash基础语法

## 1. 条件判断

条件判断有2中格式，分别是“test EXPRESSION”和“[ EXPRESSION ]”。

“test”判断语句，在实际中应用的比较少；相反的，“[]”判断语句应用很广。下面分别对它们进行介绍

### 1.1 test判断语句

**基本格式**

    test EXPRESSION

**格式说明**

test是关键字，表示判断；
EXPRESSION是被判断的语句。

**应用实例**

| 表达式       | 说明                                |
| ------------ | ----------------------------------- |
| test -d File | 判断File是不是"目录"                |
| test -f File | 判断File是不是"普通文件"            |
| test -L File | 判断File是不是"符号链接"            |
| test -r File | 判断File是不是"可读"                |
| test -s File | 判断File是不是"文件长度大于0、非空" |
| test -w File | 判断File是不是"可写"                |
| test -u File | 判断File是不是"有suid位设置"        |
| test -x File | 判断File是不是"可执行"              |


### 1.2 []条件判断

**基本格式**

    [ EXPRESSION ]

**格式说明**

中括号的左右扩弧和EXPRESSION之间都必须有空格！

**应用实例**


| 表达式                    | 说明                                      |
| ------------------------- | ----------------------------------------- |
| [ (expr) ]                | expr为真                                  |
| [ !expr ]                 | expr为假                                  |
| [ expr1 -a expr2 ]        | expr1和expr2同时为真                      |
| [ expr1 -o expr2 ]        | expr1或expr2为真                          |
| [ -n string ]             | string的长度不为0                         |
| [ -z string ]             | string的长度为0                           |
| [ string1 = string2 ]     | 两个字符串string1和string2相等            |
| [ string1 != string2 ]    | 两个字符串string1和string2不等            |
| [ integer1 -eq integer2 ] | 两个integer1和integer2整数相等            |
| [ integer1 -ne integer2 ] | integer1不等于integer2                    |
| [ integer1 -ge integer2 ] | integer1大于或等于integer2                |
| [ integer1 -gt integer2 ] | integer1大于integer2                      |
| [ integer1 -le integer2 ] | integer1小于或等于integer2                |
| [ integer1 -lt integer2 ] | integer1小于integer2                      |
| [ file1 -ef file2 ]       | 文件file1和file2有相同的device和inode数目 |
| [ file1 -nt file2 ]       | file1的修改事件早于file2                  |
| [ file1 -ot file2 ]       | file1的修改事件晚于file2                  |
| [ -b file ]               | file是块设备                              |
| [ -c file ]               | file是字符设备                            |
| [ -d file ]               | file是文件夹                              |
| [ -e file ]               | file是存在                                |
| [ -f file ]               | file是普通文件                            |
| [ -g file ]               | file存在，且有group-ID                    |
| [ -G file ]               | file存在，且group-ID是有效的              |
| [ -h file ]               | file存在，且是一个硬链接                  |
| [ -L file ]               | file存在，且是一个软链接                  |
| [ -r file ]               | file存在，且可读                          |
| [ -w file ]               | file存在，且可写                          |
| [ -x file ]               | file存在，且可执行                        |


例如，

一、判断文件/home/skywang/123.txt是否存在

    $ [ -f /home/skywang/123.txt ]
二、判断变量val是否等于字符串“skywang”

    $ [ "$val" = "skywang" ]
三，判断变量num是否等于数字100

    $ [ "$num" -eq "100" ]


### 1.3 测试逻辑表达式

**基本格式**

| 符号 | 说明                                          |
| ---- | --------------------------------------------- |
| -a   | 逻辑与,操作符两边均为真,结果为真,否则为假。   |
| -o   | 逻辑或,操作符两边一边为真,结果为真,否则为假。 |
| !    | 逻辑否,条件为假,结果为真。                    |

**应用实例**

一、判断文件123.txt是不是可读写

    $ [ -r 123.txt -a -w 123.txt ]
等价于

    $ [ -r 123.txt ] && [ -w 123.txt ]
二、判断变量num是不是等于数字101或102

    $ [ "$num" -eq "101" -o "$num" -eq "102" ]
等价于

    $ [ -r 123.txt ] || [ -w 123.txt ]
三、判断文件123.txt是不是不可读

    $ [ ! -r 123.txt ]



## 2. if then else语句

**基本格式**

    if 条件1
    then 命令1
    elif 条件2
    then 命令2
    else 命令3
    if

**格式说明**

if 语句必须以单词 fi 终止。
elif 和 else 为可选项,如果语句中没有否则部分,那么就不需要 elif 和 else 部分。
if 语句可以有许多 elif 部分。最常用的 if 语句是 if then fi 结构。


**应用实例**

一、判断文件文件123.txt是否存在，存在则输出“file exist”；没有则输出“file not exist”。bash文件内容如下：

    #!/bin/bash

    if [ -f 123.txt ];then
    echo "file exist"
    else
    echo "file not exist"
    fi

    exit 0

二、提示用户输入值。若输入的值小于0，则输出“negtive number”；若等于0,则输出“number zero”，否则，输出“positive number”。bash文件内容如下：

    #!/bin/bash

    # 提示用户输入一个值
    echo -n "please input a number:"

    # 保存用户输入的值到num中
    read num

    if [ "$num" -lt "0" ];then
    # 小于0,则输出“negtive number”
    echo "negtive number"
    elif [ "$num" -gt "0" ];then
    # 大于0,则输出“positive number”
    echo "positive number"
    else
    # 大于0,则输出"number zero"
    echo "number zero"
    fi

    exit 0

## 3. case语句

case语句为多选择语句。可以用case语句匹配一个值与一个模式,如果匹配成功,执行相匹配的命令。

**基本格式**

    case 值 in
    模式1}
    命令1
    ...
    ;;
    模式2)
    命令2
    ...
    ;;
    esac

**格式说明**

“模式”部分可能包括元字符，即:

| 符号     | 说明               |
| -------- | ------------------ |
| \*       | 任意字符。         |
| ?        | 任意单字符。       |
| \[\.\.\] | 类或范围中任意字符 |

**应用实例**

一、提示用户输入Y/y或N/n。若输入Y/y，则输出“yes”；若输入N/n,则输出“no”；否则，“incorrect input”。bash文件内容如下：

    #!/bin/bash

    # 提示用户输入一个值
    echo -n "are you femail(Y/N):"

    # 保存用户输入的值到val中
    read val

    case $val in
    Y|y)
    # 用户输入Y或y
    echo "yes"
    ;;
    N|n)
    # 用户输入N或n
    "no"
    ;;
    *)
    # 其它输入
    echo "incorrect input"
    ;;
    esac

    exit 0


## 4. for循环

**基本格式**

    for 变量名in列表
    do
    命令1
    命令2...
    done

**格式说明**

当变量值在列表里, for循环即执行一次所有命令,使用变量名访问列表中取值。
命令可为任何有效的 shell命令和语句。变量名为任何单词。 in列表用法是可选的,如果不用它, for循环使用命令行的位置参数。

**应用实例**

一、输入当前文件夹的一级子目录中文件名字。bash脚本内容如下：

    #!/bin/bash

    # 将ls的结果保存到变量CUR_DIR中
    CUR_DIR=`ls`

    # 显示ls的结果
    echo $CUR_DIR

    for val in $CUR_DIR
    do
    # 若val是文件，则输出该文件名
    if [ -f $val ];then
    echo "FILE: $val"
    fi
    done

    exit 0


二、输出1-10之间数字的总和。bash脚本内容如下：

    #!/bin/bash

    sum=0
    for ((i=1;i<10;i++))
    do
    ((sum=$sum+$i))
    done

    echo "sum=$sum"

    exit 0


## 5. until循环

until循环执行一系列命令直至条件为真时停止。

until循环与 while循环在处理方式上刚好相反。一般 while循环优于until循环,但在某些时候 — 也只是极少数情况下, until循环更加有用。

**基本格式**

    until 条件
    命令1
    ...
    done

**格式说明**

条件可为任意测试条件,测试发生在循环末尾,因此循环至少执行一次 — 请注意这一点。

**应用实例**

一、从0开始逐步递增，当数值等于5时，停止递增。Bash脚本内容如下：

    #!/bin/bash

    # 设置起始值为0
    val=0

    until [ "$val" -eq "5" ]
    do
    # 输出数值
    echo "val=$val"
    # 将数值加1
    ((val++))
    done

    exit 0


## 6. while循环

**基本格式**

    while 命令
    do
    命令1
    命令2
    ...
    done

**应用实例**

一、从0开始逐步递增，当数值等于5时，停止递增。Bash脚本内容如下：

    #!/bin/bash

    # 设置起始值为0
    val=0

    while [ "$val" -lt "5" ]
    do
    # 输出数值
    echo "val=$val"
    # 将数值加1
    ((val++))
    done

    exit 0

## 7. 使用break和continue控制循环

**基本格式**

break命令允许跳出循环。

continue命令类似于 break命令,只有一点重要差别,它不会跳出循环,只是跳过这个循环步。

**应用实例**

一、[break应用]从0开始逐步递增，当数值等于5时，停止递增。Bash脚本内容如下：

    #!/bin/bash

    # 设置起始值为0
    val=0

    while true
    do
    if [ "$val" -eq "5" ];then
    # 如果val=5，则跳出循环
    break;
    else
    # 输出数值
    echo "val=$val"
    # 将数值加1
    ((val++))
    fi
    done

    exit 0


二、[continue应用]从0开始逐步递增到10：当数值为5时，将数值递增2；否则，输出数值。Bash脚本内容如下：

    #!/bin/bash

    # 设置起始值为0
    val=0

    while [ "$val" -le "10" ]
    do
    if [ "$val" -eq "5" ];then
    # 如果val=5，则将数值加2
    ((val=$val+2))
    continue;
    else
    # 输出数值
    echo "val=$val"
    # 将数值加1
    ((val++))
    fi
    done

    exit 0

# 第四部分 bash数组

## 1. 数组定义

**方法一**

    array=(10 20 30 40 50)

一对括号表示是数组，数组元素用“空格”符号分割开。引用数组时从序号0开始。

**方法二**

    array[0]=10
    array[1]=20
    array[2]=30
    array[3]=40
    array[4]=50

**方法三**

    var="10 20 30 40 50"; array=($var)


## 2. 数组操作

(01) 显示数组中第2项

    $ echo ${array[i]}

说明：数组是从序号0开始计算(即第1项为array[0])。

(02) 显示数组中的所有元素

    $ echo ${array[@]}
或者

    $ echo ${array[*]}

(03) 显示数组长度

    $ echo ${#array[@]}
或者

    $ echo ${#array[*]}

(04) 删除数组第2项元素

    $ unset array[1]

说明：unset仅仅只清除array[1]的值，并没有将array[1]删除掉

(05) 删除整个数组

    $ unset array

(06) 输出数组的第1-3项

    $ echo ${array[@]:0:3}

说明：参考“${数组名[@或*]:起始位置:长度}”

(07) 将数组中的0替换成1

    $ echo ${a[@]/0/1}

说明：${数组名[@或*]/查找字符/替换字符


# 第五部分 函数

## 1. 函数定义

**基本格式**

    function 函数名()
    {
    ...
    }

**格式说明**

function可有可无。但建议保留，因为保留的话看起来更加直观。

**应用实例**

    function foo()
    {
        # 定义局部变量i
        local i=0
        # 定义局部变量total=传入foo的参数总数
        local total=$#
        # 输出参数总数
        echo "total param =$total"
        # 输出传入foo的每一个参数
        for val in $@
        do
            ((i++))
            echo "$i -- val=$val"
        done

        # 返回参数总数
        return $total
    }

## 2. 函数调用和传参

**调用方法**

直接通过函数名去调用。

**应用实例**

    foo param1 param2 param3

说明：调用函数foo，并传入param1 param2 param3三个参数

## 3. 函数返回值

**使用方法**

    return 返回值

**方法说明**

例如，foo param1 param2 param3之后，再调用$?就是上次调用的返回值



## 4. 应用实例

编辑一个函数foo：打印foo的输入参数的总数，并输入每个参数和参数对应的序号。

    #!/bin/bash

    function foo()
    {
        # 定义局部变量i
        local i=0
        # 定义局部变量total=传入foo的参数总数
        local total=$#
        # 输出参数总数
        echo "total param =$total"
        # 输出传入foo的每一个参数
        for val in $@
        do
            ((i++))
            echo "$i -- val=$val"
        done

        # 返回参数总数
        return $total
    }

    foo
    foo param1 param2 param3
    # 输出foo param1 param2 param3的返回值
    echo "return value=$?"

    exit 0


输出结果:

    total param =0
    total param =3
    1 -- val=param1
    2 -- val=param2
    3 -- val=param3
    return value=3

# 第六部分 数值运算

数值比较请参考"第三部分"的1.2节，本部分只介绍数值运算。

常用的4种数值运算说明

数值元算主要有4种实现方式：**(())、let、expr、bc**。

**工作效率**：(()) == let > expr > bc

(01), (())和let是bash内建命令，执行效率高；而expr和bc是系统命令，会消耗内存，执行效率低。
(02), (())、let和expr只支持整数运算，不支持浮点运算；而bc支持浮点运算。

下面分别介绍这4种实现方式的使用方法。


## 应用实例一：分别用上面四种方式实现"3*(5+2)"。

    #!/bin/bash

    # 数值i=3*(5+2) (方法一:$(())实现)
    val=$((3*(5+2)))
    echo "val=$val"

    # 数值i=3*(5+2) (方法二:let实现)
    let "val=3*(5+2)"
    echo "val=$val"

    # 数值i=3*(5+2) (方法三:expr实现)
    val=`expr 3 \* \( 5 + 2 \)`
    echo "val=$val"

    # 数值i=3*(5+2) (方法四:bc实现)
    val=`echo "3*(5+2)"|bc`
    echo "val=$val"

    exit 0

## 应用实例二：分别勇上面四种方式实现“数值+1”。

    #!/bin/bash

    val=0
    # 数值加1 (方法一)
    ((val++))
    echo "val=$val"

    val=0
    # 数值加1 (方法二)
    let val++
    echo "val=$val"

    val=0
    # 数值加1 (方法三)
    val=`expr $val + 1`
    echo "val=$val"

    val=0
    # 数值加1(方法四)
    val=`echo "$val+1"|bc`
    echo "val=$val"

    exit 0

## 应用实例三：计算1/3,保留3位小数。

    #!/bin/bash

    # 数值i=3*(5+2) (方法四:bc实现)
    val=`echo "scale=3; 1/3"|bc`
    echo "val=$val"

    exit 0

# 第七部分 字符运算

## 1. 字符串定义

| 表达式          | 说明                                                      |
| --------------- | --------------------------------------------------------- |
| ${var}          | 变量var的值, 与$var相同                                   |
| ${var-DEFAULT}  | 如果var没有被声明, 那么就以$DEFAULT作为其值               |
| ${var:-DEFAULT} | 如果var没有被声明, 或者其值为空, 那么就以$DEFAULT作为其值 |
| ${var=DEFAULT}  | 如果var没有被声明, 那么就以$DEFAULT作为其值               |
| ${var:=DEFAULT} | 如果var没有被声明, 或者其值为空, 那么就以$DEFAULT作为其值 |
| ${var+OTHER}    | 如果var声明了, 那么其值就是$OTHER, 否则就为null字符串     |
| ${var:+OTHER}   | 如果var被设置了, 那么其值就是$OTHER, 否则就为null字符串   |
| ${var?ERR_MSG}  | 如果var没被声明, 那么就打印$ERR_MSG                       |
| ${var:?ERR_MSG} | 如果var没被设置, 那么就打印$ERR_MSG                       |
| ${!varprefix*}  | 匹配之前所有以varprefix开头进行声明的变量                 |
| ${!varprefix@}  | 匹配之前所有以varprefix开头进行声明的变量                 |


## 2. 字符串操作

### 2.1 string操作公式表

| 表达式                           | 说明                                                                          |
| -------------------------------- | ----------------------------------------------------------------------------- |
| ${#string}                       | $string的长度                                                                 |
| ${string:position}               | 在$string中, 从位置$position开始提取子串                                      |
| ${string:position:length}        | 在$string中, 从位置$position开始提取长度为$length的子串                       |
| ${string#substring}              | 从变量$string的开头, 删除最短匹配$substring的子串                             |
| ${string##substring}             | 从变量$string的开头, 删除最长匹配$substring的子串                             |
| ${string%substring}              | 从变量$string的结尾, 删除最短匹配$substring的子串                             |
| ${string%%substring}             | 从变量$string的结尾, 删除最长匹配$substring的子串                             |
| ${string/substring/replacement}  | 使用$replacement, 来代替第一个匹配的$substring                                |
| ${string//substring/replacement} | 使用$replacement, 代替所有匹配的$substring                                    |
| ${string/#substring/replacement} | 如果$string的前缀匹配$substring, 那么就用$replacement来代替匹配到的$substring |
| ${string/%substring/replacement} | 如果$string的后缀匹配$substring, 那么就用$replacement来代替匹配到的$substring |


### 2.2 应用实例

首先，我们定义个str字符串变量，然后再对此变量进行字符串操作。

    $ str="hello world"

(01) 显示字符串长度

    $ echo ${#str}

(02) 提取world

    $ echo ${str:6}

(03) 提取or

    $ echo ${str:7:2}

(04) 删除hello

    $ echo ${str#hello}
或

    $ echo ${str#*lo}

(05) 删除world

    $ echo ${str%world}
或

    $ echo ${str%wo*}
(06) 将所有的字符“l”替换为“m”

    $ echo ${str//l/m}

# 第八部分 bash自带参数


| 表达式 | 说明                                                                        |
| ------ | --------------------------------------------------------------------------- |
| $?     | 上次指令或程序的执行结果。如果执行成功则为0，否则为非0                      |
| $#     | 参数个数                                                                    |
| $@     | 代表全部变量，例如，"$1" "$2" "$3" "$4"，每个变量是独立的，且用双引号括起来 |
| $*     | 代表全部变量，例如，$1 $2 $3 $4                                             |
| $-     | 在shell启动或使用set命令时提供选项                                          |
| $$     | 当前shell的进程号                                                           |
| $0     | 当前shell进程名                                                             |
| $n     | n表示数字。例如，$1表示第1个参数，$2表示第2个参数                           |


# 第九部分 bash调试

## 1. bash命令调试

    bash [-nvx] scripts.sh

选项与参数:
-n :不要执行 script,仅查询语法的问题;
-v :再执行 sccript 前,先将 scripts 的内容输出到屏幕上;
-x :将使用到的 script 内容显示到屏幕上,这是很有用的参数!

例如，想要执行bash脚本，并查看bash的调用流程，可以通过以下命令：

    $ bash -x test.sh

## 2. echo调试

    echo [OPTION] STRING

说明：
-n : 输出内容之后，不换行。默认是输入内容之后，换行。
-e : 开启反斜线“\”转义功能
-E : 开启反斜线“\”转义功能（默认）。

例如，输出“please input a number:”之后不换行。

    $ echo -n "please input a number:"

## 3. printf

和echo一样，printf也能用于输出。语法格式和C语言中printf一样。

例如，输出“hello printf”之后换行。

    $ printf "hello printf\n"


# 第十部分 bash注释

## 1. 单行注释

    # echo "single line"

说明：#放在文件开头，表示注释掉本行。

## 2. 多行注释

可以通过以下两种方法实现多行注释：

    :||{
    ....被注释的多行内容
    }

或者

    if false ; then
    ....被注释的多行内容
    fi


# 第十一部分 bash内建指令

## 1. 内建命令查看方法

**基本格式**

    type cmd

**格式说明**

type是命令关键字，cmd表示查看的命令；若输出builtin，则该命令是bash的内建命令。

例如:

    $ type echo

除此之外，用户也可以通过man bash或者man builtins查看bash的全部内置命令.

## 2. 常用内建命令说明

| 指令     | 使用示例                                                       | 说明                                                                                                                                                                       |
| -------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| echo     | echo arg                                                       | 在屏幕上显示出由arg指定的字串                                                                                                                                              |
| read     | read变量名表                                                   | 从标准输入设备读入一行，分解成若干字，赋值给bash程序内部定义的变量                                                                                                         |
| shift    | shift [N] <br/> (N为大于0的整数；当N省略时，等价与于“shift 1”) | 所有的参数依次向左移动N个位置，并使用$#减少N，直到$#=0为止。                                                                                                               |
| alias    | alias name='value'                                             | 别名。用name替换value，value要用单引号括住。                                                                                                                               |
| export   | export变量名[=变量值]                                          | export可以把bash的变量向下带入子bash(即子bash中可以使用父bash的变量)，从而让子进程继承父进程中的环境变量。但子bash不能用export把它的变量向上带入父bash。                   |
| readonly | readonly 变量名                                                | 定义只读变量。不带任何参数的readonly命令将显示出所有只读变量。                                                                                                             |
| exec     | exec 命令参数                                                  | 当bash执行到exec语句时，不会去创建新的子进程，而是转去执行指定的命令，当指定的命令执行完时，该进程（也就是最初的bash）就终止了，所以bash程序中exec后面的语句将不再被执行。 |
| "."(点)  | bash程序文件名                                                 | 使bash读入指定的bash程序文件并依次执行文件中的所有语句。                                                                                                                   |
| exit     | exit N                                                         | 退出Shell程序。在exit之后可有选择地指定一个数位作为返回状态。                                                                                                              |


