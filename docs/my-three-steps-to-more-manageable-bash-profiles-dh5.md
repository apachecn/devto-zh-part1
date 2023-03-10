# 我的实现更易管理的 bash 概要文件的三个步骤

> 原文：<https://dev.to/nickp60/my-three-steps-to-more-manageable-bash-profiles-dh5>

## 来源

[伟大的博文载入顺序](https://shreevatsa.wordpress.com/2008/03/30/zshbash-startup-files-loading-order-bashrc-zshrc-etc/)
[一个颇有见地的 SO 问题](https://stackoverflow.com/questions/415403/whats-the-difference-between-bashrc-bash-profile-and-environment)

## 问题

我在一个名为 BioLinux 的 Linux 发行版上工作，它是建立在 Ubuntu 14 之上的。这是 zsh 作为默认 shell 提供的。当我从一名实验室生物学家过渡到计算机世界时，我开始有很多问题。一个反复出现的问题是如何设置$PATH 变量。每次我试图安装一个新程序时，我都面临着一个决定:我是要改变。bashrc，。bash_profile，。zshrc 或者。简介？我结束了意大利面条代码调用所有这些在一些顺序。每一个都是变量、别名、自定义函数等等的混合。

当我使用 ssh 登录这台计算机，发现$PATH 变量不同或丢失时，事情变得更加复杂。

后来，我开始越来越多地在远程机器上工作，并希望我的设置在不同的 shells 上保持一致。这些机器中有些既有 zsh 又有 bash，有些只有 bash。

过去的这个周末，我整理了我的混乱，并想分享我所看到的可管理的解决方案(目前！).

## 修复#1:坚持一个外壳

我的困惑有一半是因为在我的主机器上使用 zsh，在我经常使用的三台远程机器上使用 bash。我在 zsh 上从来没有任何问题，但是在谷歌上搜索了一下之后，我发现我没有利用 zsh 的所有特性。所以我决定换回 good 'ol bash，原因有两个:
1)在我使用的所有机器上保持相同。
2)简化。我显然没有使用 zsh，因为它可以被使用，所以我决定转移到 bash，直到我觉得我需要 zsh 提供的额外功能。

```
chsh -s /bin/bash myuser 
```

Enter fullscreen mode Exit fullscreen mode

## 修复#2:在交互式和非交互式外壳之间保持相同

[https://super user . com/questions/183870/difference-between-bashrc-and-bash-profile](https://superuser.com/questions/183870/difference-between-bashrc-and-bash-profile)

我还是不明白这件事的来龙去脉，但是。当你打开一个新的 shell 时，bashrc 不会被检查。bash_profile 是。但是由于我希望我的设置既可以在登录 shells 中调用，也可以在 ssh 会话中调用，所以我通过更改我的。bash_profile 改为下面 3 行:

```
if [ -f ~/.bashrc ]; then source ~/.bashrc
fi 
```

Enter fullscreen mode Exit fullscreen mode

所以，当我打开一个新的壳。bash_profile 被调用，然后它加载我在。bashrc 这样，无论我什么时候登录或者我在做什么，我都会得到相同的行为。

## 解决方案 3:将机器特有的东西与便利的东西分开

我喜欢在我的 shell 中使用漂亮的颜色，自定义别名，在我的提示符中显示 git 状态，以及其他有趣的东西。我希望我的每台机器都有相同的别名、功能和颜色设置。但我不能照搬我的。bashrc 到不同的机器上，因为$ PATHs 会不同。

我的解决方案是通过我的。然后，我在我的。bashrc

```
source ~/.nickstuff 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我接触到一台新机器时，我可以移动它。nickstuff 文件添加到我新机器的主目录中，在新机器中添加相同的行。bashrc，我可以访问所有使我的生活更轻松的功能。

对于那些好奇的人，这是我在我的。nickstuff 文件:

```
##### stuff fo aesthetics, etc
alias pyclean='find . | grep -E "(__pycache__|\.pyc|\.pyo$)" | xargs rm -rf'
alias duh="du ./ --max-depth 1 -h"
alias diffy='diff -y --suppress-common-lines'

if [ "$HOST" = 'gruffalo' ]; then export HOST_COLOR="\[033[1;36m\]"
fi

# custom
kptrk(){
    source ~/.bash-preexec.sh
    if [ "$KPTRK_ON" = true ]
    then echo "KeePTRacK is disabled"
    preexec() { true; }
    precmd() { true; }
    export KPTRK_ON=false
    export KPTRK_DIR=0
    else export KPTRK_DIR=$(pwd)
    echo "KeePTRacK is enabled!"
    preexec() { printf "$(date)\t$1  \n">> $KPTRK_DIR/kptrk_README; }
    precmd() { echo "kptrk is on"; }
    export KPTRK_ON=true fi
}

# get rid of files added when running setup.py
rmsetup(){
    python setup.py install --record files.txt
    cat files.txt | xargs rm -rf
}

# prompt stuff
# my default PS1
export PS1_def="\[\033[38;5;49m\]\u\[$(tput sgr0)\]\[\033[38;5;15m\]@\[$(tput sgr0)\]\[\033[38;5;34m\]\h\[$(tput sgr0)\]\[\033[38;5;15m\][\[$(tput sgr0)\]\[\033[38;5;139m\]\W\[$(tput sgr0)\]\[\033[38;5;15m\]]\$(__git_ps1) \[$(tput sgr0)\]"

# teaching PS1
export PS1_teach="\[\033[38;5;202m\]\u\[$(tput sgr0)\]\[\033[38;5;15m\][\[$(tput sgr0)\]\[\033[38;5;10m\]\w\[$(tput sgr0)\]\[\033[38;5;15m\]] \[$(tput sgr0)\]"

export PS1=$PS1_def
EDITOR=/usr/bin/nano 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

最后，我对这个设置很满意。我把所有的$PATH 和$LIBs 都放在我的。bashrc，以及我所有的便利功能、提示等都设置在我的。nickstuff 文件，易于移动到新机器。

我希望你觉得这有用！对我来说，用这种方式管理我的设置要容易得多，但我相信你们大多数人都有适合自己的解决方案；请在评论中分享！