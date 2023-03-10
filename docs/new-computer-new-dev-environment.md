# 新计算机，新开发环境

> 原文：<https://dev.to/andy/new-computer-new-dev-environment>

所以我最近得到了我的第一台新 MacBook Air！耶！但是我不得不重新设置一切...嗯，很像[以前关于设置开发环境的帖子](https://dev.to/jess/getting-my-dev-environment-set-up-again-my-day-in-dependency-hell)，我也经历了一点搞清楚每一件小事的痛苦。尽管我很想责怪电脑，但我知道这只是我的错，因为我忘了很多该怎么做。所以我在这里为我未来的自己写这篇文章，以防我不得不再次这样做，也为任何可能不得不这样做的人。

## 基础知识

在 activate 做助教帮助我记起了一些关于建立我的环境的事情。有一个很棒的向导可以让我跟随，我最初只是想了解一些基础知识。在这种情况下，基本意味着我需要工作的任何东西:

*   铬
*   松弛的
*   原子
*   公司自产自用
*   正确的 Ruby 版本
*   轨道
*   一种数据库系统
*   转到 setup
*   终端设置
*   Atom 包(Linter，Emmet，ERB-Rails 的代码片段，等等)

Chrome、Slack 和 Atom 非常简单，而 Go Rails 指南使得安装其他软件变得非常容易。我经历了和 Jess 在上面的帖子中遇到的`rbenv`类似的地狱，谢天谢地，在谷歌搜索了大量内容后，我修复了它。太好了！一切都结束了...对吗？

你瞧，我有一些生活质量设置缺失。我没有终端或 Git 自动完成。还好我的大脑提醒我只是复制我的旧点文件(。bash_profile，。例如，inputrc)，我们应该没问题。

搞定了。我想。我仍然会有那种感觉，觉得我错过了什么，但它可能会一直在那里，直到我习惯了在这台电脑上打字。

这是我的`.bash_profile`给感兴趣的人:

```
# Show what Git branch you're on in command line
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
export PS1="\[\033[36m\]\w\$(parse_git_branch)\[\033[0m\]  $ "
# git auto complete commands and branch names
if [ -f ~/.git-completion.bash ]; then
  . ~/.git-completion.bash
fi
# fix rbenv shell command
eval "$(rbenv init -)" 
```

Enter fullscreen mode Exit fullscreen mode

和我的自动完成 bash 的`.inputrc`:

```
set completion-ignore-case on
TAB:menu-complete 
```

Enter fullscreen mode Exit fullscreen mode

## 永无止境的添加列表:

*   Emmet 和 Atom 有时并不擅长自动完成。要修复，请转到设置栏:Atom -> Keymap...并粘贴以下:`'atom-text-editor[data-grammar="text html ruby"]:not([mini])': 'tab': 'emmet:expand-abbreviation-with-tab'`用. html.erb 文件重启和跳转应该可以。(不使用 Ruby 的可以直接删除`[data-grammar="text html ruby"]`。)