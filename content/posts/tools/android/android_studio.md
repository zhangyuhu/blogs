---
title: "android studio 安装使用"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "android studio 安装使用"
lightgallery: true
tags: [android studio]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [下载](#%E4%B8%8B%E8%BD%BD)
- [安装](#%E5%AE%89%E8%A3%85)
- [配置](#%E9%85%8D%E7%BD%AE)
- [插件](#%E6%8F%92%E4%BB%B6)
- [快捷键设置](#%E5%BF%AB%E6%8D%B7%E9%94%AE%E8%AE%BE%E7%BD%AE)

<!-- /TOC -->

# 1. 下载
[studio](https://developer.android.google.cn/studio)

# 2. 安装

```bash
mv android-studio-2021.2.1.15-linux.tar.gz /opt/
cd /opt
sudo tar -zxvf android-studio-2021.2.1.15-linux.tar.gz
```

更改 /opt/android-studio/bin/idea.porperties：

```
sudo gedit /opt/android-studio/bin/idea.properties
```

文末添加

```javascript
disable.android.first.run=true
```

此操作防止第一次打开Android Studio时卡在”Fetching Android SDK component information” 。

更改/opt/android-studio/bin目录权限：

```
sudo chmod 777 /opt/android-studio/bin
```



# 配置

![image-20220729164622788](../images/image-20220729164622788.png)



![image-20220729164645373](../images/image-20220729164645373.png)



![image-20220729164829551](../images/image-20220729164829551.png)



# 插件

**Rainbow Brackets**



# 快捷键设置

参考: [【Android -- 开发工具】Android Studio 设置快捷键](https://blog.csdn.net/duoduo_11011/article/details/78606144)

`File → Settings → keymap`

`代码返回快捷键 ： Ctrl + Alt + left/right`
