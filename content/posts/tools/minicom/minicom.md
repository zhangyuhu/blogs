---
title: "minicom:串口通信工具的使用"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "minicom"
lightgallery: true
tags: [minicom]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [简介](#%E7%AE%80%E4%BB%8B)
- [Minicoms使用](#minicoms%E4%BD%BF%E7%94%A8)
    - [1. 安装](#1-%E5%AE%89%E8%A3%85)
    - [2. 相关配置](#2-%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE)
    - [3. 更多](#3-%E6%9B%B4%E5%A4%9A)
        - [3.1. Ctrl+A](#31-ctrla)
        - [3.2. 自动保存log](#32-%E8%87%AA%E5%8A%A8%E4%BF%9D%E5%AD%98log)
    - [4. 退出软件](#4-%E9%80%80%E5%87%BA%E8%BD%AF%E4%BB%B6)
- [其他问题](#%E5%85%B6%E4%BB%96%E9%97%AE%E9%A2%98)
    - [1. 配置串口权限](#1-%E9%85%8D%E7%BD%AE%E4%B8%B2%E5%8F%A3%E6%9D%83%E9%99%90)
        - [1.1. 方式一：使用命令更改](#11-%E6%96%B9%E5%BC%8F%E4%B8%80%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E6%9B%B4%E6%94%B9)
        - [1.2. 方式二：配置udev规则推荐](#12-%E6%96%B9%E5%BC%8F%E4%BA%8C%E9%85%8D%E7%BD%AEudev%E8%A7%84%E5%88%99%E6%8E%A8%E8%8D%90)
    - [2. 配置默认文件](#2-%E9%85%8D%E7%BD%AE%E9%BB%98%E8%AE%A4%E6%96%87%E4%BB%B6)
- [使用自动脚本](#%E4%BD%BF%E7%94%A8%E8%87%AA%E5%8A%A8%E8%84%9A%E6%9C%AC)

<!-- /TOC -->

# 简介

minicom是一个串口通信工具，就像Windows下的超级终端。可用来与串口设备通信，如调试交换机和Modem等。

# Minicoms使用

## 安装

```bash
sudo apt-get install minicom
```

## 相关配置

使用前需要进行配置，执行

```bash
sudo minicom -s
```

可打开minicom并进入配置模式，使用方向键，选择需要配置的项目，如 **Serial port setup** ，回车进入配置，可以看到多个配置项，此时光标在最下方。

需要修改某个配置，则输入对应的字母，光标即会跳转到对应的项，编辑后，回车确认，光标再次回到最下方。

一般而言，需要修改

```bash
A -    Serial Device # A配置项，指定USB设备。一般USB转串口会生成设备/dev/ttyUSBx，x是数字序号。
E -    Bps/Par/Bits # E配置项，根据实际情况，指定波特率等参数
F -    Hardware Flow Control # F配置项，硬件流控，要看你的设备是否有。如果没有，或者你不确定的话，可以先关掉，将默认的Yes切换为No.
```

修改好之后，回车退到上一个界面，此时记得往下，选择 **Save setup as dfl** 将刚刚的修改保存为默认配置，避免下次使用还需要再次配置。

最后，选择 **Exit** 会退出配置界面，并打开minicom软件才会使用上述参数进行初始化。选择 **Exit from Minicom** 则会直接退出minicom。

## 更多

可以使用 minicom -h 查看，也可在mincon中，按 Ctrl+A 再按 Z 查看。

### Ctrl+A

Ctrl+A 是mimicom的特殊功能前缀按键，但还有另一个很实用的作用，就是暂停屏幕输出。在设备开始大量输出log时，基本看不清屏幕内容。此时可以按 Ctrl+A，暂停输出，方便查看所需log。

Ctrl+A 进入特殊功能：

- 按Z进入设置菜单

- 按 O键：打开配置选项

- 按 W键：自动卷屏。当显示的内容超过一行之後，自动将後面的内容换行。这个功能在查看内核的啓动信息时很有用

- 按 C键：清除屏幕的显示内容

- 按 B键：浏览minicom的历史显示

- 按 X键：退出minicom，会提示确认退出

- 按 N键：可激活时间戳，在每行log前添加当前系统的时间戳

- 按 S键：可向设备端发送文件

- 按 R键：可接收文件


### 自动保存log

让 minicom 自动保存log，可以方便调试。

查看参数，minicom可以使用 -C 参数指定保存log文件。于是完善脚本，自动把log以日期命名，保存到/tmp目录下。tmp目录关机即清空，如果想持久保存log，需要修改到其他目录。

## 退出软件

minicom使用前缀按键 Ctrl-A，即执行特殊操作时，都需要先按 Ctrl+A，再按某个按键使用对应的功能。

Ctrl+A，再按 Z， 可查看帮助，从帮助可以看到，退出时，要先按 Ctrl+A，再按 X



# 其他问题

## 配置串口权限

minicom 本身不需要sudo权限，但因为要打开串口设备/dev/xxx ，所以一般会需要使用sudo来启动minicom。

这里我们可以修改下串口设备的权限，这样以后就不用使用sudo了。

### 方式一：使用命令更改

简单粗暴地使用chmod命令修改

```bash
sudo chmod 666 /dev/ttyUSB0
```

### 方式二：配置udev规则(推荐)

修改配置文件

```bash
sudo vim /etc/udev/rules.d/70-ttyusb.rules
```

增加一行

```bash
KERNEL=="ttyUSB[0-9]*", MODE="0666"
```

修改后，需要重新插拔设备，以重新生成设备节点。



## 配置默认文件

在/etc/minicom/目录下是minicom的配置文件

增加配置文件如下：

```bash
sudo vi  /etc/minicom/minirc.ttyUSB0
```

```bash
pr port             /dev/ttyUSB0
pu baudrate         115200
pu bits             8
pu parity           N
pu stopbits         1
pu minit
pu mreset
pu rtscts           No
```

 执行

```bash
minicom ttyUSB0
```

加载默认配置文件



# 使用自动脚本

参考：[EasierMinicom](https://github.com/zqb-all/EasierMinicom)

```bash
com() {
	local ports_USB ports_ACM ports datename dev devs dev_count
	ports_USB=$(ls /dev/ttyUSB* 2>null | xargs -I {} basename {})
	ports_ACM=$(ls /dev/ttyACM* 2>null | xargs -I {} basename {})
	ports="$ports_USB $ports_ACM"

	#check lock
	devs=""
	dev_count=0
	for dev in ${ports}; do
		! ls /run/lock/*"${dev}"* &>/dev/null && {
			devs+="${dev} "
			((dev_count++))
		}
	done
	[ -z "$devs" ] && echo "No Unlock Devices" && return 0

	datename=$(date +%Y%m%d-%H%M%S)
	if [ $dev_count -eq 1 ]; then
		dev=$devs
	else
		#select dev to open
		echo "Please select one device: (Ctrl+C to abort)"
		select dev in $devs; do
			if [ "$dev" ]; then
				echo "You select the '$dev'"
				break
			else
				echo "Invaild selection"
			fi
		done
	fi

	out="/tmp/$(basename ${dev}).$datename.log"
	keep_dir="${HOME}/minicom_keep"

	minicom $dev -C "${out}" "$@"

	[ -f "${out}" ] && {
		echo log : "${out}"
		read -p "Keep it? [y|N]: " keep

		[ "${keep}" = 'Y' -o "${keep}" = 'y' ] && {
			read -p "Enter file name > " keep_file_name

			[ x"$keep_file_name" = x"" ] && keep_file_name=$(basename "${out}")

			mkdir -p "$keep_dir"
			cp "${out}" "${keep_dir}/$keep_file_name"
			echo "saved in $keep_dir/$keep_file_name"
		}
	}
	read -p "Vim it? [y|N]: " edit_vim

	[ "${edit_vim}" = 'Y' -o "${edit_vim}" = 'y' ] && vim "${out}"
}
```



- 自动检测未锁定的设备，如果有多个则列出给用户选择，如果只有一个则直接使用。
- 默人加载 /etc/minicom/minirc.${dev} 的配置文件

-  自动保存log到/tmp目录，退出时，询问是否要保存到另外的目录。



**使用**

```bash
echo "source $(pwd)/EasierMinicom.sh" >> ~/.bashrc
source ~/.bashrc
com
```