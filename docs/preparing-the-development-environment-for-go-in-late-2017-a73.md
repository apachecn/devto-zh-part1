# 为 2017 年末的 Go 准备开发环境

> 原文：<https://dev.to/hatajoe/preparing-the-development-environment-for-go-in-late-2017-a73>

# 简介

*   gvm
*   导演
*   去
*   戈兰

## gvm

[moovweb/gvm](https://github.com/moovweb/gvm)

据我所知，gvm 是最流行的 Go 版本管理器。能够轻松切换 Go 的版本。

安装:

```
% bash < <(curl -s -S -L https://raw.githubusercontent.com/moovweb/gvm/master/binscripts/gvm-installer)
% vi ~/.zshenv
[[-s ~/.gvm/scripts/gvm]] && . ~/.gvm/scripts/gvm 
```

Enter fullscreen mode Exit fullscreen mode

## direnv

[direnv/direnv](https://github.com/direnv/direnv)

direnv 与 Go 有很好的兼容性，可以随意切换 GOPATH。

安装:

```
% brew install direnv
% vi ~/.zshenv
[[-f /usr/local/bin/direnv]] && \
  eval “$(direnv hook zsh)”
% vi ~/.direnvrc
use_go(){
 . $GVM_ROOT/scripts/gvm-default
 gvm use $1
} 
```

Enter fullscreen mode Exit fullscreen mode

## Go1.9.2

将最新的 Go 作为二进制文件安装。

```
% gvm install go1.9.2 -B 
```

Enter fullscreen mode Exit fullscreen mode

## 戈兰

[GoLand:JetBrains 推出的功能强大且符合人体工程学的产品](https://www.jetbrains.com/go/)

GoLand 是 JetBrains 开发的 IDE。它无需繁琐的设置就能使用。(我也喜欢 vim-go，但我最近在用这个，很简单)

# 入门

```
% mkdir awesome-project
% cd awesome-project
% direnv edit
use go go1.9.2
layout go 
```

Enter fullscreen mode Exit fullscreen mode

准备好了！