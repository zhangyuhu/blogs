---
title: "git:常见问题"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "git"
lightgallery: true
tags: [git]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。


<!-- TOC -->

- [git 缓存清理（解决ignore不生效问题）](#git-缓存清理解决ignore不生效问题)
- [.git 文件夹过大](#git-文件夹过大)
  - [出现原因](#出现原因)
  - [删除历史操作](#删除历史操作)
  - [脚本操作](#脚本操作)

<!-- /TOC -->

# git 缓存清理（解决ignore不生效问题）

明明.ignore文件中标明了忽略，提交时还是有这些文件？

.ignore文件可以按照其注明的规则让git忽略文件，但是有时候，有部分我们不想让git管理的文件已经被git管理了，我们再在.ingore中添加规则，是不能生效的，这时我们清理下git缓存就好

删除git 缓存

````bash
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
````


下次提交我们就不会看到.ignore中的文件了


# .git 文件夹过大

## 出现原因

.git文件主要用来记录每次提交的变动，当我们的项目越来越大的时候， .git文件越来越大。

很大的可能是因为提交了大文件，如果你提交了大文件 A，那么即使你在之后的版本中将其删除，但实际上，记录中的大文件仍然存在。

原因在于虽然你在后面的版本中删除了大文件A，但是Git是有版本倒退功能，那么如果大文件A不记录下来，git拿什么来回退呢？

git给出了解决方案，**使用git branch-filter来遍历git history tree, 可以永久删除history中的大文件，达到让.git文件瘦身的目的**。

## 删除历史操作

- 首先找出git中前N大的文件

```bash
git rev-list --objects --all | grep -E `git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -10 | awk '{print$1}' | sed ':a;N;$!ba;s/\n/|/g'`
```

- 删除文件

遍历所有提交： commit多了会比较慢

```bash
git filter-branch -f --prune-empty --index-filter 'git rm -rf --cached --ignore-unmatch <file>' --tag-name-filter cat -- --all
```

- 回收内存

```bash
rm -Rf .git/refs/original

rm -Rf .git/logs/

git reflog expire --expire=now --all

git fsck --full --unreachable

git repack -A -d

git gc --aggressive --prune=now
```

- 提交到远程仓库

```bash
git push --force [remote] master
```

上述操作最重要的两条命令是 **git filter-branch** 和 **gc**, filter-branch 真正在清理，但是只运行它也是没用的，需要再删除备份的文件，重新打包之类的，最后的gc命令，用来收集产生的垃圾，最终清除大文件。

## 脚本操作

编辑脚本文件： git_clean.sh

脚本文件下载：[git_clean.sh](https://gitee.com/fromyuhu/COMMON/tree/master/Tools/GitClean)

```bash
git rev-list --objects --all | grep -E `git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -10 | awk '{print$1}' | sed ':a;N;$!ba;s/\n/|/g'` >1.txt

for value in `cat 1.txt |awk '{print $2}'`
do
    # echo ${value}
    git log --pretty=oneline --branches -- ${value}
    git filter-branch --index-filter "git rm --cached --ignore-unmatch ${value}" -- --all

    rm -Rf .git/refs/original
    rm -Rf .git/logs/
    git reflog expire --expire=now --all
    git fsck --full --unreachable
    git repack -A -d
    git gc --aggressive --prune=now
done
```

将脚本文件放置到仓库根目录（与.git 同级）
执行
```bash
./git_clean.sh
```

更新远程仓库

```bash
git push --force [remote] master
```