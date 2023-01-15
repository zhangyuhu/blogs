---
title: "git:tag的使用"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "git tag"
lightgallery: true
tags: [git]
categories: [tools]
---

> 本文采用[知识共享署名 4.0 国际许可协议](http://creativecommons.org/licenses/by/4.0/)进行许可，转载时请注明原文链接，图片在使用时请保留全部内容，可适当缩放并在引用处附上图片所在的文章链接。

<!-- TOC -->

- [git tag](#git-tag)

<!-- /TOC -->

## git tag

```bash
git tag # 列出已有的标签
git tag -l "v0.1" # 过滤列出已有的标签

git tag -a v1.4 -m "my version 1.4" # 创建标签
git show v1.4 # 查看标签信息和与之对应的提交信息

git tag -d v1.4 # 删除标签
git push origin :refs/tags/v1.4 # 删除远程标签
git push origin --delete v1.4 # 删除远程标签

git checkout v1.4 # 切换到 v1.4 tag
git checkout -b version_v1.4 v1.4 # 切换到 v1.4 tag version_v1.4

git push origin v1.4 # 传送标签到远程仓库服务器
git push origin --tags # 把所有不在远程仓库服务器上的标签全部传送到远程仓库服务器

```
**后期打标签**
```bash
git log --pretty=oneline

dd1db2ddf7ab23a57b0197abe80167ba4847f185 (HEAD -> master, tag: v0.0.2, origin/master, origin/HEAD) Merge branch 'release-0.0.2'
1967f19f3d2f7b13d18f80dada14da1b8385ecc3 (release-0.0.2) version update :v0.0.2
28b3ff52ea28221314264482d6db6fb7349cf2f8 (develop) Merge branch 'release-0.0.1' into develop
e2d4b5d8eb8407d7141c9846c4459cd1733922fb (release-0.0.1) version update :v0.0.1
2c6ffbce9ffd31e4cee8ca5013924324b49ec898 add bump-version.sh
9c29190a5388ae74f6c983468c8637de40653816 (origin/develop) Merge remote-tracking branch 'origin/hotfix' into develop
5a0a3d928c59a35e3421d2dc029b0487e24a2772 add HotFix/01.fix
78881870f888c519f21025d22869eb5d38b75030 Merge remote-tracking branch 'origin/myfeature_02' into develop
1c9e156e9b54c6714a6f6e8075b8f6cf316ca7c5 Merge remote-tracking branch 'origin/myfeature_01' into develop
b2dc34bc80ce6c9a77a9c399abc7633d09bf9359 Merge branch 'myfeature' into develop
696a92f6e0f77700c1aef37a57abcbc5d69a6673 add TestFile/4.text
5f57182ab7113fdadfa855e4c78237e1a29292b8 Merge branch 'myfeature' into develop
18dd8ba9bb27dc1ff1e414c8538转载说明beb9e904c9032 TestFile/3.test
2124458eef016c34a4d0a0af68dc93166e93ba84 TestFile/2.test
754b56d1c44e49923d679c41839e84e0dfb650d6 add TestFile/1.test
3a8af3c6e7d2852d24b6de6711530cc0ddcc8760 Merge branch 'myfeature' into develop
963672575df191249da5c7f1aaef56afa9cccf2e (origin/feature, feature) add new file
395f4fbd335500cf7c5b51735a27231bea028cd8 (origin/release) Initial commit

git tag -a v0.0.1 5a0a3d928c59a35e3421d2dc029b0487e24a2772

git tag -a v0.0.0 -m "version 0.0.0" 395f4fbd335500cf7c5b51735a27231bea028cd8
```





