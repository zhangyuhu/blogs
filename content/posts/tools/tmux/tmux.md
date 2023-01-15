---
title: "tmux的使用"
date: 2023-01-15T17:52:52+08:00
draft: false
description: "tmux的使用"
lightgallery: true
tags: [tmux]
categories: [tools]
---


## tmux 自定义快捷操作
```bash
# 1 新建会话
alias tnew="tmux new -s "
# 2 分离会话(ctrl+b d)
alias tdetach="tmux detach"
# 3 列出会话(ctrl+b s)
alias tlist="tmux ls"
# 4 接入会话
alias tattach="tmux attach -t "
# 5 杀死指定会话
alias tkill="tmux kill-session -t "
# 6 杀死全部会话
alias tkillall="tmux kill-server"
# 7 切换会话
alias tswitch="tmux switch -t "
# 8 重命名会话(ctrl+b $)
alias trename="tmux rename-session -t "
# 9 窗口上下划分窗格
alias tsplitud="tmux split-window"
# 10 窗口左右划分窗格
alias tsplitlr="tmux split-window -h"
# 11 光标到上方窗格
alias tmoveu="tmux select-pane -U"
# 12 光标到下方窗格
alias tmoved="tmux select-pane -D"
# 13 光标到上方窗格
alias tmovel="tmux select-pane -L"
# 14 光标到上方窗格
alias tmover="tmux select-pane -R"
# 15 交换窗格位置(当前窗格上移)
alias tswapu="tmux swap-pane -U"
# 16 交换窗格位置(当前窗格下移)
alias tswapd="tmux swap-pane -D"
```

