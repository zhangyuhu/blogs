---
title: "ubuntu 常用软件安装"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "software install"
lightgallery: true
tags: [software]
categories: [tools]
---

# cmake 升级
```bash
apt remove cmake
cd /usr/src
wget https://github.com/Kitware/CMake/releases/download/v3.16.0/cmake-3.16.0.tar.gz
tar -zxvf cmake-3.16.0.tar.gz
cd cmake-3.16.0
apt install libssl-dev build-essential

./bootstrap && make && sudo make install

cmake --version
```
# readline
```bash
wget -c ftp://ftp.gnu.org/gnu/readline/readline-6.2.tar.gz

tar -zxvf readline-6.2.tar.gz

cd readline-6.2

./configure           //这个地方一定要指定路径,要不然要报错(一般在/usr/local下用软件命名,比如 /usr/local/readline下)

make && make installl

 ldconfig
```