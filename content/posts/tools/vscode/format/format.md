---
title: "vsoced代码格式化配置"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "工具使用"
lightgallery: true
tags: [vsoced]
categories: [tools]
---

<!-- TOC -->

- [代码管理工具](#%E4%BB%A3%E7%A0%81%E7%AE%A1%E7%90%86%E5%B7%A5%E5%85%B7)
- [代码格式化工具ClangFormat](#%E4%BB%A3%E7%A0%81%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%B7%A5%E5%85%B7clangformat)
    - [1. 简介](#1-%E7%AE%80%E4%BB%8B)
    - [2. 文档](#2-%E6%96%87%E6%A1%A3)
    - [3. 安装](#3-%E5%AE%89%E8%A3%85)
    - [4. 使用](#4-%E4%BD%BF%E7%94%A8)
        - [4.1. 使用vscode 手动格式化](#41-%E4%BD%BF%E7%94%A8vscode-%E6%89%8B%E5%8A%A8%E6%A0%BC%E5%BC%8F%E5%8C%96)
        - [4.2. git 自动格式化提交文件](#42-git-%E8%87%AA%E5%8A%A8%E6%A0%BC%E5%BC%8F%E5%8C%96%E6%8F%90%E4%BA%A4%E6%96%87%E4%BB%B6)
        - [4.3. 手动格式化](#43-%E6%89%8B%E5%8A%A8%E6%A0%BC%E5%BC%8F%E5%8C%96)
- [静态扫描工具](#%E9%9D%99%E6%80%81%E6%89%AB%E6%8F%8F%E5%B7%A5%E5%85%B7)
    - [1. cppcheck](#1-cppcheck)
        - [1.1. 安装](#11-%E5%AE%89%E8%A3%85)
        - [1.2. 使用](#12-%E4%BD%BF%E7%94%A8)
    - [2. Tscancode](#2-tscancode)
        - [2.1. 安装](#21-%E5%AE%89%E8%A3%85)
        - [2.2. 使用](#22-%E4%BD%BF%E7%94%A8)
- [文档自动生成工具  doxygen](#%E6%96%87%E6%A1%A3%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E5%B7%A5%E5%85%B7--doxygen)
    - [1. 安装](#1-%E5%AE%89%E8%A3%85)
        - [1.1. 常用配置](#11-%E5%B8%B8%E7%94%A8%E9%85%8D%E7%BD%AE)
        - [1.2. 根据配置生成doxygen文档](#12-%E6%A0%B9%E6%8D%AE%E9%85%8D%E7%BD%AE%E7%94%9F%E6%88%90doxygen%E6%96%87%E6%A1%A3)
    - [2. vscode  自动生成 Doxygen 注释](#2-vscode--%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90-doxygen-%E6%B3%A8%E9%87%8A)
        - [2.1. 下载](#21-%E4%B8%8B%E8%BD%BD)
        - [2.2. 配置文件](#22-%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)
        - [2.3. 使用](#23-%E4%BD%BF%E7%94%A8)
- [建议安装的插件及配置](#%E5%BB%BA%E8%AE%AE%E5%AE%89%E8%A3%85%E7%9A%84%E6%8F%92%E4%BB%B6%E5%8F%8A%E9%85%8D%E7%BD%AE)
    - [1. Code Spell Checker](#1-code-spell-checker)
- [其他配置](#%E5%85%B6%E4%BB%96%E9%85%8D%E7%BD%AE)
    - [1. 保存去除行尾字符](#1-%E4%BF%9D%E5%AD%98%E5%8E%BB%E9%99%A4%E8%A1%8C%E5%B0%BE%E5%AD%97%E7%AC%A6)
    - [2. 保存文件在文件末尾插入最终行](#2-%E4%BF%9D%E5%AD%98%E6%96%87%E4%BB%B6%E5%9C%A8%E6%96%87%E4%BB%B6%E6%9C%AB%E5%B0%BE%E6%8F%92%E5%85%A5%E6%9C%80%E7%BB%88%E8%A1%8C)

<!-- /TOC -->

# 代码管理工具

为了团队协作方便以及代码代码稳定性建设，团队内可以使用统一的工具进行代码格式化，生成文档等。这里整理下相关工具及导入工程实施的方法。

以下工具基于如Ubuntu 18.04 LTS环境进行验证，但工具到时跨平台，保持配置文件一致，其他平台环境安装需要使用的同学自行解决。



# 代码格式化工具ClangFormat

## 简介

- ClangFormat 是一个规范代码的工具
- ClangFormat 支持的语言有：C/C++/Java/JavaScript/Objective-C/Protobuf/C#
- ClangFormat 支持的规范有：LLVM，Google，Chromium，Mozilla 和 WebKit

## 文档

[官方文档](https://clang.llvm.org/docs/ClangFormat.html)

[其配置文件的官方文档](https://clang.llvm.org/docs/ClangFormatStyleOptions.html)

## 安装

```bash
# 安装
sudo apt install clang-format
# 查看版本
clang-format --version
```

## 使用

### 使用vscode 手动格式化

- 安装 插件 clang-format

- 将默认格式化工具设置为 clang-format

  vscode   设置中 搜索 `defaultFormatter` ，选择默认配置为 `clang-format` 。

  ![image-20211208193908026](../image/image-20211208193908026.png)

- 将配置文件 [.clang-format](http://wiki.intra.xiaojukeji.com/pages/viewpage.action?pageId=777922336) 放到工程根目录，以DVR 工程为例：

- 使用快捷格式化 ，windows：`Shift+Alt+F`      Mac：`Shift+Option+F`        Ubuntu：`Ctrl+Shift+I`

### git 自动格式化提交文件

   git pre-commit 在提交代码的时候对变动的文件进行格式化, 将 [pre-commit]() 拷贝到 git/hooks 目录下

   ```bash
   cp ./pre-commit ../../.git/hooks/
   ```

   此后git在提交的时候会进行自动格式化。

### 手动格式化
   - 使用 clang-format 命令

    ```bash
    clang-format -style=google -i  xxx.cpp   指定格式化方式
    clang-format -style=file  .clang-format -i  xxx.cpp   指定配置文件
    clang-format -i xxx.cpp # 使用工程默认
    ```

   - 使用脚本格式化以有文件

   现有代码可能格式不一致，可以使用提供的脚本进行统一格式化做单次提交到服务器。可以使用 tools/format 文件夹下 format.sh 对文件夹内文件递归进行格式化。

```bash
./format.sh  /home/didi/work/http/src/http
```


# 静态扫描工具

##  cppcheck

CppCheck是一个C/C++代码缺陷静态检查工具。不同于C/C++编译器及其它分析工具，CppCheck只检查编译器检查不出来的bug，不检查语法错误。所谓静态代码检查就是使用一个工具检查我们写的代码是否安全和健壮，是否有隐藏的问题

源码下载地址：[cppcheck ](https://github.com/danmar/cppcheck/releases)

###  安装

```bash
# 安装
sudo apt-get install cppcheck

# 查看版本
cppcheck --version
# Cppcheck 1.82 c++11 最好使用1.8 异常版本，如果apt 	安装非最新版本可以下载源码安装
```

### 使用

[使用参考](https://www.cnblogs.com/shanyu20/p/11238109.html)

```bash
cppcheck xxx/ --enable=all --output-file=./xxxx.txt
```


## Tscancode

TscanCode是一款腾讯开源的静态代码扫描工具，TscanCode旨在助力开发与测试人员从代码层面挖掘问题，将那些长期困扰项目的诸如空指针宕机等问题，扼杀于萌芽阶段。支持用户根据不同需求自定义配置检查项，有极强的扩展性和可维护性。平均扫描速度10W行/分钟。

TscanCode支持以下类型规则扫描：

1. 空指针检查，包含可疑的空指针，判空后解引用比如Crash等共3类subid检查。
2. 数据越界，Sprintf_S越界共1类subid检查。
3. 内存泄漏，分配和释放不匹配同1类subid检查。
4. 逻辑错误，重复的代码分支，bool类型和INT进行比较，表达式永远True或者false等共18类检查。
5. 可疑代码检查，if判断中含有可疑的=号，自由变量返回局部变量等共计15类检查。
6. 运算错误，判断无符号数小于0,对bool类型进行++自增等，共计11类检查。

代码仓库 ：[TscanCode](https://github.com/Tencent/TscanCode)


### 安装

release 文件下载地址 ：[release 文件下载](https://github.com/Tencent/TscanCode/tree/master/release)

### 使用
工具
[使用参考](https://www.jianshu.com/p/f99d9a5d25ac)

```bash
./tscancode --xml --enable=all -q ../../../src/ >scan_result.xml 2>&1
```

**说明** ： 工程里集成了脚本和生成的文件 见 ： tools/codescan/ 路径



# 文档自动生成工具  doxygen

## 安装

```bash
安装doxygen—> 安装Graphviz—>生成doxygen配置文件—>修改doxygen配置文件—>生成doxygen文档
```

安装doxygen
```bash
sudo apt-get install doxygen
```
安装Graphviz
```bash
sudo apt-get install graphviz
```
生成doxygen配置文件

```bash
doxygen -s -g
```
注释：上述s 和 g 分别表示simple和generate

### 常用配置
常用选项如下：

```bash
# 项目名称，将作为于所生成的程序文档首页标题
PROJECT_NAME        = “Test”
# 文档版本号，可对应于项目版本号，譬如 svn、cvs 所生成的项目版本号
PROJECT_NUMBER      = "1.0.0
# 程序文档输出目录
OUTPUT_DIRECTORY    =  /home/user1/docs
# 程序文档输入目录
INPUT                = /home/user1/project/kernel
# 程序文档语言环境
OUTPUT_LANGUAGE      = Chinese
# 只对头文件中的文档化信息生成程序文档
FILE_PATTERNS        =
# 递归遍历当前目录的子目录，寻找被文档化的程序源文件
RECURSIVE            = YES
# 如果是制作 C 程序文档，该选项必须设为 YES，否则默认生成 C++ 文档格式
OPTIMIZE_OUTPUT_FOR_C  = YES
#提取信息，包含类的私有数据成员和静态成员
EXTRACT_ALL            = yes
EXTRACT_PRIVATE        = yes
EXTRACT_STATIC        = yes
# 对于使用 typedef 定义的结构体、枚举、联合等数据类型，只按照 typedef 定义的类型名进行文档化
TYPEDEF_HIDES_STRUCT  = YES
# 在 C++ 程序文档中，该值可以设置为 NO，而在 C 程序文档中，由于 C 语言没有所谓的域/名字空间这样的概念，所以此处设置为 YES
HIDE_SCOPE_NAMES      = YES
# 让 doxygen 静悄悄地为你生成文档，只有出现警告或错误时，才在终端输出提示信息
QUIET  = YES
# 递归遍历示例程序目录的子目录，寻找被文档化的程序源文件
EXAMPLE_RECURSIVE      = YES
# 允许程序文档中显示本文档化的函数相互调用关系
REFERENCED_BY_RELATION = YES
REFERENCES_RELATION    = YES
REFERENCES_LINK_SOURCE = YES
# 不生成 latex 格式的程序文档
GENERATE_LATEX        = NO
# 在程序文档中允许以图例形式显示函数调用关系，前提是你已经安装了 graphviz 软件包
HAVE_DOT              = YES
CALL_GRAPH            = YES
CALLER_GRAPH          = YES
#在最后生成的文档中，把所有的源代码包含在其中
SOURCE BROWSER        = YES
$这会在HTML文档中，添加一个侧边栏，并以树状结构显示包、类、接口等的关系
GENERATE TREEVIEW      ＝ ALL
```
### 根据配置生成doxygen文档

```bash
doxygen Doxyfile
```

效果

- 文件

![image-20211208211602959](../image/image-20211208211602959.png)

- class

![image-20211208211519335](../image/image-20211208211519335.png)


- namespase

![image-20211208211632373](../image/image-20211208211632373.png)


## vscode  自动生成 Doxygen 注释

### 下载

vscode 下载插件 ：Generate Doxygen Comments

### 配置文件

安装插件后，File–Preferences–Settings-- 中打开 用户 setting.json文件 ,增加如下内容 (配置可以根据自己需要修改):

```bash
    // Doxygen documentation generator set
    "doxdocgen.file.copyrightTag" : [
        "@copyright Copyright (c) 2012-2021 Beijing Xiaoju Technology Co, Ltd."
    ],
    "doxdocgen.file.customTag": [
        "@par 修改日志:",
        "<table>",
        "<tr><th>Date           <th>Version  <th>Author         <th>Description",
        "<tr><td>{date}         <td>v1.0     <td>zhangyuhu      <td>内容",
        "</table>",
    ],
    "doxdocgen.file.fileOrder": [
        "file",
        "brief",
        "author",
        "version",
        "date",
        "copyright",
        "empty",
        "custom",
    ],
    "doxdocgen.file.fileTemplate": "@file {name}",
    "doxdocgen.file.versionTag": "@version v1.0",
    "doxdocgen.generic.authorEmail": "zhangyuhu@didiglobal.com",
    "doxdocgen.generic.authorName": "zhangyuhu",
    "doxdocgen.generic.authorTag": "@author {author} ({email})",

    "doxdocgen.generic.order": [
        "brief",
        "tparam",
        "param",
        "return"
    ],
    "doxdocgen.generic.paramTemplate": "@param{indent:8}{param}{indent:25}My Param doc",
    "doxdocgen.generic.returnTemplate": "@return {type} ",
    "doxdocgen.generic.splitCasingSmartText": true,
```



### 使用

[Vs code自动生成Doxygen格式注释](https://www.cnblogs.com/silencehuan/p/11875958.html)

在文件 / 函数等 输入 **/**** 回车可自动生成注释



#  建议安装的插件及配置



## Code Spell Checker

拼写检查工具

示例1:



![image-20211220174318509](../image/image-20211220174318509.png)

![image-20211220174348232](../image/image-20211220174348232.png)

示例2:

![image-20211220174628759](../image/image-20211220174628759.png)

![image-20211220174648762](../image/image-20211220174648762.png)







# 其他配置


## 保存去除行尾字符

设置中搜索 trimTrailingWhitespace ，并启用。

## 保存文件在文件末尾插入最终行

设置中搜索 insert final newline  ，并启用。

