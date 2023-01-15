---
title: "git:修改提交"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "git"
lightgallery: true
tags: [git]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [1. Git提交代码的流程](#1-git%E6%8F%90%E4%BA%A4%E4%BB%A3%E7%A0%81%E7%9A%84%E6%B5%81%E7%A8%8B)
- [2. git commit --amend](#2-git-commit---amend)
- [3. git 查看](#3-git-%E6%9F%A5%E7%9C%8B)
- [4. git 修改某次提交](#4-git-%E4%BF%AE%E6%94%B9%E6%9F%90%E6%AC%A1%E6%8F%90%E4%BA%A4)
- [5. 修改提交时间](#5-%E4%BF%AE%E6%94%B9%E6%8F%90%E4%BA%A4%E6%97%B6%E9%97%B4)
- [6. commit 撤回及修改](#6-commit-%E6%92%A4%E5%9B%9E%E5%8F%8A%E4%BF%AE%E6%94%B9)

<!-- /TOC -->

[重写历史](https://git-scm.com/book/zh/v2/Git-%E5%B7%A5%E5%85%B7-%E9%87%8D%E5%86%99%E5%8E%86%E5%8F%B2)

## Git提交代码的流程
提交流程
``` bash
git pull --rebase
git add .
git commit -m""
git push origin xxx
```

```bash
git push origin --delete xx # 删除远程分支
git push origin xxx --force # 强制推送到远程分支
git push origin dev-zyh  # 推送到指定分支
```
## git commit --amend
```
git commit --amend  # 会通过 core.editor 指定的编辑器进行编辑
git commit --amend --no-edit   # 不会进入编辑器，直接进行提交
git commit --amend --no-edit --author "zhangyuhu <zhangyuhude@163.com>"
```

## git 查看
```bash
git log -1 --stat
git show 548c72f42f51c22dbf2fdf133426e094c59789e4 --stat
git show //最新的commit
git show commitId //指定commit
git show commitId fileName //指定commit的某个文件

# 查看指定commit id对应修改文件列表
git show --raw
git show --raw commit_id
```

## git 修改某次提交

```
commit f2711319a72fa9d3a5486c397c7a10511b970036 (HEAD -> master)
Author: zhangyuhu <zhangyuhude@163.com>
Date:   Mon Aug 24 13:08:12 2020 +0800

    update shll sys folder

commit 1caaf17d90a3288aff8897fca08e33c81a6b9703
Author: yuhuzhang <yuhuzhang@deepglint.com>
Date:   Mon Aug 24 12:56:55 2020 +0800

    add RunPowerOn.sh

commit 8ac823f3e93e82638db5b076952bdcfa12e93cfb
Author: zhangyuhu <zhangyuhude@163.com>
Date:   Fri Aug 21 19:44:06 2020 +0800

    add go test file
```
```bash
git rebase -i 8ac823f3e93e82638db5b076952bdcfa12e93cfb

edit b8901f3 add RunPowerOn.sh
pick 5593c97 update shll sys folder

git cazyh

git rebase --continue
```

```bash
1.git rebase -i 分支~移动数字
2. 选择对应操作
3.git commit --amend
4.git rebase continue
```

## 修改提交时间

```bash
git commit --amend --date="2020-11-28T09:51:07" --no-edit
```

## commit 撤回及修改

```bash
git reset --soft  :取消commit
git reset --mixed :取消commit ，取消add
git reset --hard  :取消commit ，取消add，取消源文件修改
```



