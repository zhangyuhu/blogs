---
title: "cmake 知识整理"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "cmake 编程"
lightgallery: true
tags: [cmake]
categories: [cmake]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [CMake](#cmake)
- [CMake中常用预定义变量](#cmake中常用预定义变量)
  - [CMake的预定义变量](#cmake的预定义变量)
  - [系统信息预定义变量](#系统信息预定义变量)
  - [开关选项](#开关选项)
- [常用语法](#常用语法)
  - [CMAKE\_MINIMUM\_REQUIRED](#cmake_minimum_required)
  - [PROJECT](#project)
    - [外部构建与内部构建](#外部构建与内部构建)
  - [SET](#set)
  - [ADD\_SUBDIRECTORY](#add_subdirectory)
  - [INCLUDE\_DIRECTORIES](#include_directories)
  - [ADD\_EXECUTABLE](#add_executable)
  - [ADD\_LIBRARY](#add_library)
  - [变量EXECUTABLE\_OUTPUT\_PATH, LIBRARY\_OUTPUT\_PATH](#变量executable_output_path-library_output_path)
  - [LINK\_DIRECTORIES](#link_directories)
  - [TARGET\_LINK\_LIBRARIES](#target_link_libraries)
  - [MESSAGE](#message)
  - [SET\_TARGET\_PROPERTIES](#set_target_properties)
  - [AUX\_SOURCE\_DIRECTORY](#aux_source_directory)
- [基本控制语法](#基本控制语法)
- [实战](#实战)
  - [单个源文件](#单个源文件)
  - [多个源文件](#多个源文件)
    - [同一目录，多个源文件](#同一目录多个源文件)
    - [多个目录，多个源文件](#多个目录多个源文件)
  - [自定义编译选项](#自定义编译选项)
  - [安装和测试](#安装和测试)
    - [定制安装规则](#定制安装规则)
    - [为工程添加测试](#为工程添加测试)
- [参考资料](#参考资料)

<!-- /TOC -->

# CMake
**CMakeLists.txt**
>>
    cmake PATH
    ccmake PATH
    ccmake 和 cmake 的区别在于前者提供了一个交互式的界面
    PATH 是 CMakeLists.txt 所在的目录
    使用 make 命令进行编译。

# CMake中常用预定义变量
## CMake的预定义变量

* PROJECT_SOURCE_DIR：工程根目录；
* PROJECT_BINARY_DIR：运行cmake命令的目录。笔者建议定义为${PROJECT_SOURCE_DIR}/ build下。具体原因见后文外部编译部分；
* CMAKE_INCLUDE_PATH：环境变量，非cmake变量；
* CMAKE_LIBRARY_PATH：环境变量；
* CMAKE_CURRENT_SOURCE_DIR：当前处理的CMakeLists.txt文件所在路径；
* CMAKE_CURRENT_BINARY_DIR：target编译目录；
* 使用ADD_SURDIRECTORY指令可以更改该变量的值；
* SET(EXECUTABLE_OUTPUT_PATH < dir >) 指令不会对该变量有影响，但改变了最终目标文件的存储路径；
* CMAKE_CURRENT_LIST_FILE：输出调用该变量的CMakeLists.txt的完整路径；
* CMAKE_CURRENT_LIST_LINE：输出该变量所在的行；
* CMAKE_MODULE_PATH：定义自己的cmake模块所在路径；
* EXECUTABLE_OUTPUT_PATH：重新定义目标二进制可执行文件的存放位置；
* LIBRARY_OUTPUT_PATH：重新定义目标链接库文件的存放位置；
* PROJECT_NAME：返回由PROJECT指令定义的项目名称；
* CMAKE_ALLOW_LOOSE_LOOP_CONSTRUCTS：用来控制IF...ELSE...语句的书写方式；

## 系统信息预定义变量
* CMAKE_MAJOR_VERSION cmake主版本号,如2.8.6中的2
* CMAKE_MINOR_VERSION cmake次版本号,如2.8.6中的8
* CMAKE_PATCH_VERSION cmake补丁等级,如2.8.6中的6
* CMAKE_SYSTEM 系统名称,例如Linux-2.6.22
* CMAKE_SYSTEM_NAME 不包含版本的系统名,如Linux
* CMAKE_SYSTEM_VERSION 系统版本,如2.6.22
* CMAKE_SYSTEM_PROCESSOR 处理器名称,如i686
* UNIX 在所有的类UNIX平台为TRUE,包括OS X和cygwin
* WIN32 在所有的win32平台为TRUE,包括cygwin

## 开关选项
* BUILD_SHARED_LIBS 控制默认的库编译方式。
注：如果未进行设置,使用ADD_LIBRARY时又没有指定库类型,默认编译生成的库都是静态库。
* CMAKE_C_FLAGS 设置C编译选项
* CMAKE_CXX_FLAGS 设置C++编译选项

# 常用语法
CMakeLists.txt 的语法比较简单，由命令、注释和空格组成，其中命令是不区分大小写的。符号 # 后面的内容被认为是注释。命令由命令名称、小括号和参数组成，参数之间使用空格进行间隔。

1. 每一个需要进行cmake操作的目录下面，都必须存在文件CMakeLists.txt 。
1. cmake指令不区分大小写。
1. cmake内置变量是区分大小写的，或者干脆就说，cmake的所有变量都是区分大小写的。
1. 变量使用${}方式取值，但是在 IF 控制语句中是直接使用变量名；
1. 指令(参数 1 参数 2…)，参数使用括弧括起，参数之间使用空格或分号分开；

## CMAKE_MINIMUM_REQUIRED
该语句一般都可以放置在CMakeLists.txt的开头，用于说明CMake最低版本要求。
这行命令是可选的，我们可以不写这句话，但在有些情况下，如果CMakeLists.txt文件中使用了一些高版本cmake特有的一些命令的时候，就需要加上这样一行，提醒用户升级到该版本之后再执行cmake。

```shell
cmake_minimum_required (VERSION 2.6)
```
## PROJECT
```shell
PROJECT(name)
```
name：工程名称
该指令一般置于CMakeLists.txt的开头，定义了工程的名称。但项目最终编译生成的可执行文件并不一定是这个项目名称。

执行了该条指令之后，将会自动创建两个变量：
```shell
< projectname >_BINARY_DIR：二进制文件保存路径；
< projectname >_SOURCE_DIR：源代码路径；
```
```shell
project(CRNode)
```
执行了上一条指令，即定义了一个项目名称CRNode，相应的会生成两个变量：CRNode_BINARY_DIR, CRNode_SOURCE_DIR。
cmake中预定义了两个变量：PROJECT_BINARY_DIR与PROJECT_SOURCE_DIR。
在这个例子中：

```shell
PROJECT_BINARY_DIR = CRNode_BINARY_DIR
PROJECT_SOURCE_DIR = CRNode_SOURCE_DIR
```

推荐直接使用PROJECT_BINARY_DIR与PROJECT_SOURCE_DIR，这样及时项目名称发生了变化，也不会影响CMakeLists.txt文件。
关于上面两个变量是否相同的问题，涉及到编译方法是内部编译还是外部编译。如果是内部编译，则上面两个变量相同；如果是外部编译，则两个变量不同。

### 外部构建与内部构建
假设此时已经完成了CMakeLists.txt的编写，在CMakeLists.txt所在目录下，有两种执行cmake的方法：
```shell
cmake ./
make
```
以及：
```shell
mkdir build
cd ./build
cmake ../
make
```
第一种方法是内部构建，第二种方法是外部构建。上述两种方法中，最大不同在于cmake与make的工作路径不同。
内部构建方法中，cmake生成的中间文件和可执行文件都会存放在项目目录中；外部构建方法中，中间文件与可执行文件都存放在build目录中。
建议使用外部构建方法。优点显而易见：最大限度的保持了代码目录的整洁，生成、编译与安装是不同于项目目录的其他目录中，在外部构建方法下，PROJECT_SOURCE_DIR指向目录与内部构建相同，为CMakeLists.txt所在根目录；而PROJECT_BINARY_DIR不同，它指向CMakeLists.txt所在根目录下的build目录。

## SET
```powershell
SET(VAR [VALUE] [CACHE TYPEDOCSTRING [FORCE]])
```
eg:
```shell
SET(CMAKE_INSTALL_PREFIX /usr/local)
```
我们显式的将CMAKE_INSTALL_PREFIX的值定义为/usr/local，如此在外部构建情况下执行make install命令时，make会将生成的可执行文件拷贝到/usr/local/bin目录下。
当然，可执行文件的安装路径CMAKE_INSTALL_PREFIX也可以在执行cmake命令的时候指定，cmake参数如下：
```shell
cmake -DCMAKE_INSTALL_PREFIX=/usr ..
```
如果cmake参数和CMakeLists.txt文件中都不指定该值的话，则该值为默认的/usr/local。

## ADD_SUBDIRECTORY
```shell
ADD_SUBDIRECTORY(source_dir [binary_dir] [EXCLUDE_FROM_ALL])
```
* source_dir：源文件路径；
* [binary_dir]：中间二进制与目标二进制存放路径；
* [EXECLUDE_FROM_ALL]：将这个目录从编译过程中排除；

这个指令用于向当前工程添加存放源文件的子目录，并可以指定中间二进制和目标二进制存放的位置。
EXCLUDE_FROM_ALL 参数的含义是将这个目录从编译过程中排除。比如，工程有时候存在example，可能就需要工程构建完成后，再进入example目录单独进行构建。

## INCLUDE_DIRECTORIES
```shell
INCLUDE_DIRECTORIES([AFTER|BEFORE] [SYSTEM] dir1 dir2 ...)
```
* [AFTER|BEFORE]：追加标志，指定控制追加或置前；
* [SYSTEM]：（笔者也不知道干嘛用的）
* dir1, …, dir n：添加的一系列头文件搜索路径；

向工程添加多个特定的头文件搜索路径，路径之间用空格分隔。类似于gcc中的编译参数-l，即指定编译过程中编译器搜索头文件的路径。当项目需要的头文件不在系统默认的搜索路径时，则指定该路径。
AFTER/BEFORE参数，控制追加或置前。默认情况下，追加当前头文件搜索路径的后面。
注：如果路径包含空格，可以使用双引号将它括起来。
eg：

```shell
INCLUDE_DIRECTORIES(/usr/include/thrift)
```

## ADD_EXECUTABLE
```shell
ADD_EXECUTABLE(exename srcname)
```
* exename：可执行文件名
* srcname：生成该可执行文件的源文件
该命令给出源文件名称，并指出需要编译出的可执行文件名。
eg：
```shell
ADD_EXECUTABLE(hello ${SRC_LIST})
```
上述例程说明SRC_LIST变量中的源文件，需要编译出名为hello的可执行文件。

eg:
```shell
SET(SRC_LIST main.cc
        rpc/CRNode.cpp
        rpc/Schd_types.cpp
        task/TaskExecutor.cpp
        task/TaskMoniter.cpp
        util/Const.cpp
        util/Globals.cc
        )

ADD_EXECUTABLE(CRNode ${SRC_LIST})
```
该例程中，定义了该工程会生成一个名为CRNode的可执行文件，所依赖的源文件是变量SRC_LIST定义的源文件列表。
注：如果前文PROJECT()指令中定义的项目名称也定义为CRNode，也没有什么问题，两者之间没有任何关系。

## ADD_LIBRARY
```shell
ADD_LIBRARY(libname [SHARED|STATIC|MODULE] [EXCLUDE_FROM_ALL] source1 source2 ... sourceN)
```
* libname：库文件名称；
* [SHARED|STATIC|MODULE]：生成库文件类型（共享库/静态库）
* [EXCLUDE_FROM_ALL]：表示该库不会被默认构建
* source1, …, sourceN：生成库所依赖的源文件
eg：
```shell
ADD_LIBRARY(hello SHARED ${LIBHELLO_SRC})
```
## 变量EXECUTABLE_OUTPUT_PATH, LIBRARY_OUTPUT_PATH
EXECUTABLE_OUTPUT_PATH为生成可执行文件路径，LIBRARY_OUTPUT_PATH为生成库文件路径。
我们可以通过SET指令对其进行设置最终的目标二进制的位置，即最终生成的工程可执行文件与最终的共享库，而不包含编译生成的中间文件。
```shell
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)
```
注：指令ADD_EXECUTABLE, ADD_LIBRARY出现的位置，如果需要改变目标存放路径，就在该处添加上述定义。

## LINK_DIRECTORIES
```shell
LINK_DIRECTORIES(directory1 directory2 ...)
```
该指令用于添加外部库的搜索路径。
## TARGET_LINK_LIBRARIES
```shell
TARGET_LINK_LIBRARIES(target library1 <debug | optimized> library2 ..)
```
* target：目标文件；
* library1, …, libraryN：链接外部库文件；
指定链接目标文件时需要链接的外部库，效果类似于gcc编译参数-L，解决外部库依赖的问题。

## MESSAGE
```shell
MESSAGE([SEND_ERROR | STATUS | FATAL_ERROR] “message to display” …)
```
向终端输出用户定义的信息或变量值
* SEND_ERROR：产生错误，生成过程被跳过；
* STATUS：输出前缀为 – 的信息；
* FATAL_ERROR：立即终止所有cmake过程；

## SET_TARGET_PROPERTIES
```shell
SET_TARGET_PROPERTIES(target1 target2 ...
            PROPERTIES prop1 value1 prop2 value2 ...)
```
设置目标的某些属性，改变它们构建的方式。

## AUX_SOURCE_DIRECTORY

# 基本控制语法

# 实战
## 单个源文件
``` shell
# CMake 最低版本号要求
# cmake_minimum_required：指定运行此配置文件所需的 CMake 的最低版本
cmake_minimum_required (VERSION 2.8)

# 项目信息
# project：参数值是 Demo1，该命令表示项目的名称是 Demo1
project (Demo1)

# 指定生成目标
# add_executable： 将名为 main.cc 的源文件编译成一个名称为 Demo 的可执行文件
add_executable(Demo main.cc)
```
> cmake .

## 多个源文件
### 同一目录，多个源文件

```shell
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)

# 项目信息
project (Demo2)

# 指定生成目标
add_executable(Demo main.cc MathFunctions.cc)
```
aux_source_directory: 查找指定目录下的所有源文件，然后将结果存进指定变量名。
```shell
 aux_source_directory(<dir> <variable>)
```
```shell
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)

# 项目信息
project (Demo2)

# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)

# 指定生成目标
add_executable(Demo ${DIR_SRCS})
```

### 多个目录，多个源文件
add_subdirectory : 项目包含一个子目录 math，这样 math 目录下的 CMakeLists.txt 文件和源代码也会被处理。
target_link_libraries：指明可执行文件 main 需要连接一个名为 MathFunctions 的链接库 。

``` shell
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)

# 项目信息
project (Demo3)

# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)

# 添加 math 子目录
add_subdirectory(math)

# 指定生成目标
add_executable(Demo main.cc)

# 添加链接库
target_link_libraries(Demo MathFunctions)
```
子目录中的 CMakeLists.txt：
```shell
# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_LIB_SRCS 变量
aux_source_directory(. DIR_LIB_SRCS)

# 生成链接库
add_library (MathFunctions ${DIR_LIB_SRCS})

```
该文件中使用命令 add_library 将 src 目录中的源文件编译为静态链接库。

## 自定义编译选项
CMake 允许为项目增加编译选项，从而可以根据用户的环境和需求选择最合适的编译方案。
```shell{.line-numbers}
# CMake 最低版本号要求
cmake_minimum_required (VERSION 2.8)

# 项目信息
project (Demo4)

# 加入一个配置头文件，用于处理 CMake 对源码的设置
configure_file (
  "${PROJECT_SOURCE_DIR}/config.h.in"
  "${PROJECT_BINARY_DIR}/config.h"
  )

# 是否使用自己的 MathFunctions 库
option (USE_MYMATH
       "Use provided math implementation" ON)

# 是否加入 MathFunctions 库
if (USE_MYMATH)
  include_directories ("${PROJECT_SOURCE_DIR}/math")
  add_subdirectory (math)
  set (EXTRA_LIBS ${EXTRA_LIBS} MathFunctions)
endif (USE_MYMATH)

# 查找当前目录下的所有源文件
# 并将名称保存到 DIR_SRCS 变量
aux_source_directory(. DIR_SRCS)

# 指定生成目标
add_executable(Demo ${DIR_SRCS})
target_link_libraries (Demo  ${EXTRA_LIBS})

```
其中：
第7行的 configure_file 命令用于加入一个配置头文件 config.h ，这个文件由CMake 从 config.h.in 生成，通过这样的机制，将可以通过预定义一些参数和变量来控制代码的生成。
第13行的 option 命令添加了一个 USE_MYMATH 选项，并且默认值为 ON 。
第17行根据 USE_MYMATH 变量的值来决定是否使用我们自己编写的 MathFunctions 库。

**config.h.in**
 ```
 #cmakedefine USE_MYMATH
 ```
 CMake 会自动根据 CMakeLists 配置文件中的设置自动生成 config.h 文件。

 从中可以找到刚刚定义的 USE_MYMATH 选项，按键盘的方向键可以在不同的选项窗口间跳转，按下 enter 键可以修改该选项。修改完成后可以按下 c 选项完成配置，之后再按 g 键确认生成 Makefile 。ccmake 的其他操作可以参考窗口下方给出的指令提示。

 ## 安装和测试
 CMake 也可以指定安装规则，以及添加测试。这两个功能分别可以通过在产生 Makefile 后使用 make install 和 make test 来执行。在以前的 GNU Makefile 里，你可能需要为此编写 install 和 test 两个伪目标和相应的规则，但在 CMake 里，这样的工作同样只需要简单的调用几条命令。

 ### 定制安装规则

 math/CMakeLists.txt 指明 MathFunctions 库的安装路径
```shell
 # 指定 MathFunctions 库的安装路径
 install (TARGETS MathFunctions DESTINATION bin)
 install (FILES MathFunctions.h DESTINATION include)
```

改根目录的 CMakeLists 文件
```shell
# 指定安装路径
install (TARGETS Demo DESTINATION bin)
install (FILES "${PROJECT_BINARY_DIR}/config.h"
         DESTINATION include)
```
通过上面的定制，生成的 Demo 文件和 MathFunctions 函数库 libMathFunctions.o 文件将会被复制到 /usr/local/bin 中，而 MathFunctions.h 和生成的 config.h 文件则会被复制到 /usr/local/include 中。（这里的 /usr/local/ 是默认安装到的根目录，可以通过修改 CMAKE_INSTALL_PREFIX 变量的值来指定这些文件应该拷贝到哪个根目录）。

### 为工程添加测试
添加测试同样很简单。CMake 提供了一个称为 CTest 的测试工具。我们要做的只是在项目根目录的 CMakeLists 文件中调用一系列的 add_test 命令。
```shell
# 启用测试
enable_testing()

# 测试程序是否成功运行
add_test (test_run Demo 5 2)

# 测试帮助信息是否可以正常提示
add_test (test_usage Demo)
set_tests_properties (test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")

# 测试 5 的平方
add_test (test_5_2 Demo 5 2)

set_tests_properties (test_5_2
PROPERTIES PASS_REGULAR_EXPRESSION "is 25")

# 测试 10 的 5 次方
add_test (test_10_5 Demo 10 5)

set_tests_properties (test_10_5
PROPERTIES PASS_REGULAR_EXPRESSION "is 100000")

# 测试 2 的 10 次方
add_test (test_2_10 Demo 2 10)

set_tests_properties (test_2_10
PROPERTIES PASS_REGULAR_EXPRESSION "is 1024")
```
上面的代码包含了四个测试。第一个测试 test_run 用来测试程序是否成功运行并返回 0 值。剩下的三个测试分别用来测试 5 的 平方、10 的 5 次方、2 的 10 次方是否都能得到正确的结果。其中 PASS_REGULAR_EXPRESSION 用来测试输出是否包含后面跟着的字符串。

可以通过编写宏来实现
```shell
# 定义一个宏，用来简化测试工作
macro (do_test arg1 arg2 result)
  add_test (test_${arg1}_${arg2} Demo ${arg1} ${arg2})
  set_tests_properties (test_${arg1}_${arg2}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)

# 使用该宏进行一系列的数据测试
do_test (5 2 "is 25")
do_test (10 5 "is 100000")
do_test (2 10 "is 1024")
```

# 参考资料
[CMake 入门实战](https://www.hahack.com/codes/cmake/)
[CMake 入门实战源码](https://www.hahack.com/codes/cmake/)
[CMake语法](https://blog.csdn.net/ajianyingxiaoqinghan/article/details/70230902)
