---
title: "NFS:Network File System(网络文件系统)"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "NFS"
lightgallery: true
tags: [NFS]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。
<!-- TOC -->

- [什么是NFS](#什么是nfs)
  - [Network File System](#network-file-system)
  - [SUN RPC 基本原理](#sun-rpc-基本原理)
  - [NFS过程](#nfs过程)
  - [NFS客户端和NFS服务端通讯过程](#nfs客户端和nfs服务端通讯过程)
- [NFS服务的优缺点](#nfs服务的优缺点)
- [Ubuntu主机的NFS共享文件夹](#ubuntu主机的nfs共享文件夹)
  - [nfs server 安装](#nfs-server-安装)
  - [nfs client 安装](#nfs-client-安装)
  - [/etc/exports 参数](#etcexports-参数)
  - [mount 参数](#mount-参数)

<!-- /TOC -->

# 什么是NFS

## Network File System

网络文件系统（英语：Network File System，缩写作 NFS）是一种分布式文件系统，力求客户端主机可以访问服务器端文件，并且其过程与访问本地存储时一样，它由Sun微系统（已被甲骨文公司收购）开发，于1984年发布。

它基于开放网络运算远程过程调用（ONC RPC）系统：一个开放、标准的RFC系统，任何人或组织都可以依据标准实现它。

开放网络运算远程过程调用（英语：Open Network Computing Remote Procedure Call，缩写为ONC RPC），一种被广泛应用的远程过程调用（RPC）系统，是一种属于应用层的协议堆栈，底层为TCP/IP协议。开放网络运算（ONC）最早源自于Sun微系统（Sun），是网络文件系统计划的一部分，因此它经常也被称为Sun ONC 或 Sun RPC。现今在多数类UNIX系统上都实现了这套系统，微软公司也以Windows Services for UNIX在他们产品上提供ONC RPC的支持。2009年，Sun微系统以标准三条款的BSD许可证发布这套系统。2010年，收购了Sun微系统的甲骨文公司确认了这套软件BSD许可证的有效性与适用范围。


## SUN RPC 基本原理

- 客户端触发调用远程过程，实际上是选取了对应RPC程序封装的函       数，并将相关参数（包括远程方法名，方法参数等）封装成IP数据包，发送给服务器
- 服务器进程接收到此数据包后，提取参数，执行相应方法过程
- 服务器方法过程返回后，将返回结果封装成IP数据包，发送给客户端
- 客户端进程，收取到数据包后，提取返回结果

 RPC程序包提供了数据报文封装和传递，保证异构环境下的可用性，客户端和服务器端应用程序设计时不需要考虑系统差异带来的数据格式，编码解码等问题，提供了很大方便。

客户程序和服务程序更多的职责是在于去定义描述具体远程过程及其触发，并将其注册绑定到RPC程序包中。

## NFS过程

![](../image/nfs.png)

NFS客户可以是一个用户进程，对服务器进行显式调用。而服务器也可以是一个用户进程 — 因为两个理由，NFS一般不这样实现。

首先，访问一个NFS文件必须对客户透明。因此，NFS的客户调用是由客户操作系统代表用户进程来完成的。第二，出于效率的考虑， N F S服务器在服务器操作系统中实现。如果NFS服务器是一个用户进程，每个客户请求和服务器应答（包括读和写的数据）将不得不在内核和用户进程之间进行切换，这个代价太大。

一个客户对一个NFS服务器的访问开始于安装协议，返回给客户一个文件句柄。客户接着可以使用那个文件句柄来访问服务器文件系统中的文件。在服务器上，一次检查文件名的一个成员，返回每个成员的一个新的文件句柄。最后的结果就是要引用的文件的一个文件句柄，它可以在随后的读写操作中被使用。

1. 初始时，文件系统协议安装，客户端需知道服务端的文件系统，后续的交互处理都是基于这一文件系统

2. 客户端与服务端之间通过文件句柄来表示所访问文件，客户端每打开一个文件，就会获得服务端的一个文件句柄（可理解成文件描述符ID）

3. 可能触发的远程过程（下面这些并非都会存在）：


> - GETATTR。返回一个文件的属性：文件类型（一般文件，目录等）、访问权限、文件
>   大小、文件的属主者及上次访问时间等信息。
>
> - SETATTR。设置一个文件的属性。只允许设置文件属性的一个子集：
>   访问权限、文件的属主、组的属主、文件大小、上次访问时间和上次修改时间。
>
> - STATFS。返回一个文件系统的状态：可用空间的大小、最佳传送大小等。例如 U n i x的
>   d f命令使用此过程。
>
> - LOOKUP。查找一个文件。每当一个用户进程打开一个 N F S服务器上的一个文件时，
>   N F S客户调用此过程。
>
> - READ。从一个文件中读数据。客户说明文件的句柄、读操作的开始位置和读数据的
>   最大字节数（最多8192个字节）。
>
> - WRITE。对一个文件进行写操作。客户说明文件的句柄、开始位置、写数据的字节数
>   和要写的数据。
>
> - CREATE。创建一个文件。
>
> - REMOVE。删除一个文件。
>
> - RENAME。重命名一个文件。
>
> - LINK。为一个文件构造一个硬链接。硬链接是一个 U n i x的概念，指的是磁盘中的一
>   个文件可以有任意多个目录项（即名字，也叫作硬链接）指向它。
>
> - SYMLINK。为一个文件创建一个符号链接。符号链接是一个包含另一个文件名字的
>   文MKDIR。创建一个目录。
>
>   RMDIR。删除一个目录。
>
>   READDIR。读一个目录。例如 Un ix的l s命令使用此过程
>   比如在windows上用记事本打开共享目录下的某一文本文件，会产生的过程有GETATTR来确认访问权限，LOOKUP确认文件存在，READ返回文件内容（当然实际windows上操作时，GETATTR和LOOKUP在打开共享目录的时候已经完成）件。大多数引用符号链接的操作（例如，打开）实际上引用的是符号链接所指的文件。
>
> - READLINK。读一个符号链接。即返回符号链接所指的文件的名字。
>
> - MKDIR。创建一个目录。
>
> - RMDIR。删除一个目录。
>
> - READDIR。读一个目录。例如 Un ix的l s命令使用此过程
>   比如在windows上用记事本打开共享目录下的某一文本文件，会产生的过程有GETATTR来确认访问权限，LOOKUP确认文件存在，READ返回文件内容（当然实际windows上操作时，GETATTR和LOOKUP在打开共享目录的时候已经完成）
>

## NFS客户端和NFS服务端通讯过程

1. 首先服务器端启动RPC服务，并开启111端口

2. 服务器端启动NFS服务，并向RPC注册端口信息

3. 客户端启动RPC（portmap服务），向服务端的RPC(portmap)服务请求服务端的NFS端口

4. 服务端的RPC(portmap)服务反馈NFS端口信息给客户端

5. 客户端通过获取的NFS端口来建立和服务端的NFS连接并进行数据的传输



# NFS服务的优缺点
优点 ：

1. 节省本地存储空间，将常用的数据存放在一台服务器可以通过网络访问该数据
2. 简单容易上手
3. 方便部署非常快速，维护十分简单

缺点 ：

1. 局限性容易发生单点故障，服务器机宕机的话所有客户端都不能访问
2. 在高并发情况下下NFS效率/性能有限
3. 客户端没用用户认证机制，且数据是通过明文传送，安全性一般（一般建议在局域网内使用）
4. NFS的数据是明文的，对数据完整性不做验证
5. 多台机器挂载NFS服务器时，连接管理维护麻烦

# Ubuntu主机的NFS共享文件夹

## nfs server 安装

```bash
# 安装server
apt-get install nfs-common nfs-kernel-server

# 创建配置文件
vi /etc/exports
sudo gedit /etc/exports
```

配置文件内容
```
# home
/mnt/DATA/CODE/beagle-bone *(rw,sync,no_root_squash,no_subtree_check)
```

启动nfs server
```bash
# 启动
sudo service nfs-kernel-server restart

# 查看共享目录
showmount -e
```
设置启动 nfs server 别名

```bash
alias nfsstart='sudo service nfs-kernel-server restart'
vi ~/.bashrc
source ~/.bashr
```

## nfs client 安装

安装

```bash
# nfs client安装
sudo apt-get install nfs-common

# 查看nfs server上共享的目录
showmount -e <ip address of nfs server>

showmount -e 10.42.0.1
```

创建挂载点

```bash
mkdir /path-to-mount
chmod 777 -R /path-to-mount
mount -t nfs 192.168.xx.xx:/path-to-share /path-to-mount

sudo mount -t nfs -o nolock,rw,rsize=32768,wsize=32768 10.42.0.1:/home/didi/work/Dedge /home/zyh/dedge
```

开机自动挂载：

```bash
# 将 mount -t nfs 192.168.xx.xx:/path-to-share /path-to-mount
mount -t nfs 192.168.xx.xx:/path-to-share /path-to-mount
# 添加到 /etc/rc.local
/etc/rc.local
```

## /etc/exports 参数


```bash
/path-to-share *(rw,sync,no_subtree_check,no_root_squash)
# path-to-share是要共享的目录，根据自己情况修改,*表示允许所有的网段访问，也可以使用具体的IP

/mnt/DATA/CODE/beagle-bone *(rw,sync,no_root_squash,no_subtree_check)
```

| 参数             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| ro               | 只读访问                                                     |
| rw               | 读写访问                                                     |
| sync             | 所有数据在请求时写入共享                                     |
| async            | nfs 在写入数据前可以响应请求                                 |
| secure           | nfs 通过 1024 以下的安全 TCP/IP 端口发送                     |
| insecure         | nfs 通过 1024 以上的端口发送                                 |
| wdelay           | 如果多个用户要写入 nfs 目录，则归组写入（默认）              |
| no_wdelay        | 如果多个用户要写入 nfs 目录，则立即写入，当使用 async 时，无需此设置 |
| hide             | 在 nfs 共享目录中不共享其子目录                              |
| no_hide          | 共享 nfs 目录的子目录                                        |
| subtree_check    | 如果共享 /usr/bin 之类的子目录时，强制 nfs 检查父目录的权限（默认） |
| no_subtree_check | 不检查父目录权限                                             |
| all_squash       | 共享文件的 UID 和 GID 映射匿名用户 anonymous，适合公用目录   |
| no_all_squash    | 保留共享文件的 UID 和 GID（默认）                            |
| root_squash      | root 用户的所有请求映射成如 anonymous 用户一样的权限（默认）客户机用root访问该共享文件夹时，不映射root用户。（root_squash：客户机用root用户访问该共享文件夹时，将root用户映射成匿名用户） |
| no_root_squash   | root 用户具有根目录的完全管理访问权限                        |
| anonuid=xxx      | 指定 nfs 服务器 /etc/passwd 文件中匿名用户的 UID             |
| anongid=xxx      | 指定 nfs 服务器 /etc/passwd 文件中匿名用户的 GID             |



## mount 参数

```bash
Mount -t nfs -o rw,hard,nointr,tcp,noac,vers=3,timeo=600,rsize=32768,wsize=32768 192.168.100.189:/mnt/DG/WORK/2_code/3_T4/aiva /home/firefly/zyh/aiva
```

指定参数具体含义如下：

| 参数             | 说明                                                         |
| :--------------- | :----------------------------------------------------------- |
| rw | 以读写方式安装文件系统（也必须要以相同的方式来导出）|
| ro | 以只读方式安装文件系统 |
| bg | 如果安装失败（服务器没有响应），在后台一直尝试，继续发其它的安装请求 |
| hard | 以硬方式安装文件系统(这是默认情况)。如果服务器当机，让试图访问它的操作被阻塞，直到服务器恢复为止 |
| soft | 以软方式安装文件系统。如果服务器当机，让试图访问它的操作失败，返回一条出错消息。这项功能对于避免进程“挂”在无关紧要的安装操作上来说非常有用 |
| intr | 允许用户中断被阻塞的操作（并且让它们返回一条出错消息） |
| nointr | 不允许用户中断 |
| retrans=n | 指定在以软方式安装的文件系统上，在返回一条出错消息之前重复发出请求的次数 |
| timeo=n | 设置请求的超时时间（以十分之一秒为单位）|
| rsize=n | 设置读缓冲的大小为n字节。对TCP和UDP安装都适用，但最优值不一样（32K较好） |
| wsize=n | 设置写缓冲的大小为n字节。对TCP和UDP安装都适 |
| nfsvers=n | 设置NFS协议的版本 2 或者 3 （在正常情况下是自动的） |
| tcp | 选择通过TCP来传输。默认选择UDP |
| fg | 和bg正好相反，是默认的参数 |
| mountport | 设定mount的端口[@more@] |
