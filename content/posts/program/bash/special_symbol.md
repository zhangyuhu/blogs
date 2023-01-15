---
title: "shell 中的特殊符号"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "bash 编程笔记"
lightgallery: true
tags: [bash]
categories: [bash]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [# 井号 (comments)](#-井号-comments)
- [~ 帐户的 home 目录](#-帐户的-home-目录)
- [; 分号 (Command separator)](#-分号-command-separator)
- [;; 连续分号 (Terminator)](#-连续分号-terminator)
- [逗号 (dot,就是“点”)](#逗号-dot就是点)
- ['string' 单引号 (single quote)](#string-单引号-single-quote)
- ["string" 双引号 (double quote)](#string-双引号-double-quote)
- [`command` 倒引号 (backticks)](#command-倒引号-backticks)
- [, 逗点 (comma，标点中的逗号)](#-逗点-comma标点中的逗号)
- [/ 斜线 (forward slash)](#-斜线-forward-slash)
- [\\ 倒斜线](#-倒斜线)
- [| 管道 (pipeline)](#-管道-pipeline)
- [! 惊叹号(negate or reverse)](#-惊叹号negate-or-reverse)
- [: 冒号](#-冒号)
- [? 问号 (wild card)](#-问号-wild-card)
- [\* 星号 (wild card)](#-星号-wild-card)
- [\*\* 次方运算](#-次方运算)
- [$ 钱号(dollar sign)](#-钱号dollar-sign)
  - [1. 变量替换(Variable Substitution)的代表符号。](#1-变量替换variable-substitution的代表符号)
  - [2. ${} 变量的正规表达式](#2--变量的正规表达式)
  - [3. $\*](#3-)
  - [4. $@](#4-)
  - [5. $#](#5-)
  - [6. $? 状态值 (status variable)](#6--状态值-status-variable)
  - [7. $$](#7-)
- [() 指令群组 (command group)](#-指令群组-command-group)
  - [1. (()))](#1-)
- [{} 大括号 (Block of code)](#-大括号-block-of-code)
  - [1. 常规用法](#1-常规用法)
    - [1.1. 大括号拓展](#11-大括号拓展)
    - [1.2. 代码块](#12-代码块)
  - [2. 几种特殊的替换结构](#2-几种特殊的替换结构)
  - [3. 四种模式匹配替换结构](#3-四种模式匹配替换结构)
  - [4. 字符串提取和替换](#4-字符串提取和替换)
- [\[\] 中括号](#-中括号)
- [\[\[\]\]](#)
- [|| \&\& \&](#--)
  - [1. ||或逻辑符号](#1-或逻辑符号)
  - [2. \&\& 逻辑符号](#2--逻辑符号)
  - [3. \&后台工作](#3-后台工作)
- [/ 单字边界](#-单字边界)
- [+ 加号 (plus)](#-加号-plus)
- [- 减号 (dash)](#--减号-dash)
- [% 除法 (Modulo)](#-除法-modulo)
- [= 等号 (Equals)](#-等号-equals)
- [== 等号 (Equals)](#-等号-equals-1)
- [!= 不等于](#-不等于)
- [^](#-1)

<!-- /TOC -->

参考：
[](https://www.cnblogs.com/zhouhbing/p/4783147.html)

[](https://www.runoob.com/w3cnote/linux-shell-brackets-features.html)



在shell中常用的特殊符号罗列如下：

```bash
# ;   ;; . , / \\ 'string'| !   $   ${}   $? $$   $*

\"string\"* **   ? : ^ $#   $@ `command`{}   [] [[]] ()   (())

||   && {xx,yy,zz,...}~   ~+   ~-   &   \\<...\\>   + - %=   ==   !=

```

# \# 井号 (comments)
这几乎是个满场都有的符号，除了先前已经提过的\"第一行\"
#!/bin/bash
井号也常出现在一行的开头，或者位于完整指令之后，这类情况表示符号后面的是注解文字，不会被执行。
```bash
# This line is comments.
echo \"a = $a\" # a = 0
```
由于这个特性，当临时不想执行某行指令时，只需在该行开头加上 # 就行了。这常用在撰写过程中。
```bash
echo \"a = $a\" # a = 0
```
如果被用在指令中，或者引号双引号括住的话，或者在倒斜线的后面，那他就变成一般符号，不具上述的特殊功能。


# ~ 帐户的 home 目录
算是个常见的符号，代表使用者的 home 目录：cd ~；也可以直接在符号后加上某帐户的名称：cd ~user或者当成是路径的一部份：
```bash
~/bin
```
~+ 当前的工作目录，这个符号代表当前的工作目录，她和内建指令 pwd的作用是相同的。
```bash
# echo ~+/
var/log
```
~- 上次的工作目录，这个符号代表上次的工作目录。
```bash
# echo ~-
/etc/httpd/logs
```


# ; 分号 (Command separator)
在 shell 中，担任\"连续指令\"功能的符号就是\"分号\"。譬如以下的例子：
```bash
cd ~/backup ; mkdir  startup ;cp ~/.* startup/.
```

# ;; 连续分号 (Terminator)
专用在 case 的选项，担任 Terminator 的角色。
```bash
case \"$fop\"
   inhelp)
      echo \"Usage: Command -help -version filename\"
      ;;
   version)
      echo \"version 0.1\"
      ;;
esac
```

# 逗号 (dot,就是“点”)
在 shell 中，使用者应该都清楚，一个 dot 代表当前目录，两个 dot 代表上层目录。
```bash
CDPATH=.:~:/home:/home/web:/var:/usr/local
```
在上行 CDPATH 的设定中，等号后的 dot 代表的就是当前目录的意思。

如果档案名称以 dot 开头，该档案就属特殊档案，用 ls 指令必须加上 -a 选项才会显示。

除此之外，在  regular expression 中，一个 dot 代表匹配一个字元。


# 'string' 单引号 (single quote)
被单引号用括住的内容，将被视为单一字串。在引号内的代表变数的$符号，没有作用，也就是说，他被视为一般符号处理，防止任何变量替换。
```bash
heyyou=home
echo '$heyyou' # We get $heyyou
```

# \"string\" 双引号 (double quote)
被双引号用括住的内容，将被视为单一字串。它防止**通配符**扩展，但允许变量扩展。这点与单引数的处理方式不同。

通配符是一种特殊语句，主要有星号(*)和问号(?)，用来模糊搜索文件。

当查找文件夹时，可以使用它来代替一个或多个真正字符；当不知道真正字符或者懒得输入完整名字时，常常使用通配符代替一个或多个真正的字符。

实际上用“*Not?pad”可以对应Notepad\MyNotepad【*可以代表任何字符串；?仅代表单个字符串，但此单字必须存在】;Notep[ao]d可以对应Notepad\Notepod【ao代表a与o里二选一】，其余以此类推。

```bash
heyyou=home
echo "$heyyou" # We get home
```

# `command` 倒引号 (backticks)
在前面的单双引号，括住的是字串，但如果该字串是一列命令列，会怎样？答案是不会执行。要处理这种情况，我们得用倒单引号来做。
```bash
fdv=`date +%F`
echo "Today $fdv"
```
在倒引号内的 date +%F 会被视为指令，执行的结果会带入 fdv 变数中。


# , 逗点 (comma，标点中的逗号)
这个符号常运用在运算当中当做\"区隔\"用途。如下例

```bash
let "t1= ((a = 5 + 3, b = 7 - 1, c = 15 / 5),(a + b + c))"
echo "t1= $t1, a = $a, b = $b"
```

let 命令是 BASH 中用于计算的工具，用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量。

如果表达式中包含了空格或其他特殊字符，则必须引起来。

# / 斜线 (forward slash)

在路径表示时，代表目录。
```bash
cd /etc/rc.d
cd ../..
cd /
```
通常单一的 / 代表 root 根目录的意思；
在四则运算中，代表除法的符号。
let \"num1 = ((a = 10 / 2, b = 25 / 5))\"


# \\ 倒斜线
在交互模式下的escape 字元，有几个作用:
放在指令前，有取消 aliases的作用；
放在特殊符号前，则该特殊符号的作用消失；
放在指令的最末端，表示指令连接下一行。

```bash
# type rmrm is aliased to `rm -i'
# \\rm ./*.log
```
上例，我在 rm 指令前加上 escape 字元，作用是暂时取消别名的功能，将 rm 指令还原。
```bash
# bkdir=/home# echo \"Backup dir, \\$bkdir = $bkdir\"Backup dir,$bkdir = /home
```
上例 echo 内的 \\$bkdir，escape 将 $ 变数的功能取消了，因此，会输出 $bkdir，而第二个 $bkdir则会输出变数的内容 /home。


# | 管道 (pipeline)
pipeline 是 UNIX 系统，基础且重要的观念。连结上个指令的标准输出，做为下个指令的标准输入。

```bash
who | wc -l
# wc命令的功能为统计指定文件中的字节数、字数、行数, 并将统计结果显示输出。
# - c 统计字节数。
# - l 统计行数。
# - w 统计字数。
```
善用这个观念，对精简 script 有相当的帮助。


# ! 惊叹号(negate or reverse)
通常它代表反逻辑的作用，譬如条件侦测中，用 != 来代表”不等于”
```bash
if [ "$?" != 0 ]then
   echo "Executes error"
   exit 1
fi
```
在规则表达式中她担任 “反逻辑” 的角色
```bash
ls a[!0-9]
```
上例，代表显示除了a0, a1 …. a9 这几个文件的其他文件。


# : 冒号
在 bash 中，这是一个内建指令：”什么事都不干”，但返回状态值 0。
```bash
:
echo $? # 回应为 0
: > f.$$
```
上面这一行，相当于 cat /dev/null > f.$$。不仅写法简短了，而且执行效率也好上许多。

有时，也会出现以下这类的用法
```bash
: ${HOSTNAME?} ${USER?} ${MAIL?}
```
这行的作用是，检查这些环境变数是否已设置，没有设置的将会以标准错误显示错误讯息。像这种检查如果使用类似 test 或 if 这类的做法，基本上也可以处理，但都比不上上例的简洁与效率。
除了上述之外，还有一个地方必须使用冒号

```bash
export PATH=$PATH:$HOME/fbin:$HOME/fperl:/usr/local/mozilla

export LD_LIBRARY_PATH=$(pwd)/lib:/usr/local/lib:/usr/lib
export PATH=$(pwd):$PATH
```
在使用者自己的HOME 目录下的 .bash_profile 或任何功能相似的档案中，设定关于”路径”的场合中，我们都使用冒号，来做区隔。


# ? 问号 (wild card)
在文件名扩展(Filename expansion)上扮演的角色是匹配**一个**任意的字元，但**不包含 null 字元**。
```bash
# ls a?a1
```
善用她的特点，可以做比较精确的档名匹配。

# * 星号 (wild card)
相当常用的符号。在文件名扩展(Filename expansion)上，她用来代表**任何字元**，**包含 null 字元**。
```bash
# ls a*a a1 access_log
```
在运算时，它则代表 “乘法”。
```bash
let “fmult=2*3″
```
除了内建指令 let，还有一个关于运算的指令 expr，星号在这里也担任”乘法”的角色。不过在使用上得小心，他的前面必须加上escape 字元。

# ** 次方运算
两个星号在运算时代表 “次方” 的意思。

```bash
let “sus=2**3″

echo “sus = $sus” # sus = 8
```



# $ 钱号(dollar sign)

## 1. 变量替换(Variable Substitution)的代表符号。
```bash
vrs=123
echo "vrs = $vrs" # vrs = 123
```
另外，在 Regular Expressions 里被定义为 “行” 的最末端 (end-of-line)。这个常用在 grep、sed、awk 以及 vim(vi) 当中。

## 2. ${} 变量的正规表达式
bash 对 ${} 定义了不少用法。以下是取自线上说明的表列
```bash
${parameter:-word} ${parameter:=word} ${parameter:?word}
${parameter:+word} ${parameter:offset} ${parameter:offset:length}
${!prefix*} ${#parameter} ${parameter#word} ${parameter##word}
${parameter%word} ${parameter%%word} ${parameter/pattern/string}
${parameter//pattern/string}
```

## 3. $*
$* 引用script 的执行引用变量，引用参数的算法与一般指令相同，指令本身为0，其后为1，然后依此类推。引用变量的代表方式如下：
```bash
$0, $1, $2, $3, $4, $5, $6, $7, $8, $9, ${10}, ${11}…..
```
个位数的，可直接使用数字，但两位数以上，则必须使用 {} 符号来括住。
$* 则是代表所有引用变量的符号。使用时，得视情况加上双引号。
```bash
echo “$*”
```

## 4. $@
$@ 与 $* 具有相同作用的符号，不过她们两者有一个不同点。
符号 $* 将所有的引用变量视为一个整体。但符号 $@ 则仍旧保留每个引用变量的区段观念。

## 5. $#
这也是与引用变量相关的符号，她的作用是告诉你，引用变量的总数量是多少。
```bash
echo “$#”
```

## 6. $? 状态值 (status variable)

一般来说，UNIX(linux) 系统的进程以执行系统调用exit() 来结束的。这个回传值就是status值。回传给父进程，用来检查子进程的执行状态。
一般指令程序倘若执行成功，其回传值为 0；失败为 1。
```bash
tar cvfz dfbackup.tar.gz /home/user > /dev/nullecho “$?”
```

## 7. $$
由于进程的ID是唯一的，所以在同一个时间，不可能有重复性的 PID。有时，script 会需要产生临时文件，用来存放必要的资料。
而此script亦有可能在同一时间被使用者们使用。在这种情况下，固定文件名在写法上就显的不可靠。唯有产生动态文件名，才能符合需要。
符号$$或许可以符合这种需求。它代表当前shell 的 PID。
```bash
echo “$HOSTNAME, $USER, $MAIL” > ftmp.$$
```
使用它来作为文件名的一部份，可以避免在同一时间，产生相同文件名的覆盖现象。
ps: 基本上，系统会回收执行完毕的 PID，然后再次依需要分配使用。所以 script 即使临时文件是使用动态档名的写法，如果 script 执行完毕后仍不加以清除，会产生其他问题。

# () 指令群组 (command group)
用括号将一串连续指令括起来，这种用法对 shell 来说，称为指令群组。如下面的例子：
```bash
(cd ~ ; vcgh=`pwd` ; echo $vcgh)
```
指令群组有一个特性，shell会以产生 subshell来执行这组指令。因此，在其中所定义的变数，仅作用于指令群组本身。我们来看个例子
```bash
# cat ftmp-01#!/bin/basha=fsh(a=incg ; echo -e “/n $a /n”)echo $a# ./ftmp-01incgfsh
```
除了上述的指令群组，括号也用在 array 变数的定义上；另外也应用在其他可能需要加上escape 字元才能使用的场合，如运算式。

## 1. (()))

这组符号的作用与 let 指令相似，用在算数运算上，是 bash 的内建功能。所以，在执行效率上会比使用 let 指令要好许多。

```bash
#!/bin/bash
(( a = 10 ))
echo -e "inital value, a = $a/n"
(( a++ ))
echo "after a++, a = $a"
```

# {} 大括号 (Block of code)

## 1. 常规用法

### 1.1. 大括号拓展

(通配(globbing))将对大括号中的文件名做扩展。在大括号中，不允许有空白，除非这个空白被引用或转义。第一种：对大括号中的以逗号分割的文件列表进行拓展。如 touch {a,b}.txt 结果为a.txt b.txt。第二种：对大括号中以点点（..）分割的顺序文件列表起拓展作用，如：touch {a..d}.txt 结果为a.txt b.txt c.txt d.txt

```bash
# ls {ex1,ex2}.sh
ex1.sh  ex2.sh
# ls {ex{1..3},ex4}.sh
ex1.sh  ex2.sh  ex3.sh  ex4.sh
# ls {ex[1-3],ex4}.sh
ex1.sh  ex2.sh  ex3.sh  ex4.sh
```

### 1.2. 代码块

又被称为内部组，这个结构事实上创建了一个匿名函数 。与小括号中的命令不同，大括号内的命令不会新开一个子shell运行，即脚本余下部分仍可使用括号内变量。括号内的命令间用分号隔开，最后一个也必须有分号。**{}的第一个命令和左括号之间必须要有一个空格**。
大括号也被运用在 “函数” 的功能上。广义地说，单纯只使用大括号时，作用就像是个没有指定名称的函数一般。因此，这样写 script 也是相当好的一件事。尤其对输出输入的重导向上，这个做法可精简 script 的复杂度。



## 2. 几种特殊的替换结构

```bash
${var:-string}
${var:+string}
${var:=string}
${var:?string}
```

1、${var:-string}和${var:=string}:若变量var为空，则用在命令行中用string来替换${var:-string}，否则变量var不为空时，则用变量var的值来替换${var:-string}；对于${var:=string}的替换规则和${var:-string}是一样的，所不同之处是${var:=string}若var为空时，用string替换${var:=string}的同时，把string赋给变量var： ${var:=string}很常用的一种用法是，判断某个变量是否赋值，没有的话则给它赋上一个默认值。

2、${var:+string}的替换规则和上面的相反，即只有当var不是空的时候才替换成string，若var为空时则不替换或者说是替换成变量 var的值，即空值。(因为变量var此时为空，所以这两种说法是等价的)

3、{var:?string}替换规则为：若变量var不为空，则用变量var的值来替换${var:?string}；若变量var为空，则把string输出到标准错误中，并从脚本中退出。我们可利用此特性来检查是否设置了变量的值。

**补充扩展**：在上面这五种替换结构中string不一定是常值的，可用另外一个变量的值或是一种命令的输出。

此外，大括号还有另一种用法，如下

```bash
{xx,yy,zz,…}
```
这种大括号的组合，常用在字串的组合上，来看个例子
```bash
mkdir {userA,userB,userC}-{home,bin,data}
```
我们得到
userA-home, userA-bin, userA-data, userB-home, userB-bin,userB-data, userC-home, userC-bin,userC-data，这几个目录。这组符号在适用性上相当广泛。能加以善用的话，回报是精简与效率。像下面的例子
```bash
chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}
```
如果不是因为这种用法，我们得写几行重复几次呀！



## 3. 四种模式匹配替换结构

模式匹配记忆方法：

```bash
# 是去掉左边(在键盘上#在$之左边)
% 是去掉右边(在键盘上%在$之右边)
```

\#和%中的单一符号是最小匹配，两个相同符号是最大匹配。

```bash
${var%pattern}
${var%%pattern
${var#pattern}
${var##pattern}
```

- 第一种模式：${variable%pattern}，这种模式时，shell在variable中查找，看它是否以给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最短的匹配模式
- 第二种模式： ${variable%%pattern}，这种模式时，shell在variable中查找，看它是否以给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
- 第三种模式：${variable#pattern} 这种模式时，shell在variable中查找，看它是否以给的模式pattern开始，如果是，就从命令行把variable中的内容去掉左边最短的匹配模式
- 第四种模式： ${variable##pattern} 这种模式时，shell在variable中查找，看它是否以给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式
- 这四种模式中都不会改变variable的值，其中，只有在pattern中使用了*匹配符号时，%和%%，#和##才有区别。结构中的pattern支持通配符，*表示零个或多个任意字符，?表示仅与一个任意字符匹配，[...]表示匹配中括号里面的字符，[!...]表示不匹配中括号里面的字符。

```bash
# var=testcase
# echo $var
testcase
# echo ${var%s*e}
testca
# echo $var
testcase
# echo ${var%%s*e}
te
# echo ${var#?e}
stcase
# echo ${var##?e}
stcase
# echo ${var##*e}

# echo ${var##*s}
e
# echo ${var##test}
case
```

## 4. 字符串提取和替换

```bash
${var:num}
${var:num1:num2}
${var/pattern/pattern}
${var//pattern/pattern}
```

- 第一种模式：${var:num}，这种模式时，shell在var中提取第num个字符到末尾的所有字符。若num为正数，从左边0处开始；若num为负数，从右边开始提取字串，但必须使用在冒号后面加空格或一个数字或整个num加上括号，如${var: -2}、${var:1-3}或${var:(-2)}。
- 第二种模式：${var:num1:num2}，num1是位置，num2是长度。表示从$var字符串的第$num1个位置开始提取长度为$num2的子串。不能为负数。
- 第三种模式：${var/pattern/pattern}表示将var字符串的第一个匹配的pattern替换为另一个pattern。
- 第四种模式：${var//pattern/pattern}表示将var字符串中的所有能匹配的pattern替换为另一个pattern。

```bash
[root@centos ~]# var=/home/centos
[root@centos ~]# echo $var
/home/centos
[root@centos ~]# echo ${var:5}
/centos
[root@centos ~]# echo ${var: -6}
centos
[root@centos ~]# echo ${var:(-6)}
centos
[root@centos ~]# echo ${var:1:4}
home
[root@centos ~]# echo ${var/o/h}
/hhme/centos
[root@centos ~]# echo ${var//o/h}
/hhme/cenths
```



# [] 中括号

常出现在流程控制中，扮演括住判断式的作用。
```bash
if [ "$?" != 0 ]then
   echo "Executes error"
   exit 1
fi
```
这个符号在正则表达式中担任类似 “范围” 或 “集合” 的角色
```bash
rm -r 200[1234]
```
上例，代表删除 2001, 2002, 2003, 2004 等目录的意思。

# [[]]
这组符号与先前的 [] 符号，基本上作用相同，但她允许在其中直接使用 || 与 && 逻辑等符号。
```bash
#!/bin/bash
read ak
if [[ $ak > 5 || $ak < 9 ]]then
   echo $ak
fi
```

# || && &

## 1. ||或逻辑符号
这个会时常看到，代表 or 逻辑的符号。

## 2. && 逻辑符号
这个也会常看到，代表 and 逻辑的符号。

## 3. &后台工作
单一个& 符号，且放在完整指令列的最后端，即表示将该指令列放入后台中工作。

```bash
tar cvfz data.tar.gz data > /dev/null &
```


# / 单字边界
这组符号在规则表达式中，被定义为”边界”的意思。譬如，当我们想找寻 the 这个单字时，如果我们用
```bash
grep the FileA
```
你将会发现，像 there 这类的单字，也会被当成是匹配的单字。因为 the 正巧是 there 的一部份。如果我们要必免这种情况，就得加上 “边界” 的符号
```bash
grep ‘/' FileA
```

# + 加号 (plus)
在运算式中，她用来表示 “加法”。
```bash
expr 1 + 2 + 3
```
此外在规则表达式中，用来表示”很多个”的前面字元的意思。
```bash
# grep '10/+9′ fileB109100910000910000931010009#这个符号在使用时，前面必须加上 escape 字元。
```

# - 减号 (dash)
在运算式中，她用来表示 “减法”。
```bash
expr 10 – 2
```
此外也是系统指令的选项符号。
```bash
ls -expr 10 – 2
```
在 GNU 指令中，如果单独使用 – 符号，不加任何该加的文件名称时，代表”标准输入”的意思。这是 GNU 指令的共通选项。譬如下例
```bash
tar xpvf -
```
这里的 – 符号，既代表从标准输入读取资料。
不过，在 cd 指令中则比较特别
```bash
cd -
```
这代表变更工作目录到”上一次”工作目录。

# % 除法 (Modulo)
在运算式中，用来表示 “除法”。
```bash
expr 10 % 2
```
此外，也被运用在关于变量的规则表达式当中的下列
```bash
${parameter%word}${parameter%%word}
```
一个 % 表示最短的 word 匹配，两个表示最长的 word 匹配。

# = 等号 (Equals)
常在设定变数时看到的符号。
```bash
vara=123
echo "vara = $vara"
```
或者像是 PATH 的设定，甚至应用在运算或判断式等此类用途上。

# == 等号 (Equals)
常在条件判断式中看到，代表 “等于” 的意思。
```bash
if [ $vara == $varb ]
…下略
```

# != 不等于
常在条件判断式中看到，代表 “不等于” 的意思。
```bash
if [ $vara != $varb ]
…下略
```

# ^
这个符号在规则表达式中，代表行的 “开头” 位置