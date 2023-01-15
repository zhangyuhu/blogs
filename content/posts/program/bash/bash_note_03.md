---
title: "bash 编程笔记 (三)"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "bash 编程笔记"
lightgallery: true
tags: [bash]
categories: [bash]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [实例一：备份/更新文件的脚本](#实例一备份更新文件的脚本)

<!-- /TOC -->
# 实例一：备份/更新文件的脚本

编写一个脚本文件backup.sh，备份android、kernel或uboot中的文件，备份的时候要保留文件在备份文件夹(即保留文件的相对路径)。
(01) 新建list_file.txt，并在list_file.txt中记录要备份的文件的路径。
(02) 当执行“./backup.sh bb”命令时：backup.sh会新建bb目录，然后读取list_file.txt中记录的文件路径，并将记录的文件路径对应的文件备份到bb目录下。
(03) 当执行“./backup.sh tt”命令时，backup.sh会判断是否存在tt目录，若存在tt目录的话，则tt中的文件更新到list_file.txt所记录的文件路径；否则的话，不更新文件。

脚本内容如下：

    #!/bin/bash

    ##############################NOTE############################
    #
    # AUTHOR      : skyWang
    # DATE        : 2012-07-16
    # DESCRIPTION : 用于备份文件 or 更新文件
    #
    #               1. 备份文件
    #               (1) 备份命令        :  ./run.sh bb
    #               (2) 备份文件所在目录:  bb
    #                   这个是在步骤(1)之后，在当前目录下自动建立bb目录
    #
    #               2. 更新文件
    #               (1) 更新命令        :  ./run.sh tt
    #               (2) 更新文件所在目录:  tt
    #                   这个是必须在步骤(1)之前已经存在的目录。因为，就是为了用tt目录中的文件去替换到目标文件。
    #
    # OTHER       : 1. list_file.txt 文件说明(即，list_file.txt中的文件必须满足一下条件）：
    #               “变量COMMPATH_PATH的值”和“list_file.txt中的每一行内容”组合起来得到的完整路径所对应的文件，必须存在！
    #
    #               例如： COMMON_PATH=/root/
    #                      list_file.txt中有以下1行内容：
    #                      android/build/core/Makefile
    #               这样，“变量COMMPATH_PATH的值”和“list_file.txt中的行”组合起来得到的完整路径就是：
    #                      /root/android/build/core/Makefile
    #
    #                “/root/android/build/core/Makefile”这个文件必须存在于电脑中。
    #
    #               （1）若list_file.txt满足以上条件，运行“./run.sh bb”就可以备份“/root/android/build/core/Makefile”。
    #                    备份时，会自动(在当前目录下)建立目录“bb”，并将文件备份到“bb”目录下
    #               （2）若将bb目录重命名成tt，运行“./run.sh tt”就可以将“tt”中的文件更新到“/root/android/build/core/Makefile”。
    #
    #
    ##############################NOTE############################


    #########TODO...START#########
    # 假设android、kernel、uboot在同一级目录；
    # 则，COMMON_PATH是它们所在目录的路径。
    COMMON_PATH=/home/skywang/a8/gingerbread/code/
    #########TODO...END###########


    #########CONSTANT...START#########
    LIST_FILE="list_file.txt"
    B_FOLDER="bb/"
    T_FOLDER="tt/"
    # android工程的文件夹名字
    ANDROID_FOLDER="android"
    # kernel工程的文件夹名字
    KERNEL_FOLDER="kernel"
    # uboot工程的文件夹名字
    UBOOT_FOLDER="uboot"
    # android长度
    ANDROID_FOLDER_LEN=`expr length "$ANDROID_FOLDER"`
    # kernel长度
    KERNEL_FOLDER_LEN=`expr length "$KERNEL_FOLDER"`
    # uboot长度
    UBOOT_FOLDER_LEN=`expr length "$UBOOT_FOLDER"`
    #########CONSTANT...END###########


    # 功能说明：根据源文件和目标文件所在完整路径，将源文件更新到目标文件
    #           更新成功的话，输出更新的起止路径；否则，打印错误信息
    # 输入参数：src_full_path    —— 源文件所在完整路径。
    #           dst_full_path    —— 目标文件所在完整路径。
    #           例如：src_full_path="tt/android/hardware/libsensor/sensors.cpp"
    #                 dst_full_path="/home/skywang/mnt/sda6/a8/a72/3g/iceCreamSandwish/code/android/hardware/libsensor/sensors.cpp"
    function func_update_file_by_path()
    {
        local src_full_path=$1
        local dst_full_path=$2
        local dst_full_dir=`dirname $dst_full_path`

        if [ -f "$src_full_path" ];then
            if [ -e "$dst_full_path" ] || [ -d "$dst_full_dir" ];then
                cp -v $src_full_path $dst_full_path
            else
                mkdir -p $dst_full_dir
                cp -v $src_full_path $dst_full_path
            fi
        else
            echo copy file ERROR:
            echo file $src_full_path not existes!!!
        fi
    }


    # 功能说明：将“输入文件”中全部文件全部更新到代码中
    #           更新成功的话，输出更新的起止路径；否则，打印错误信息
    # 输入参数：input_file    ——    “输入文件”所在的完整路径
    #           例如：input_file="/home/skywang/mnt/sda6/a8/a72/3g/iceCreamSandwish/backup/exmaple/list_file.txt"
    function func_push_files()
    {
        local input_file=$1
        local full_path
        local backup_path
        local count=0

        while read -r line
        do
            let count=`expr $count + 1`
            if [ `expr match $line $ANDROID_FOLDER` == "$ANDROID_FOLDER_LEN" ] ||
               [ `expr match $line $KERNEL_FOLDER` == "$KERNEL_FOLDER_LEN" ] ||
               [ `expr match $line $UBOOT_FOLDER` == "$UBOOT_FOLDER_LEN" ];then
                echo "$count : right push path"
                src_path=${T_FOLDER}${line}
                dst_path=${COMMON_PATH}${line}
                func_update_file_by_path $src_path $dst_path
            else
                echo "$count : push file $line is invalidate!"
            fi
        done < $input_file

    }


    # 功能说明：根据源文件所在完整路径，将源文件拷贝到另一个目录下
    #           备份成功的话，输出备份的起止路径；否则，打印错误信息
    # 输入参数：src_full_path    —— 源文件所在完整路径。
    #           dst_full_dir    —— 目标路径
    #           例如：src_full_path="/home/skywang/mnt/sda6/a8/a72/3g/iceCreamSandwish/code/android/hardware/libsensor/sensors.cpp"
    #                 dst_full_dir="bb/android/hardware/libsensor/"
    function func_backup_file_by_path()
    {
        local src_full_path=$1
        local dst_full_dir=$2"/"

        if [ -f "$src_full_path" ];then
            if [ -d "$dst_full_dir" ];then
                cp -v $src_full_path $dst_full_dir
            else
                mkdir -p $dst_full_dir
                cp -v $src_full_path $dst_full_dir
            fi
        else
            echo backup file ERROR:
            echo file $src_full_path not existes!!!
        fi
    }

    # 功能说明：将“输入文件”中全部文件全部做备份。
    #           备份成功的话，输出备份的起止路径；否则，打印错误信息
    # 输入参数：input_file    ——    “输入文件”所在的完整路径
    #           例如：input_file="/home/skywang/mnt/sda6/a8/a72/3g/iceCreamSandwish/backup/exmaple/list_file.txt"
    function func_pull_files()
    {
        local input_file=$1
        local full_path
        local backup_path
        local count=0

        while read -r line
        do
            let count=`expr $count + 1`
            if [ `expr match $line $ANDROID_FOLDER` == "$ANDROID_FOLDER_LEN" ] ||
               [ `expr match $line $KERNEL_FOLDER` == "$KERNEL_FOLDER_LEN" ] ||
               [ `expr match $line $UBOOT_FOLDER` == "$UBOOT_FOLDER_LEN" ];then
                echo "$count : right pull path"
                full_path=${COMMON_PATH}${line}
                backup_path=${B_FOLDER}`dirname $line`
                func_backup_file_by_path $full_path $backup_path
            else
                echo "$count : pull file $line is invalidate!"
            fi
        done < $input_file

    }

    func_copy_all_files()
    {
        if [ ! -e "$LIST_FILE" ];then
            echo ERROR: the file $LIST_FILE not existes!!!
            exit 1
        fi


        case $1 in
            BB|bb)
                echo -e "\n\n==============backup files=============="
                func_pull_files $LIST_FILE && exit 0
                ;;
            TT|tt)
                echo -e "\n\n==============syn    files=============="
                func_push_files $LIST_FILE && exit 0
                ;;
            *)
                echo ERROR parameter: please input parameter \"bb\" to backup, or \"tt\" to update!!!
                ;;

        esac
    }

    func_copy_all_files $1

    exit 0

