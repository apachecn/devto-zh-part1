# 我最喜欢的 Git 助手

> 原文：<https://dev.to/andrew565/my-favorite-git-helpers>

您可以找到所有这些别名和函数(甚至更多！)在我的 Github 上:https://github.com/Andrew565/dotfiles。请继续阅读关于它们是什么和它们做什么的详细解释，并且一定要查看我的[首选 git 工作流](https://andrewscripts.wordpress.com/2017/06/08/my-one-way-git-workflow-or-how-to-git-well/)。

我在我的终端中使用 bash。我知道 ZSH 和鱼以及所有其他的都有它们的优点，但是我从 bash 开始，我很喜欢它，所以我就用它。

我在 bash 最喜欢做的事？为所有东西创建别名和辅助函数，尤其是 Git 命令。所以，这里列出了一些我最喜欢的。

# 痛击别名

```
# Git Aliases
alias g="git"
alias gs="git status"
alias gl="git log"
alias grh="git reset --hard"
alias gcmm="git checkout --"
alias gsm="git stash && git co master && git stash pop"
alias gsd="git stash && git co development"
alias gmd="git merge development"
alias gmm="git merge master"
alias gupdate="git co development && git pull"
alias glatest="git co development && git pull"
alias gmupdate="git co master && git pull"
alias gcm="gmupdate"
alias gphm="git push heroku master" 
```

Enter fullscreen mode Exit fullscreen mode

其中大部分可能是不言自明的。这里有几个命令可能不是很明显:

`alias gsm="git stash && git co master && git stash pop"`

当我在一个特性分支中工作时，这是非常宝贵的，我实际上想在主分支中工作。这种情况已经不常发生了，因为大多数时候我从来没有直接向师父承诺过，但它还是发生了。

`alias gcmm="git checkout --"`

当您有一个准备提交的文件，并且您实际上不希望它成为当前提交的一部分时，可以使用此命令。它也可以别名为“unstage”。

# Bash 函数

化名只能帮到你这么多。有时，您希望能够将函数调用捆绑在一起，或者使用变量。这就是 bash 函数的用武之地。

## gnew -创建新的分支

```
# create new branch from a default named branch
# modify the first line to make it an argument if desired
gnew() {
 DEST_BRANCH=$2
 : ${DEST_BRANCH:='master'}
 git co $DEST_BRANCH
 git pull
 git co -b "$1"
 # bundle install # If using Bundler (ruby)
 # bundle exec rake db:migrate
 git push -u
} 
```

Enter fullscreen mode Exit fullscreen mode

正如它所说，这会在另一个分支的基础上创建一个新的分支。如果您没有指定要从哪个分支分支出来，它将假定/默认为“主”分支。像这样使用:

`gnew new-branch-name old-branch-name`

然后，它检查旧的分支/主控，确保您是从最新的修订中分支的，然后使用您提供的名称作为第一个参数创建一个新的分支。注释掉的是运行 bundler 和 rake db:migrate 的命令，只有在处理 Ruby/Rails 项目时才有用。最后，它执行`git push -u`，该操作推上新的分支，并设置本地分支来跟踪远程版本，这使得未来的推和拉命令更加无缝。

## GRM-Git Rebase Master

```
# Get the latest changes on master pulled down locally
# and then rebase them into/onto the current branch
grm() {
  CURRENT=`git rev-parse --abbrev-ref HEAD` # figures out the current branch
  git checkout master
  git pull
  git checkout $CURRENT
  git rebase master
} 
```

Enter fullscreen mode Exit fullscreen mode

在[我的首选工作流](https://andrewscripts.wordpress.com/2017/06/08/my-one-way-git-workflow-or-how-to-git-well)中，我总是在提交之前将最新的提交从 master 返回到我的 feature 分支。这使得合并冲突最小化，并且有助于保持提交历史的干净。

## gsrm-Git Stash&T3】Rebase Master

```
# Stash current, then update to latest, then pop the stash
gsrm() {
  git stash
  grm
  git stash pop
} 
```

Enter fullscreen mode Exit fullscreen mode

类似于 grm(它甚至利用了 grm ),它会将您当前正在处理的尚未被登台/提交的任何内容隐藏起来，然后进行 rebase，然后再次将其弹出。如果您知道您想要引入到您的特性分支中的上游变更，但是您正在处理的内容还没有准备好提交，那么这将非常有用。旁注:不要用这个作为不经常犯错误的借口。你绝对应该继续这么做。

## gswitch -快速结账分店

```
gswitch() {
  BRANCH=`git branch | grep "$1"`
  git checkout $BRANCH
} 
```

Enter fullscreen mode Exit fullscreen mode

通过问题编号或功能关键字切换分支。例如:

`gswitch 1808 == git checkout features/1808-click-on-avatar`

注意:只有使用唯一的描述才能正常工作，因此建议使用发行号。如果您定期删除已经合并的本地分支机构，效果也最好。

# .gitconfig

如果你没有利用全球[。gitconfig](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) 文件，你做错了。这里我也有一些别名和一些函数。

## 别名-查找您有哪些别名

`aliases = !git config --get-regexp 'alias.*' | colrm 1 6 | sed 's/[ ]/ = /'`

我*所以*需要在 bash 中重新创建这个，我很难记住我创建的所有别名(T2 有很多)。ðŸ˜

## cam -添加文件时提交并附加消息

`cam = commit -am`

这(本质上)与以下内容相同:

`git add . && git commit -m "your message"`

如果您有任何不想提交的未转移文件，请注意不要使用此选项，因为它会将所有内容都添加到提交中。

## last——我犯的最后一件事是什么？

`last = cat-file commit HEAD`

## 共同结账

`co = checkout`

## 删除-本地-更快地清除旧分支

`delete-local = branch -d`

不知何故，我更容易记住`git delete-local`而不是`git branch -d`。

## 删除-远程-快速清除远程分支

`delete-remote = "!sh -c 'git push origin :$1' -"`

注意:这并不是问你在做之前是否知道自己在做什么，所以要小心！

## 【分支机构】-获取所有本地分支机构的列表

`branches = "!sh -c 'git branch -a | grep -v remotes'"`

如果您还想要一个远程分支的列表，您可以去掉`grep -v remotes`。这是“自然语言”的另一个别名，输入`git branches`感觉更好。

## 编辑-未合并&添加-未合并-像老板一样修复合并冲突

```
# use like `git edit-unmerged; ...edit..., ...test...
# git add-unmerged; get commit || rebase --continue
edit-unmerged = "!f() { git diff --name-status --diff-filter=U | cut -f2 ; }; code `f`"
add-unmerged = "!f() { git diff --name-status --diff-filter=U | cut -f2 ; }; git add `f`" 
```

Enter fullscreen mode Exit fullscreen mode

这为我节省了太多时间，真是可笑。基本上，`edit-unmerged`会查看所有标记为“未合并”的文件，这意味着它们有需要解决的冲突。然后在您的代码编辑器中打开它们(用您喜欢的编辑器的 cli 别名替换上面的`code`，`vi`，`emacs`，`subl`等等)。).当您完成时，`add-unmerged`再次找到未合并文件的列表，并对它们执行`git add`(并且只对它们执行，所以您不必担心会添加什么)。此时，一切都应该准备好了，所以您可以运行`git commit`或`git rebase --continue`。

# 期末笔记

记住，一个好的工匠从不抱怨他的工具。这些助手并不是要取代真正知道你在做什么。这里又是我的 Github repo 与这些文件的链接:https://github.com/Andrew565/dotfiles。