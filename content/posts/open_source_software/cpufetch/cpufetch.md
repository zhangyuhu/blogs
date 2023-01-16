---
title: "开源软件:cpufetch"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [cpufetch]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [1. CPUFetch](#1-cpufetch)
- [2. 开源地址](#2-开源地址)
- [3. 编译运行](#3-编译运行)
- [4. 运行效果](#4-运行效果)
- [5. bash 启动打印CPU信息](#5-bash-启动打印cpu信息)

<!-- /TOC -->

# 1. CPUFetch

CPUFetch是一个简单的命令行工具，与Neofetch有点类似，但是用于在Linux，Windows，macOS和Android中获取CPU体系结构。

该工具会输出制造商徽标（例如Intel，AMD）以及基本的CPU信息，包括：

- CPU名称。
- 微架构。
- 纳米（nm）的半导体技术。
- 最大频率。
- 核心和线程数。
- 高级向量扩展（AVX）
- 融合乘加（FMA）
- L1，L2，L3缓存大小。
- 峰值性能。



# 2. 开源地址

[cpufetch](https://github.com/Dr-Noob/cpufetch)


# 3. 编译运行

```shell
git clone https://github.com/Dr-Noob/cpufetch
cd cpufetch
make
./cpufetch
```


# 4. 运行效果

![image-20210416135747969](../image/image-20210416135747969.png)

# 5. bash 启动打印CPU信息

1. 请将可执行文件复制到/usr/local/bin/

   ```shell
    sudo mv cpufetch /usr/local/bin/
   ```

2. .修改 .bashrc

   ```
   cd ~
   vi .bashrc
   ```

   在.bashrc 最后位置添加

   ```shell
   # start cpufetch
   cpufetch
   ```

   重新打开终端
