---
title: "mac电脑常用配置"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "mac电脑常用配置"
lightgallery: true
tags: [mac]
categories: [tools]
---


- [终端：](#终端)
- [homebrew :](#homebrew-)
- [git](#git)
- [mac快捷键](#mac快捷键)
  - [通用](#通用)
- [Mac系统设置git命令自动补全](#mac系统设置git命令自动补全)
- [mac 使用 zsh](#mac-使用-zsh)
  - [ZSH](#zsh)
  - [oh-my-zsh](#oh-my-zsh)
    - [- ### zsh-syntax-highlighting](#---zsh-syntax-highlighting)



# 终端：

iterm2

# homebrew :
一、brew 安装脚本 （自动选择软件源）
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"

二、brew 卸载脚本
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"

三、常用命令
安装软件：brew install xxx
卸载软件：brew uninstall xxx
搜索软件：brew search xxx
更新软件：brew upgrade xxx
查看列表：brew list
更新brew：brew update
清理所有包的旧版本：brew cleanup
清理指定包的旧版本：brew cleanup $FORMULA
查看可清理的旧版本包，不执行实际操作：brew cleanup -n


# git
git config --global user.name "zhangyuhu"
git config --global user.email "zhangyuhu@didiglobal.com"
ssh-keygen -C 'zhangyuhu@didiglobal.com' -t rsa
cat ~/.ssh/id_rsa.pub



# mac快捷键

## 通用

Command是Mac里最重要的修饰键，在大多数情况下相当于Windows下的Ctrl。
 所以以下最基本操作很好理解：

- `Command + Z` 撤销
- `Command + X` 剪切
- `Command + C` 拷贝（Copy）
- `Command + V` 粘贴
- `Command + A` 全选（All）
- `Command + S` 保存（Save)
- `Command + F` 查找（Find）



# Mac系统设置git命令自动补全

1. brew install bash-completion

2. brew info bash-completion

```bash
DIDI-FVFDH0A8P3Y1:bash_completion.d didi$ brew info bash-completion
bash-completion: stable 1.3 (bottled)
Programmable completion for Bash 3.2
https://salsa.debian.org/debian/bash-completion
Conflicts with:
  bash-completion@2 (because each are different versions of the same formula)
/usr/local/Cellar/bash-completion/1.3_3 (189 files, 608.3KB) *
  Poured from bottle on 2021-09-15 at 00:40:57
From: https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git/Formula/bash-completion.rb
==> Caveats
Add the following line to your ~/.bash_profile:
  [[ -r "/usr/local/etc/profile.d/bash_completion.sh" ]] && . "/usr/local/etc/profile.d/bash_completion.sh"

Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Analytics
install: 8,329 (30 days), 25,293 (90 days), 111,168 (365 days)
install-on-request: 7,394 (30 days), 22,291 (90 days), 98,980 (365 days)
build-error: 0 (30 days)
```

3. git --version

```bash
git version 2.30.1 (Apple Git-130)
```

4.

curl https://raw.githubusercontent.com/git/git/v2.30.1/contrib/completion/git-completion.bash -o  /usr/local/etc/bash_completion.d/git-completion.bash

5. brew unlink bash-completion
6. brew link bash-completion


# mac 使用 zsh

## ZSH

查看安装的 shell

```undefined
cat /etc/shells

/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh
```

使用 brew 更新 zsh

```php
brew install zsh

==> Downloading https://homebrew.bintray.com/bottles/zsh-5.5.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring zsh-5.5.1.high_sierra.bottle.tar.gz
/usr/local/Cellar/zsh/5.5.1: 1,444 files, 12MB
```

切换为 zsh

```undefined
chsh -s /bin/zsh
```

重启终端即可使用 zsh

## oh-my-zsh

下载安装脚本

```csharp
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

配置主题

```
ZSH_THEME="bira"
source ~/.zshrc
```

插件

- 自动补全插件

```shell
mkdir ~/.oh-my-zsh/custom/plugins/incr
cd ~/.oh-my-zsh/custom/plugins/incr
wget -O incr.plugin.zsh http://mimosa-pudica.net/src/incr-0.2.zsh
# wget -O ~/.oh-my-zsh/plugins/incr/incr.plugin.zsh http://mimosa-pudica.net/src/incr-0.2.zsh
```

incr 下载后，还需要在 `~/.zshrc` 中添加：

```shell
plugins=(
  zsh-autosuggestions
  git
  incr
)
```

在 ~/.zshrc 文件末尾添加：

```
source ~/.oh-my-zsh/custom/plugins/incr/incr*.zsh
```

### - ### zsh-syntax-highlighting

[官网](https://github.com/zsh-users/zsh-syntax-highlighting)

**作用** 平常用的`ls`、`cd` 等命令输入正确会绿色高亮显示，输入错误会显示其他的颜色。

**安装**

克隆项目

```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
在 `~/.zshrc` 中配置

```
plugins=(其他的插件 zsh-syntax-highlighting)
```

使配置生效

```
source ~/.zshrc
```



常用插件：

[zsh oh-my-zsh 插件推荐](https://hufangyun.com/2017/zsh-plugin/)

