---
title: "shell"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "bash 编程笔记"
lightgallery: true
tags: [bash]
categories: [bash]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [资料](#%E8%B5%84%E6%96%99)
- [note：关键字](#note%E5%85%B3%E9%94%AE%E5%AD%97)
    - [1. $#,$@,$0,$1,$2](#1-012)
    - [2. while循环和遍历参数](#2-while%E5%BE%AA%E7%8E%AF%E5%92%8C%E9%81%8D%E5%8E%86%E5%8F%82%E6%95%B0)
    - [3. i](#3-i)
    - [4. 通配符wildcard](#4-%E9%80%9A%E9%85%8D%E7%AC%A6wildcard)
    - [5. 元字符（特殊字符 Meta）](#5-%E5%85%83%E5%AD%97%E7%AC%A6%E7%89%B9%E6%AE%8A%E5%AD%97%E7%AC%A6-meta)
    - [6. shell转义符](#6-shell%E8%BD%AC%E4%B9%89%E7%AC%A6)
    - [7. 管道和重定向](#7-%E7%AE%A1%E9%81%93%E5%92%8C%E9%87%8D%E5%AE%9A%E5%90%91)
- [note:代码段](#note%E4%BB%A3%E7%A0%81%E6%AE%B5)
    - [1. 判断文件夹或文件是否存在](#1-%E5%88%A4%E6%96%AD%E6%96%87%E4%BB%B6%E5%A4%B9%E6%88%96%E6%96%87%E4%BB%B6%E6%98%AF%E5%90%A6%E5%AD%98%E5%9C%A8)

<!-- /TOC -->

# 资料
[ABS_GUIDE](http://www.tldp.org/LDP/abs/abs-guide.pdf)

[Linux Shell 通配符、元字符、转义符使用实例介绍](https://www.cnblogs.com/chengmo/archive/2010/10/17/1853344.html)

# note：关键字
## $#,$@,$0,$1,$2
    $$
    Shell本身的PID（ProcessID）
    $!
    Shell最后运行的后台Process的PID
    $?
    最后运行的命令的结束代码（返回值）
    $-
    使用Set命令设定的Flag一览
    $*
    所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。
    $@
    所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。
    $#
    添加到Shell的参数个数
    $0
    Shell本身的文件名
    $1～$n
    添加到Shell的各参数值。$1是第1参数、$2是第2参数…。

```shell
#!/bin/bash
printf "The complete list is %s\n" "$$"
printf "The complete list is %s\n" "$!"
printf "The complete list is %s\n" "$?"
printf "The complete list is %s\n" "$*"
printf "The complete list is %s\n" "$@"
printf "The complete list is %s\n" "$#"
printf "The complete list is %s\n" "$0"
printf "The complete list is %s\n" "$1"
printf "The complete list is %s\n" "$2
```

## while循环和遍历参数
```shell
while [ $# -gt 0 ] ; do
    echo $1
    #左移一个参数，这样可以使用$1遍历所有参数
    shift
done
```
## i
```shell
!!		重复前一个命令
!字符	重复前一个以此字符开头的命令
!num	重复历史记录里序号为num的命令
!?abc	重复执行之前命令中包含abc的命令
!-n		重复之前第n个命令
```
## 通配符(wildcard)

​	通配符是由shell处理的（不是由所涉及到命令语句处理的，其实我们在shell各个命令中也没有发现有这些通配符介绍）, 它只会出现在 命令的“参数”里（它不用在 命令名称里， 也不用在 操作符上）。当shell在“参数”中遇到了通配符时，shell会将其当作路径或文件名去在磁盘上搜寻可能的匹配：若符合要求的匹配存在，则进行代换(路径扩展)；否则就将该通配符作为一个普通字符传递给“命令”，然后再由命令进行处理。总之，通配符 实际上就是一种shell实现的路径扩展功能。在 通配符被处理后, shell会先完成该命令的重组，然后再继续处理重组后的命令，直至执行该命令。

``` shell
* 匹配一个或多个 M* 以M 开头的字符串
？匹配任意一个字符 M？ 以M开头的两个字符
[0-9]匹配0-9中的一个数字
[abc]匹配abc中的一个字符
[^abc]匹配abc以外的一个字符
ls Do[ab]ument 报错，匹配不到 ls Document
```
| 字符                  | 含义                                        | 实例                                                         |
| --------------------- | ------------------------------------------- | ------------------------------------------------------------ |
| *                     | 匹配 0 或多个字符                           | a*b a与b之间可以有任意长度的任意字符, 也可以一个也没有, 如aabcb, axyzb, a012b, ab。 |
| ?                     | 匹配任意一个字符                            | a?b a与b之间必须也只能有一个字符, 可以是任意字符, 如aab, abb, acb, a0b。 |
| [list]                | 匹配 list 中的任意单一字符                  | a[xyz]b  a与b之间必须也只能有一个字符, 但只能是 x 或 y 或 z, 如: axb, ayb, azb。 |
| [!list]               | 匹配 除list 中的任意单一字符                | a[!0-9]b a与b之间必须也只能有一个字符, 但不能是阿拉伯数字, 如axb, aab, a-b。 |
| [c1-c2]               | 匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z] | a[0-9]b 0与9之间必须也只能有一个字符 如a0b, a1b... a9b。     |
| {string1,string2,...} | 匹配 sring1 或 string2 (或更多)其一字符串   | a{abc,xyz,123}b  a与b之间只能是abc或xyz或123这三个字符串之一。 |

需要说明的是：通配符看起来有点象正则表达式语句，但是它与正则表达式不同的，不能相互混淆。把通配符理解为shell 特殊代号字符就可。而且涉及的只有，*,? [] ,{} 这几种。

## 元字符（特殊字符 Meta）

shell 除了有通配符之外，由shell 负责预先先解析后，将处理结果传给命令行之外，shell还有一系列自己的其他特殊字符。

| 字符 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| IFS  | 由 <space> 或 <tab> 或 <enter> 三者之一组成(我们常用 space )。 |
| CR   | 由 <enter> 产生。                                            |
| =    | 设定变量。                                                   |
| $    | 作变量或运算替换(请不要与 shell prompt 搞混了)。             |
| >    | 重导向 stdout。 *                                            |
| <    | 重导向 stdin。 *                                             |
| \|   | 命令管线。 *                                                 |
| &    | 重导向 file descriptor ，或将命令置于背境执行。 *            |
| ( )  | 将其内的命令置于 nested subshell 执行，或用于运算或命令替换。 * |
| { }  | 将其内的命令置于 non-named function 中执行，或用在变量替换的界定范围。 |
| ;    | 在前一个命令结束时，而忽略其返回值，继续执行下一个命令。 *   |
| &&   | 在前一个命令结束时，若返回值为 true，继续执行下一个命令。 *  |
| \|\| | 在前一个命令结束时，若返回值为 false，继续执行下一个命令。 * |
| !    | 执行 history 列表中的命令。*                                 |

加入”*” 都是作用在命令名直接。可以看到shell 元字符，基本是作用在命令上面，用作多命令分割（或者参数分割）。因此看到与通配符有相同的字符，但是实际上作用范围不同。所以不会出现混淆。

以下是man bash 得到的英文解析：

metacharacter
       A character that, when unquoted, separates words. One of the following:
       | & ; ( ) < > space tab
control operator
       A token that performs a control function. It is one of the following symbols:
       || & && ; ;; ( ) | <newline>

## shell转义符

有时候，我们想让 通配符，或者元字符 变成普通字符，不需要使用它。那么这里我们就需要用到转义符了。 shell提供转义符有三种。

| 字符       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| ‘’(单引号) | 又叫硬转义，其内部所有的shell 元字符、通配符都会被关掉。注意，硬转义中不允许出现’(单引号)。 |
| “”(双引号) | 又叫软转义，其内部只允许出现特定的shell 元字符：$用于参数代换 `用于命令代替 |
| \(反斜杠)  | 又叫转义，去除其后紧跟的元字符或通配符的特殊意义。           |
>
man bash 英文解释如下：
>
There are three quoting mechanisms: the escape character, single quotes, and double quotes.

## 管道和重定向

多命令协作:通过管道和重定向完成.(将多个命令结合在一起),几乎所有命令的输入和返回输出是纯文本.
example : cat 输入 文件路径 返回 文件文本内容

命令行shell的数据流定义:

| 名称   | 说明     | 编号 | 默认 |
| :----- | -------- | ---- | ---: |
| STDIN  | 标准输入 | 0    | 键盘 |
| STDOUT | 标准输出 | 1    | 终端 |
| STDERR | 标准错误 | 2    | 终端 |

命令通过STDIN接收参数或数据，通过STDOUT输出结果或通过STDERR输出错误.

| 分类   | 关键字 | 定义                         |                                    例子 |
| :----- | ------ | ---------------------------- | --------------------------------------: |
| 重定向 | >      | 将STDOUT重定向到文件(覆盖)   |   echo "baidu" > outfile / ls > outfile |
|        | >>     | 将STDOUT重定向到文件(追加)   | echo "baidu" >> outfile/ date >>outfile |
|        | 2>     | 将STDERROR重定向到文件(覆盖) |                  ls nothere 2> errorout |
|        | 2>&1   | 将STDERROR与STDOUT结合       |               ls nothere 2>&1 alloutput |
|        | <      | 重定向STDIN                  |            grep linuxcast < /etc/passwd |
| 管道   | \|     | 将一个命令的STDOUT           |                 ls -l \| grep linuxcast |
|        |        | 作为另一个命令的STDIN        |    find / -user linuxcast \| grep video |

2> /dev/null  重定向给null,丢弃

# note:代码段
## 判断文件夹或文件是否存在
文件夹不存在则创建
```shell
if [ ! -d "/data/" ];then
  mkdir /data
  else
  echo "文件夹已经存在"
fi
```
文件存在则删除
```shell
if [ ! -f "/data/filename" ];then
  echo "文件不存在"
  else
  rm -f /data/filename
fi
```
判断文件夹是否存在
```shell
if [ -d "/data/" ];then
  echo "文件夹存在"
  else
  echo "文件夹不存在"
fi
```
判断文件是否存在
```shell
if [ -f "/data/filename" ];then
  echo "文件存在"
  else
  echo "文件不存在"
fi
```
文件比较符
```
-e 判断对象是否存在
-d 判断对象是否存在，并且为目录
-f 判断对象是否存在，并且为常规文件
-L 判断对象是否存在，并且为符号链接
-h 判断对象是否存在，并且为软链接
-s 判断对象是否存在，并且长度不为0
-r 判断对象是否存在，并且可读
-w 判断对象是否存在，并且可写
-x 判断对象是否存在，并且可执行
-O 判断对象是否存在，并且属于当前用户
-G 判断对象是否存在，并且属于当前用户组
-nt 判断file1是否比file2新  [ "/data/file1" -nt "/data/file2" ]
-ot 判断file1是否比file2旧  [ "/data/file1" -ot "/data/file2" ]
```