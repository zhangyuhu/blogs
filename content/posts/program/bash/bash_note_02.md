---
title: "bash 编程笔记 (二)"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "bash 编程笔记"
lightgallery: true
tags: [bash]
categories: [bash]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [第一部分 awk工具](#%E7%AC%AC%E4%B8%80%E9%83%A8%E5%88%86-awk%E5%B7%A5%E5%85%B7)
    - [1. awk介绍](#1-awk%E4%BB%8B%E7%BB%8D)
- [第二部分 sed工具](#%E7%AC%AC%E4%BA%8C%E9%83%A8%E5%88%86-sed%E5%B7%A5%E5%85%B7)
    - [1. sed介绍](#1-sed%E4%BB%8B%E7%BB%8D)
- [第三部分 正则表达式和grep](#%E7%AC%AC%E4%B8%89%E9%83%A8%E5%88%86-%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E5%92%8Cgrep)
    - [1. 正则表达式](#1-%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F)
    - [2. 正则表达式特殊符号](#2-%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E7%89%B9%E6%AE%8A%E7%AC%A6%E5%8F%B7)
    - [3. grep表达式](#3-grep%E8%A1%A8%E8%BE%BE%E5%BC%8F)
    - [4. 应用实例](#4-%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B)
    - [5. egrep](#5-egrep)
        - [5.1. egrep说明](#51-egrep%E8%AF%B4%E6%98%8E)
        - [5.2. egrep应用实例](#52-egrep%E5%BA%94%E7%94%A8%E5%AE%9E%E4%BE%8B)

<!-- /TOC -->

> 本文主要通过实例对bash中需要用到的一些高级工具(如awk、sed、...)进行说明。

> 学习的时候，请以“应用实例”为中心，以其它内容为参考进行学习。如果遇到文章中未讲解的内容，可以通过man去查阅用法。

# 第一部分 awk工具

本章主要通过awk的一些应用实例，来说明awk的相关语法。这样，更利于我们进行理解；所以，阅读本章时，请以“应用实例”为中心进行阅读，其它部分是参考内容。

## 1. awk介绍

awk是一种用于处理文本的编程语言工具。awk本身就是linux下的一个工具，既可以单独使用，也可以嵌入到bash中。

awk语言的最基本功能是在文件或字符串中基于指定规则浏览和抽取信息。 awk抽取信息后,才能进行其他文本操作。完整的 awk脚本通常用来格式化文本文件中的信息。

<br/>
## 2. awk环境变量

|  变量  |       说明      |
| ------ | --------------- |
| $n | 当前记录的第n个字段，字段由FS分隔 |
| $0 | 完整的输入记录 |
| ARGC | 命令行参数的数目 |
| ARGIND | 命令行中当前文件的位置(从0开始算) |
| ARGV | 包含命令行参数的数组 |
| CONVFMT | 数字转换格式(默认是%.6g) |
| ENVIRON | 环境变量关联数组 |
| ERRNO | 最后一个系统错误的描述 |
| FIELDWIDTHS | 字段宽度列表(用空格键分隔) |
| FILENAME | 当前文件名 |
| FNR | 同NR，当FNR是相对于当前文件而言 |
| FS  | 字段分隔符(默认是任意的空格符) |
| IGNORECASE | 若为真，则匹配时忽略大小写 |
| NF | 当前记录中的字段数 |
| NR | 已读的记录数 |
| OFMT | 数字的输出格式(默认是%.6g) |
| OFS | 输出域分隔符(默认是一个空格) |
| ORS | 输出记录分隔符(默认是一个换行符) |
| RLENGTH | 由match函数所匹配的字符串的长度 |
| RS | 控制记录分隔符(默认是一个换行符) |
| RSTART | 由match函数所匹配的字符串的第一个位置 |
| SUBSEP | 数字下标分隔符(默认值是\034) |


<br/>
## 3. awk条件操作符

|  符号  |       描述      |
| ------ | --------------- |
| <  | 小于 |
| <= | 小于或等于 |
| == | 等于 |
| != | 不等于 |
| >= | 大于或等于 |
| >  | 大于 |
| !  | 匹配正则表达式 |
| !~ | 不匹配正则表达式 |



<br/>
## 4. awk字符串操作

|  表达式  |       说明      |
| -------- | --------------- |
| gsub(r,s) | 在整个$0中用s替换r |
| gsub(r,s,t) | 在整个t中用s替换r |
| index(s,t) | 返回s中字符串t的第一个位置 |
| length(s) | 返回s的长度 |
| match(s,r) | 测试s是否包含匹配r的字符串 |
| split(s,a,fs) | 在fs上将s分成序列a |
| sprint(fmt,exp) | 返回经fmt格式化后的exp |
| sub(r,s) | 用$0中最左边最长的子串代替s |
| substr(s,p) | 返回字符串s中从p开始的后缀部分 |
| substr(s,p,n) | 返回字符串s中从p开始的长度为n后缀部分 |


<br/>
## 5. 正则表达式中常用类

|  表达式  |       说明      |
| -------- | --------------- |
| [[:upper:]] | [A-Z] |
| [[:lower:]] | [a-z] |
| [[:alpha:]] | [a-zA-Z] |
| [[:digit:]] | [0-9] |
| [[:alnum:]] | [0-9a-zA-Z] |
| [[:space:]] | 空格或tab键 |


<br/>
## 6 awk应用实例

首先建立一个123.txt，添加任意文本，然后进行以下练习。

(01), 输出文件全部文本

    $ awk '{print $0}' 123.txt

说明:
{}：表示一段awk命令。
print：输出指令。
$0：当前行的全部文本。

awk工具操作文件时，是以行为单位，逐步对每行进行操作。拿本例来说{print $0}，意味着“对每一行都执行print $0操作，即输出每一行的全部文本”。

$N：当N>1时，$N表示当前行的第N段；每一行中以FS(默认值是空格)来分段。

(02), 输出"ls -l"中每行的field1、field9

    $ ls -l | awk '{printf("%s %s\n", $1, $9)}'

说明:
|：管道符号。表示将“前面指令的输出”作为“后面指令的输入”，即将“ls -l”的输出作为“awk”的输入。
printf：输出指令。它的使用方法和C语言中printf的使用方法一样。
$1：该行的第1段。
$9：该行的第9端。

(03), 在上一题的基础上添加功能：第一，输出每一行的行号和该行所包括的域的总数。第二，第1行和最后一行输出提示语

    $ ls -l | awk 'BEGIN{printf("----begin----\n")} {printf("Line-%3d Field-%d : %s %s\n", NR, NF, $1, $9)} END{printf("----end----\n")}'

说明：
BEGIN：表示在文本进行操作之前进行的工作。
END：表示在对文本的所有行都处理完毕之后前进行的工作。
在awk中，请尽量使用{}来进行区分指令段，{}可以嵌套使用！这样做的好处写出的脚本不容易出错，而且可读性更强！

(04), 输出"ls -l"中文件(夹)名字包括数字的完整信息

    $ ls -l | awk '{if($9 ~ /[[:digit:]]/) {print $0}}'

说明：
$9 ~ /[[:digit:]]/ ： 表示能够匹配数字的“第一行的第9段”。

(05), 输出"ls -l"中非文件夹的完整信息

    $ ls -l | awk '{if($1 !~ /^d/) {print $0}}'

说明：
^d ： 以d开头的。^表示起始位。此外，$表示结束位。如d$，表示以d结尾的。

(06), 找到"ls -l"中文件(夹)名字的长度大于10的行，然后输出其完整信息。

    $ ls -l | awk '{if(length($9) > 10) {print $0}}'

(07), 如何给文本的每一行添加行号？

    $ awk '{printf("%03d %s\n",NR, $0)}' ori.txt > dst.txt

(08), 打印字段数大于3的行的总数

    $ awk 'BEGIN{COUNT=0}; {if(NF>3) COUNT++}; END{printf("COUNT=%d\n", COUNT)}' ori.txt

(09), 将文本中的各行合并一行，中间用“|”分割

    $ awk 'BEGIN{ORS="|"}{print $0 }END{print "\n"}' ori.txt > dst.txt
说明：
ORS：表示记录分割符，每条记录表示每行。ORS默认值为换行符。

(10), 将文本中空格换成换行符

    $ awk 'BEGIN{FS=" ";OFS="\n"}{print $1 }END{print "\n"}' ori.txt > dst.txt

(11), 将3行合并成一行，并输入行号

    $ awk 'BEGIN{ORS="";i=0}{ j=1; while(j<=NF){ if(i%3==0){printf("%02d ",i/3+1)}; printf("%s ",$j); i++; j++; if(i%3==0){print "\n"} }} END{print "\n"}' ori.txt > dst.txt


# 第二部分 sed工具


## 1. sed介绍

sed是一个非交互性文本流编辑器。和awk一样，它是个独立的工具，当然也可以和bash联合使用。

它可以随意编辑文件或标准输入,对它们进行编辑、删除。它能一次性处理所有改变,对用户来讲,十分高效。sed编辑文件或标准输入时，编辑的是它们的拷贝；也就是说，不会改变原始的文件。若需要保存修改，可以通过重定向操作符>>、>>>,或者在利用sed的写入参数。

<br/>
## 2. 基本格式

    sed [选项] 输入文件

-n 不打印;sed不写编辑行到标准输出,缺省为打印所有行(编辑和未编辑)。
-p 命令可以用来打印编辑行。
-c 下一命令是编辑命令。使用多项编辑时加入此选项。如果只用到一条 sed命令,此选项无用,但指定它也没有关系。
-e 追加执行脚本
-f 如果正在调用 sed脚本文件,使用此选项。



<br/>
## 3. 使用sed在文件中定位文本的方式

|  方式  |       说明      |
| ------ | --------------- |
| x | x为行号。例如，1 |
| x,y | 表示行号范围从x到y。例如，2,5表示从第2行到第5行 |
| x,y! | 查询不包含指定行(x到y) |
| /pattern/ | 查询包含pattern模式的行。例如, /disk/或/[a-z]/ |
| pattern/,x | 在给定行号上查询包含pattern模式的行。例如， /ribbon/,3 |
| x,/pattern/ | 通过行号和模式查询匹配行。 例如， 3,/vdu/ |


<br/>
## 4. sed编辑命令

|  方式  |       说明      |
| ------ | --------------- |
| p | 打印匹配行 |
| = | 显示文件行号 |
| a\ | 在定位行后附加文本信息 |
| i\ | 在定位行前附加文本信息 |
| d  | 删除定位行 |
| c\ | 用新文本替换定位文本 |
| s | 使用替换模式 |
| r | 从另一个文件中读文本 |
| w | 写文本到一个文件 |
| q | 第一个模式匹配完成后退出 |
| {} | 在定位行指定的命令组 |
| n | 从另一个文件中读取，并追加在下一行 |



<br/>
## 5. 应用实例

首先建立一个123.txt，添加任意文本，然后进行以下练习。

(01), 输出文件第5行

    $ sed -n '5p' 123.txt

说明：-n表示默认不输出任何内容。5p表示输出第5行：5表示第5行，p表示输出。

(02), 输出文件除1-3行之外的行

    $ sed -n '1,3!p' 123.txt

说明：
"1,3"表示输出范围是第1-3行; "1,3!"表示输出范围是除第1-3行之外。
'1,$p'表示输出全部行，因为$表示最后一行。

(03), 输出"ls -l"结果中的第1-3行

    $ ls -l |sed -n '1,3p'

说明：|是管道符号，表示将ls -l的输出作为sed的输入。

(04), 输出匹配“the”的行

    $ sed -n '/the/p' 123.txt

说明：/the/表示匹配the的行

(05), 输出匹配“the”的行，并且输出每行行号

    $ sed -n -e '/the/p' -e '/the/=' 123.txt

说明：-e表示对每行进行多重编辑，多重编辑的每一个命令前都需要添加-e。
本例中，-e '/the/p'打印匹配the的行；-e '/the/='表示输出匹配the的行的行号。

(06), 删除匹配“the”的行

    $ sed '/the/d' 123.txt

说明：d表示删除。

(07), 删除匹配“the”的行；然后输出删除操作之后的所有行，并输出每行行号

    $ sed '/the/d' 123.txt | sed -n -e '1,$p' -e '1,$='

说明：
sed '/the/d' 123.txt ：得到了删除“the”之后的行
| ：管道符号。意味着前面的输出作为后面的输入
sed -n -e '1,$p' -e '1,$=' ：表示输出全部行之后，在输出每行行号

(08), 在每一行前面插入2行文本，第一行是line1,第2行是line2

    $ sed '1,$iline1\nline2' 123.txt

说明：
1,$i表示第一行到最后一行的每一行都执行插入操作。
line1\nline2表示插入的文本，其中\n转义之后表示“换行”符号。

(09), 在最后一行后面插入1行文本，内容是end

    $ sed '$aend' 123.txt

说明：$表示最后一行，a表示在文本后插入，end是插入的内容

(10), 将“this”全部替换成“that”

    $ sed 's/this/that/g' 123.txt

说明：
[ address[,address ] ] s / pattern-to-find / replacement-pattern/[gpwn]
s 表示替换操作。查询pattern-to-find,成功后用replacement-pattern替换它。
替换选项如下:
g 缺省情况下只替换每行的第一次匹配，g表示替换每行的所有匹配。
p 缺省sed将所有被替换行写入标准输出，加p选项将使-n选项无效。-n选项不打印输出结果。
w 后接“文件名”，表示将输出定向到一个文件。

(11), 将“this”全部替换成“this boy”

    $ sed 's/this/this boy/g' 123.txt

或

    $ sed 's/this/boy &/g' 123.txt

说明：
sed 's/this/boy &/pg' 123.txt中&表示附加修改(即在原始内容的基础上添加内容)。
&表示匹配的内容。即，boy &等价于boy this

(12), 去掉空白行后另存文件

    $ sed '/^$/d' 123.txt > 456.txt

或

    $ sed '/^$/c\' 123.txt > 456.txt

说明：
/^$/表示空白行：^表示开启，$表示结尾，开始和结尾之间没有任何内容，即是空白行。
c\表示修改。

(13), 去掉文件扩展名

    $ echo "hello.txt"| sed 's/.txt//g'

(14), 添加文件扩展名

    $ echo "hello"| sed 's/$/.txt/g'

(15), 删除文本中每一行的第2个字符

    $ sed 's/.//2' ori.txt > dst.txt

(16), 删除文本中每一行的倒数第2个字符

    $ sed 's/\(.\)\(.\)$/\2/' ori.txt > dst.txt

说明：考察了sed中"\( \)"的含义和用法

(17), 删除每一行的第2个单词

    $ sed 's/\([[:alpha:]]\+\)\(\ \)\([[:alpha:]]\+\)*/\1/' ori.txt > dst.txt

(18), 隔行删除

    $ sed '0~2 d' ori.txt > dst.txt


# 第三部分 正则表达式和grep


## 1. 正则表达式

正则表达式就是字符串的表达式。它能通过具有意义的特殊符号表示一列或多列字符串。

grep是linux系统下常用的正则表达式工具，可以使用grep来检索文本等输入流的字符串。


## 2. 正则表达式特殊符号



|  表达式  |       说明      |
| -------- | --------------- |
| ^ | 匹配行首 |
| $ | 匹配行尾 |
| . | 任意字符 |
| * | 匹配0～N个某字符或某表达式。例如， [a-z]*表示匹配0到多个小写字母 |
| [] | 匹配中括号类的字符。中括号内可以用-表示范围，例如，[1-5]等价于[12345] |
| \ | 屏蔽具有字符的特殊含义 |
| \< | 匹配以某内容为开始的项。例如， \<st表示匹配以st开始的项 |
| \> | 匹配以某内容为结束的项。例如， nd\>表示匹配以end结尾的项 |
| string\{m,n\} | 连续m到n个string |
| string\{m\} | 出现m次string |
| string\{m\} | 至少出现m次string |
|  |  |
| [[:upper:]] | A-Z的任意大写字母 |
| [[:lower:]] | a-z的任意小写字母 |
| [[:alpha:]] | 任意字母 |
| [[:digit:]] | 0-9的任意数字 |
| [[:xdigit:]] | 十六禁止的数字类型，包括：0-9，A-F，a-f |
| [[:alnum:]] | 任意字母或数字 |
| [[:blank:]] | 空格或tab键 |
| [[:space:]] | 任何会产生空白的字母，包括空格、TAB、CR等 |


## 3. grep表达式

**基本格式**

    grep [OPTIONS] PATTERN [FILE...]

**格式说明**

PATTERN : 匹配模式。可以是字符串，也可以是正则表达式。
[FILE...] : 是grep搜索的文件(集)
[OPTIONS] : 是grep的选项。常用的选项有以下选项。
> -c : 只输出匹配行的计数。
-I : 不区分大 小写(只适用于单字符)。
-h : 查询多文件时不显示文件名。
-l : 查询多文件时只输出包含匹配字符的文件名。
-n : 显示匹配行及 行号。
-s : 不显示不存在或无匹配文本的错误信息。
-v : 显示不包含匹配文本的所有行。
-r : 当FILE中包含文件夹名时，遍历该文件夹的所有子目录；默认情况下，不会遍历子目录。

## 4. 应用实例

下面以input.txt为例，对grep进行说明。input.txt的文本内容如下：


    "Open Source" is a good mechanism to develop programs.
    apple is my favorite food.
    Football game is not use feet only.
    this dress doesn't fit me.
    However, this dress is about $ 3183 dollars.^M
    GNU is free air not free beer.^M
    Her hair is very beauty.^M
    I can't finish the test.^M
    Oh! The soup taste good.^M
    motorcycle is cheap than car.
    This window is clear.
    the symbol '*' is represented as start.

    Oh!
    My god!
    The gd software is a library for drafting programs.^M
    You are the best is mean you are the no. 1.
    The world <Happy> is the same with "glad".
    I like dog.
    google is the best tools for search keyword.
    goooooogle yes!
    go! go! Let's go


(01), 查找包含“the”的行，并显示行号。

    $ grep -n "the" input.txt

说明：-n表示显示“行号”

(02), 不区分大小写，查找包括“the”的行，并显示行号。

    $ grep -in "the" input.txt

说明：-n表示显示“行号”；-i表示不区分大小写，即ignore大小写。

(03), 查找不包括“the”的行，统计行数。

    $ grep -cv "the" input.txt

说明：-c表示统计(count)；-v表示不匹配的项。

(04), 查找“当前目录”及其“所有子目录”中包含“the”的文件，并显示“the”在其中的行号。

    $ grep -rn "the" .

说明：-r表示递归查找；-n表示显示行号。

(05), 查找匹配“t?st”的项，其中?为任意字符。

    $ grep -n "t.st" input.txt

说明：.表示匹配任意字符

(06), 查找包含数字的行

    $ grep -n "[0-9]" input.txt

或

    $ grep -n "[[:digit:]]" input.txt

说明：[0-9]表示0-9之间的一个数字；[[:digit:]]也表示0-9之间的一个数字

(07), 查找以the开头的行

    $ grep -n "^the" input.txt

说明："^the"表示以the开头

(08), 查找以小写字母结尾的行。

    $ grep -n "[a-z]$" input.txt

说明：[a-z]表示一个小写字母，$表示结束符；[a-z]$表示以小写字母结束的项。

(09), 查找空白行。

    $ grep -n "^$" input.txt

说明：^表示开头，如^t表示以字母t开头；$表示结尾，如e$表示以e结尾。^$表示空白行。

(10), 查找以字母g开头的单词

    $ grep -n "\<g" input.txt

说明：\<表示单词的开始，\<g表示以g开始的单词。

(11), 查找字符串为go的单词。注意：不能包括goo,good等字符串

    $ grep -n "\<go\>" input.txt

说明：\<表示单词的开始，\>表示单词结尾。\<go\>表示以字母g开头，以字母o结尾。

(12), 查找包括2-5个字母o的行。

    $ grep -n "o\{2,5\}" input.txt

说明：pattern\{n,m\}表示n到m个pattern。o\{2,5\}表示2-5个字母o。

(13), 查找包括2个以上字母o(包括2个)的行。

    $ grep -n "ooo*" input.txt

或

    $ grep -n "oo\+" input.txt

或

    $ grep -n "o\{2,\}" input.txt

说明：
ooo*: 前面两个oo表示匹配2个字母o，后面的o*表示匹配0到多个字母o。
oo\+: 第一个字母o表示匹配单个字母o；最后的“o\+”一起发挥作用，其中，\+是转义后的+，表示1到多个；而o\+表示1到多个字母o。
pattern\{n,\}表示多于n个pattern。o\{2,\}表示多于2个字母o。



## 5. egrep

### 5.1 egrep说明

egrep是扩展的grep，即它的功能比grep更多一些。"egrep"等价于"grep -e"。

egrep相比与grep，支持括号“()”以及操作符“|”(表示或)。



### 5.2 egrep应用实例

仍然以上面的input.txt为输入文本进行说明

(01), 查找包含the或者this的行

    $ egrep -n "the|this" input.txt

说明：-n表示输出匹配项的行号，"the|this"表示包括the或者包括this的项。

(02), 查找包含the或者this的行

    $ egrep -vn "(the|this)" input.txt

说明：-n表示输出匹配项的行号，"the|this"表示包括the或者包括this的项；-v表示匹配的对立面。即 -v "the|this"表示既不包括the又不包括this的项。


