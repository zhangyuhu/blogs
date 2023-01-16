---
title: "开源软件:ffmpeg & opencv_4.5.0  安装"
date: 2023-01-16T17:52:52+08:00
draft: false
description: "开源软件"
lightgallery: true
tags: [ffmpeg,opencv]
categories: [开源软件]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [1. 编译的原因](#1-编译的原因)
- [2. 环境](#2-环境)
- [3. ffmpeg](#3-ffmpeg)
  - [3.1. 下载编译](#31-下载编译)
  - [3.2. cmake支持](#32-cmake支持)
- [4. opencv](#4-opencv)
  - [4.1. toolchain](#41-toolchain)
  - [4.2. 下载编译](#42-下载编译)
- [5. References](#5-references)

<!-- /TOC -->

# 1. 编译的原因

想在目标平台 `gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf` 上读取视频，作为算法 SDK 的输入数据；交叉编译了带 `videoio` 模块的 `OpenCV`，遇到 `VideoCapture` 无法读取 `.mp4` 格式视频的问题。排查后发现：

1. 尽管不指定 FFMPEG，VideoIO 模块也能编译出来，但对应的编解码器 FOURCC 只能编解码 `.avi` 格式的视频，无法满足实际需求。

2. `videoio` 模块可以有不同 backend，在 PC 上先前主要是 Windows 平台，使用了预编译的 ffmpeg dll，没有对编解码 backend 引起重视；而 `aarch64-none-linux-gnu` 这样的嵌入式平台，没找到现成的 `FFMPEG` 库（如果有，请留言告知），于是手动编译 `FFMPEG`，并在 OpenCV 的 cmake 构建中集成。

# 2. 环境

- Host: Ubuntu 18.04
- 交叉编译工具: rv1126 工具链 gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf
- cmake 3.20

# 3. ffmpeg

## 3.1. 下载编译

```bash
#!/bin/bash

OUT_DIR=../out_ffmpeg_rv1126
CROSS_PREFIX=/opt/tools/rk_docs_and_compiler/compiler/gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-
FFMPENG_BRANCH=n4.2.2
MAKE_JOBE=40

#download
git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
cd ffmpeg
git checkout -b ${FFMPENG_BRANCH}

./configure \
    --prefix=${OUT_DIR} \
    --shlibdir=${OUT_DIR}/lib \
    --disable-shared \
    --disable-doc \
    --arch=aarch64 \
    --cross-prefix=${CROSS_PREFIX} \
    --target-os=linux \
    --disable-x86asm \
    --disable-asm \
    --disable-ffmpeg \
    --disable-ffplay \
    --disable-ffprobe \
    --enable-avresample

make -j${MAKE_JOBE}
make install
```



## 3.2. cmake支持

ffmpeg 官方不提供 cmake 构建支持，github 上找到的 ffmpeg cmake 写得很长但不起作用；简单起见，用如下 Python 代码生成 `ffmpeg-config.cmake`（注意库版本为手动硬编码，库的顺序也很重要）

`gen-ffmpeg-cmake-config.py`：

```python
#!/usr/bin/env python
#coding: utf-8

"""
Generate ffmpeg-config.cmake
"""

# note: the order matters
# ref: https://blog.csdn.net/zijin0802034/article/details/52779791
ffmpeg_lib_desc = {
    'libavformat': '58.71.100',
    'libavdevice': '58.12.100',
    'libavcodec': '58.129.100',
    'libavutil': '56.67.100',
    'libswscale': '5.8.100',
    'libswresample': '3.8.100',
    'libavfilter': '7.109.100',
    'libavresample': '4.0.0',
}

content_lines = [
    'set(ffmpeg_path "${CMAKE_CURRENT_LIST_DIR}")',
    '',
    'set(FFMPEG_EXEC_DIR "${ffmpeg_path}/bin")',
    'set(FFMPEG_LIBDIR "${ffmpeg_path}/lib")',
    'set(FFMPEG_INCLUDE_DIRS "${ffmpeg_path}/include")',
    '',
]


# setup library names
content_lines.append('# library names')
content_lines.append(
    'set(FFMPEG_LIBRARIES',
)

for key, value in ffmpeg_lib_desc.items():
    lib_name = key
    lib_version = value
    content_lines.append(
        '    ${FFMPEG_LIBDIR}/' + lib_name + '.a'
    )
content_lines.append('    pthread')
content_lines.append(')')
content_lines.append('')


# setup library found
content_lines.append('# found status')
for key, value in ffmpeg_lib_desc.items():
    lib_name = key
    content_lines.append(
        'set(FFMPEG_{:s}_FOUND TRUE)'.format(lib_name)
    )
content_lines.append('')

# setup library versions
content_lines.append('# library versions')
for key, value in ffmpeg_lib_desc.items():
    lib_name = key
    lib_version = value
    content_lines.append(
        'set(FFMPEG_{:s}_VERSION {:s})'.format(lib_name, lib_version)
    )
content_lines.append('')

# final stuffs
content_lines.append('set(FFMPEG_FOUND TRUE)')
content_lines.append('set(FFMPEG_LIBS ${FFMPEG_LIBRARIES})')

# summary up and write
fout = open('ffmpeg-config.cmake', 'w')
for line in content_lines:
    fout.write(line + '\n')
fout.close()
```

其中版本号的设定，是为了在 OpenCV 中找到 FFMPEG 后，能通过最低版本检查。

将这个文件，放在 ffmpeg 安装路径的根目录，并运行，得到 `ffmpeg-config.cmake`，则后续可用 `find_package(FFMPEG)` 使用；

# 4. opencv


## 4.1. toolchain

交叉编译阶段用到的 toolchain 文件里，如果后续用的是 ffmpeg 动态库，则需要在 toolchain 里指定 rpath。本人最终用的静态库，因而未设 rpath。

如下脚本，保存为 `build/aarch64-none-linux-gnu.toolchain.cmake`
```cmake
# message("begin child cmake")
# set cross-compiled system type, it's better not use the type which cmake cannot recognized.
set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR ARM)

# when gcc-arm-aarch64-none-linux-gnu was installed, toolchain was available as below:
set(TOOLCHAIN_DIR "/opt/tools/rk_docs_and_compiler/compiler/gcc-arm-8.3-2019.03-x86_64-arm-linux-gnueabihf/bin")
set(CMAKE_CXX_COMPILER ${TOOLCHAIN_DIR}/arm-linux-gnueabihf-g++)
set(CMAKE_C_COMPILER ${TOOLCHAIN_DIR}/arm-linux-gnueabihf-gcc)

# set searching rules for cross-compiler
set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)

# other needed options
#set(GNUEABIHF_C_FLAGS "-Wall -fPIC -mcpu=cortex-a9 -mfpu=neon-fp16 -mfloat-abi=hard -mthumb-interwork -marm" CACHE INTERNAL docstring)
#set(GNUEABIHF_C_FLAGS "-Wall -fPIC -Wl,-rpath-link=/home/zz/soft/ffmpeg/aarch64-none-linux-gnu/lib" CACHE INTERNAL docstring) # for shared libs, rpath required
set(GNUEABIHF_C_FLAGS "-Wall -fPIC" CACHE INTERNAL docstring)
set(GNUEABIHF_CXX_FLAGS "")

set(CMAKE_C_FLAGS "-march=armv7-a ${CMAKE_C_FLAGS} ${GNUEABIHF_C_FLAGS}" CACHE STRING "C flags" FORCE)
set(CMAKE_CXX_FLAGS "-march=armv7-a ${CMAKE_CXX_FLAGS} ${GNUEABIHF_C_FLAGS} ${NDK_CXX_FLAGS}" CACHE STRING "C++ flags" FORCE)
```

https://stackoverflow.com/questions/12427928/configure-and-build-opencv-to-custom-ffmpeg-install

## 4.2. 下载编译

用如下脚本来编译，保存为 `build/aarch64-linux-gnu.sh` 并执行

```bash
#!/bin/bash

OUT_DIR=../out_opencv_rv1126
OPENCV_MODULES_DIR=../opencv_contrib/modules/
FFMPEG_DIR=../out_ffmpeg_rv1126

OPENCV_BRANCH=4.5.0
MAKE_JOBE=40

#download
git clone --branch ${OPENCV_BRANCH} https://github.com/opencv/opencv.git
git clone --branch ${OPENCV_BRANCH} https://github.com/opencv/opencv_contrib.git

#编译
mkdir build_opencv
cd build_opencv

TOOLCHAIN=../cross.cmake

cmake -DCMAKE_TOOLCHAIN_FILE=$TOOLCHAIN \
    -D CMAKE_BUILD_TYPE=Release \
    -D CMAKE_INSTALL_PREFIX=${OUT_DIR} \
    -D OPENCV_EXTRA_MODULES_PATH=${OPENCV_MODULES_DIR} \
    -D WITH_FFMPEG=ON \
    -D OPENCV_FFMPEG_USE_FIND_PACKAGE=ON \
    -D OPENCV_FFMPEG_SKIP_BUILD_CHECK=ON \
    -D FFMPEG_DIR=${FFMPEG_DIR} \
    -D BUILD_TESTS=OFF \
    -D BUILD_PERF_TESTS=OFF \
    -D BUILD_opencv_apps=OFF \
    -D WITH_PROTOBUF=OFF \
    -D WITH_QUIRC=OFF \
    -D WITH_1394=OFF \
    -D WITH_CUDA=OFF \
    -D WITH_OPENCL=OFF \
    -D WITH_VTK=OFF \
    -D WITH_MATLAB=OFF \
    -D BUILD_DOCS=OFF \
    -D BUILD_opencv_python3=OFF \
    -D BUILD_opencv_python2=OFF \
    -D WITH_IPP=OFF \
    -D BUILD_ANDROID_PROJECTS=OFF \
    -D BUILD_ANDROID_EXAMPLES=OFF \
    -D BUILD_ANDROID_SERVICE=OFF \
    -D BUILD_SHARED_LIBS=OFF \
    -D OPENCV_FORCE_3RDPARTY_BUILD=ON \
    -D BUILD_JAVA=OFF \
    -D BUILD_FAT_JAVA_LIB=OFF \
    -D OPENCV_GENERATE_PKGCONFIG=ON \
    ../opencv

make -j${MAKE_JOBE}
make install
```



# 5. References

[ OpenCV 交叉编译：基于 FFMPEG 的 VideoIO 模块](https://gitee.com/AILearnRelated/cv-dbg/blob/master/ffmpeg-videoio-aarch64-none-linux-gnu/OpenCV-video-ffmpeg.md#)
