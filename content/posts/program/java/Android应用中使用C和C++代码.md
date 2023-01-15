
---
title: "Android应用中使用C和C++代码"
date: 2023-01-15T17:52:52+08:00
draft: false
description: ""
lightgallery: true
tags: [java,jni]
categories: [java]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [Android NDK](#android-ndk)
- [JNI之C\&C++调用Java](#jni之cc调用java)
- [NDK-动态和静态库](#ndk-动态和静态库)
  - [1. 静态库](#1-静态库)
    - [1.1. 静态库编译流程:](#11-静态库编译流程)
    - [1.2. 静态库使用:](#12-静态库使用)
  - [2. 动态库](#2-动态库)
    - [2.1. 动态库编译流程:](#21-动态库编译流程)
    - [2.2. 动态库使用:](#22-动态库使用)
    - [2.3. readelf工具](#23-readelf工具)
  - [3. 总结](#3-总结)

<!-- /TOC -->

# Android NDK

[Android NDK](https://developer.android.com/ndk)


# JNI之C&C++调用Java

```C++
//使用jni调用java的代码, 必须了解java的反射机制;

//在jni中反射调用java代码的话, 一定要写异常处理


// jni中异常处理:

if (pEnv->ExceptionCheck()) //这里异常判断一定要写
{
	pEnv->ExceptionDescribe();
	pEnv->ExceptionClear();//如果出现异常,不清除,返回也会崩溃;
}

//这里就直接贴jni代码了, 详细在注释里说明;
//异常处理
#define isException() if (pEnv->ExceptionCheck())\
{pEnv->ExceptionDescribe();pEnv->ExceptionClear();}

JNIEXPORT jint JNICALL Java_org_bing_testjni_MainActivity_Add
  (JNIEnv *env, jobject obj, jint a, jint b)
{
//通过包名或的类的类型
//如果是静态函数, 那么第二个参数就是jclass
    jclass clsMainActivity = pEnv->FindClass("cr25/myjni/MainActivity");
isException();//异常处理

//获取构造
//参数1: 类的类型jclass
//参数2: 构造函数名称, java中所有类的构造函数名都是”<init>”
//参数3: 记录了参数个数, 类型, 返回值类型, 下面会详细说
jmethodID methodConstruct = pEnv->GetMethodID(clsMainActivity, "<init>", "()V");
isException();

//创建类对象
jobject  objMain = pEnv->NewObject(clsMainActivity, methodConstruct);
isException();

//获取javaAdd方法地址
jmethodID JavaAdd= pEnv->GetMethodID(clsMainActivity,"JavaAdd", "(II)I");
isException();

//执行函数, 注意这个CallXXXMethod, XXX是要根据返回值类型来确定
//如: CallByteMethod, CallStringMethod, CallFloatMethod
//因为在JAVA反射中可以用Object统一返回值, C++没法来统一返回值
int resutl = pEnv->CallIntMethod(objMain , JavaAdd, a, b);

//到这里就调用完了函数, 还说一下字段获取与修改
//获取字段
//参数1: jclass
//参数2: 字段名
//参数3: 字段类型记录
	jfieldID mData = pEnv->GetFieldID(clsMainActivity, "mData", "I");

//设置字段的值
    pEnv->SetIntField(objMainActivity, mData, 2);


   return resutl ;
}
```

Daalvik字节码类型描述符

```
单独说一下GetMethodID与GetFieldID第三个参数意义,
其实这些字符串就是代表了参数与返回值类型的的简写;
括号内的代表参数, 括号外代表返回值
如: (II)V
I是代表int , V是代表viod, 表示有两个int的参数, 返回值是void类型
如: (Ljava/lang/String;ILjava/lang/String;)J
Ljava/lang/String;代表是类的类型, 格式为L[包名]; 注意是”;”结束
J是代表Long
表示有三个参数, String, int, String, 返回值为Long

   V  void          只能用于返回值类型
   Z  boolean       变量值只能是 真(true) 或 假(false)
   B  byte          字节型
   S  short         短整型
   C  char          字符型
   I  int           整数型
   J  long（64位）  长整型
   F  float         浮点型
   D  double(64位)  双精度浮点型
   L  java类类型 package.name.ObjectName Lpackage/name/ObjectName;
   [  数组类型 [I表示一个整型一维数组，等于java中的int[]。注意多位数数组的维数最大为255个。
[[I=int[][]
[[[I=int[][][]
```

# NDK-动态和静态库

## 1. 静态库

### 1.1. 静态库编译流程:

> gcc------> obj 文件---->多个obj使用ar----->静态库文件

需要用到的核心命令:

```bash
gcc –g –c mod1.c mod2.c mod3.c           编译obj (debug版)
AR  r libdemo.a mod1.o mod2.o mod3.o       将3个obj 添加到libdemo.a中
AR  tv libdemo.a                          查看libdemo.a中的obj文件
AR  d libdemo.a mod3.o                    将mod3.o从libdemo.a中删除
```

### 1.2. 静态库使用:

代码中包含头文件, 在链接的时候使用libdemo.a链接即可;

## 2. 动态库

### 2.1. 动态库编译流程:

> gcc------> obj 文件---->多个obj使用gcc的 -shared----->so文件

需要用到的核心命令:

```bash
gcc -g -c -fpic -Wall mod1.c mod2.c mod3.c
gcc -g  -shared mod1.o mod2.o mod3.o -o libfoo.so
gcc -g  -fpic -Wall -shared mod1.o mod2.o mod3.o -o libfoo.so

-fpic 表示代码中地址全部使用相对地址, 不使用绝对地址,解决重定位
-c   只编译不链接
-Wall 显示所有警告
-g   生成Debug版
```

### 2.2. 动态库使用:

1. 静态使用
直接把So文件当作obj一起链接到文件, 文件中会存so信息, 在使用时会在目录中搜索so
linux动态库搜索顺序:  system -> lib -> 环境变量
增加新环境变量, 用于系统搜索到我们的so, 这个环境变量是一次性的
`adb shell export  LD_LIBRARY_PATH += /data/local/tmp`
2. 动态使用
使用api加载指定目录的so, 再使用;
dlopen打开一个so文件
dlsym根据函数名拿到函数指针

**一个工程代码如果同时有静态和动态编译, 那么就只编译动态库;**

### 2.3. readelf工具
使用readelf工具可以查看elf文件的符号表
命令: 平台-readelf - s  elf文件 ,当value值为0表示为导入函数

```C++
__attribute__() //关键字可以指定函数属性
```


如:

```C++
__attribute__(visibility("hidden")) // 隐藏函数
__attribute__(visibility("defult")) // 导出函数
```

以下在so的装载和卸载的时候被调用(老版本, 已经被淘汰, 但是可以用);

```C++
void _init() // 初始化函数
void _fini() // 反初始化函数
```

在新版本的初始化与反初始化机制中, 可以设置多个函数, 名字可以自定义;

```C++
void __attribute__((constructor))  OnLoad() // 初始化函数
void __attribute__((destructor))  UnLoad() // 反初始化
void __attribute__((constructor))  OnLoad1() // 初始化函数1
void __attribute__((destructor))  UnLoad1() // 反初始化1
```



## 3. 总结
1.相关函数
```
dlopen()函数打开一个共享库
dlsym()函数在库中搜
dlclose() 函数光比之前dlopen打开的库
dlerror() 函数返回一个错误消息的字符串
```
2.隐藏函数

```
void __attribute__ ((visibility("hidden"))) fun() {}
```
3. so构造析构(会在so加载和卸载的时候调用)
```
void  __attribute__ ((constructor)) Load()
void  __attribute__ ((destructor)) UnLoad()
```
4. _init()和_fini()函数 会在so加载和卸载时调用

5. _init函数比构造函数来的早 hidden后在用dlsym函数无法找到