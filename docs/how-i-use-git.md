# 我如何使用 Git

> 原文：<https://dev.to/dmerejkowsky/how-i-use-git>

欢迎光临！

这篇文章将引导我如何使用 git。

我们将讨论 git 本身的配置、我编写的别名和脚本，以及我使用的其他工具。

# 简介

## 说话便宜，给我看看代码

一切都可以在我的 [dotfiles repo](https://github.com/dmerejkowsky/dotfiles/blob/master/configs/git/config) 里找到。

请随意阅读，但请不要直接使用它:这段代码只供一个人使用:我。除非你是我隐藏的弟弟，否则很可能不适合你。

## 配置文件的位置

我更喜欢把我的 git 配置文件放在`~/.config/git/config`而不是`~/.gitconfig`中。

这当然是个人喜好的问题，但也符合 [XDG 目录规范](https://specifications.freedesktop.org/basedir-spec/basedir-spec-0.6.html)

## 你为什么要费心写这个？

几个原因:

*   当我谈论这些事情时，人们似乎很喜欢(正如我如何 Lint 我的 Python 文章的相对成功所看到的)
*   当向他人描述我的配置文件和工作流时，我倾向于发现可以改进的地方。
*   我总是希望你能从我的经历中学到一些东西(否则，我为什么要分享它？)

这个问题解决了，让我们开始吧！

## 选项

### 排除文件

我喜欢保留一个我总是想忽略的文件模式的单独列表，而不去碰我参与的所有项目的`.gitignore`:

```
# in ~/.config/git/config [core]
excludesfile = ~/.config/git/excludes 
```

Enter fullscreen mode Exit fullscreen mode

```
# in ~/.config/git/excludes
# Vim
*.swp

# QtCreator
CMakeLists.txt.user
*.autosave 
```

Enter fullscreen mode Exit fullscreen mode

### 再看

`rerere`代表*重放记录的合并分辨率*。

您必须显式启用它:

```
[rerere]
enabled = true 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看它的实际应用:

*   我在做一个叫`new-feature`的分支，一周前开始的。
*   我决定基于最新的`master`版本:

```
$  git fetch origin
$  git rebase oirigin/master
First, rewinding head to replay your work on top of it...
Applying: new feature ... CONFLICT (content): Merge conflict in bar.txt Recorded preimage for 'bar.txt'  #  <---- rerere
error: Failed to merge in the changes.
Patch failed at 0001 new feature $  git mergetool
#  fix conflicts
$  git rebase --continue
Applying: new feature Recorded resolution for 'bar.txt'. #  <--- rerere 
```

Enter fullscreen mode Exit fullscreen mode

因此，假设`master`和`new-feature`都继续变化，我们需要重新运行`git rebase` :

```
$  git rebase origin/master
 bar.txt | 1 +
 1 file changed, 1 insertion(+)
First, rewinding head to replay your work on top of it...
Applying: new feature .... Falling back to patching base and 3-way merge...
Auto-merging bar.txt
CONFLICT (content): Merge conflict in bar.txt Resolved 'bar.txt' using previous resolution. #  <--- rerere 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在这种情况下，如果您试图像往常一样运行`mergetool`，您会得到一条消息，说*没有文件需要合并*。
改为使用`git add` :

```
$  git add bar.txt
$  git diff --staged -- bar.txt # Check that the changes still make sense
$  git rebase --continue 
```

Enter fullscreen mode Exit fullscreen mode

如果你不喜欢明确地输入`git add`，你可以让 git 帮你输入:

```
[rerere]
autoUpdate = true 
```

Enter fullscreen mode Exit fullscreen mode

### 拉

默认情况下，`pull`中无非是`git fetch`后跟`git merge`。

比起合并，我通常更喜欢 rebase，所以我将`git pull`配置为总是执行 rebase:

```
[pull]
rebase = true 
```

Enter fullscreen mode Exit fullscreen mode

如果我*真的*需要合并，我会跑:

```
$  git fetch origin
$  git merge origin/master 
```

Enter fullscreen mode Exit fullscreen mode

### rebase

默认情况下，在许多情况下，git 会向您显示更改内容的摘要(a *diffstat* ),比如快速合并:

```
$  # on master, behind origin/master
$  git merge
Updating 24878f5..5be8c2e
Fast-forward
 bar.txt | 1 +
 1 file changed, 1 insertion(+) 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果你不快进到另一个分支，它就不会这样做，除非你有:

```
[rebase]
stat = true 
```

Enter fullscreen mode Exit fullscreen mode

原因可能是计算 *diffstat* 很昂贵(至少在很多情况下比实际的合并更昂贵)，但我个人并不介意时间上的成本。

通常这些信息让我意识到潜在的冲突。

#### 修复历史

假设您有一个 3 次提交的列表，最后一次提交是对第一次提交的修复:

```
#  edit foo.py
$  git commit -m "Foo: add bar() method"
#  write some code in bar.py
$  git commit -m "Bar: add baz() method using Foo"
#  realize a crash, patch foo.py again 
```

Enter fullscreen mode Exit fullscreen mode

现在，您希望第三个提交被第一个提交压扁。这将使代码审查更容易，历史更清晰。

有两种方法可以解决这个问题:

首先，您可以进行新的提交并运行`git rebase --interactive` :

```
$  git commit -m "Fix Foo.bar() crash when called without arguments"
$  git rebase -i master
pick bbace84 Foo: add bar() method
pick 5499c6d Bar: add baz() method using Foo
pick 33c32e1 Fix Foo.bar() crash #  Edit file to have:
pick bbace84 Foo: add bar() method
fixup 33c32e1 Fix Foo.bar() crash
pick 5499c6d Bar: add baz() method using Foo #  quit and save
Successfully rebased and updated refs/heads/foobar. 
```

Enter fullscreen mode Exit fullscreen mode

或者(这样更快)，确保您的最后一次提交以`fixup!`开始，后面是您想要修复的提交消息的前缀。

然后设置:

```
[rebase]
autosquash = true 
```

Enter fullscreen mode Exit fullscreen mode

之后，当你运行`git rebase -i`时，行`fixup`会神奇地出现:

```
$  git comit -m 'fixup! Foo: add bar'
$  git rebase -i master
#  check the "rebase-todo" file is correct
#  done! 
```

Enter fullscreen mode Exit fullscreen mode

### 合并

```
[merge]
tool = kdif3 
```

Enter fullscreen mode Exit fullscreen mode

我用`KDiff3`主要是因为我太习惯了，不想改变。一些事实:

*   缺点:
    *   它又大又慢，取决于`KDE4`
    *   它发出恼人的声音(这是对`KDE4`产生巨大依赖性的原因)...)
    *   它能够解决 git 自动处理的冲突，这很好，但有时会出错。(尽管这种情况非常罕见)
    *   它让你为冲突选择' A '，' B '或' C '，但有时你想直接在' D '中*编辑*行，这个编辑器使用起来很痛苦。
*   优点:
    *   它显示了四个窗口:“A”，共同的祖先，“B”，你这边的文件，“C”，另一边的文件，“D”，合并的结果。
    *   您可以使用“快速和肮脏”的解决方法，如“到处选择' C '”，或“对所有未解决的冲突选择' C '”

因此，当 KDiff3 失败时，我往往只是直接在 Neovim 中编辑未合并的文件，并手动处理冲突标记(`<<<<<<`、`=======`、`>>>>>>`)<sup id="fnref1">、 [1](#fn1) 、</sup>)。

注意:`git`在合并解析期间自动写入一个`.orig`文件用于备份目的。

您可以使用
关闭此功能

```
[merge]
keepBackup = false 
```

Enter fullscreen mode Exit fullscreen mode

我花了一段时间才弄明白这一点，但 KDiff3 *也*在它完成时写一个`.orig`文件，所以你必须在`KDiff3`设置窗口中取消勾选一个框，让`.orig`文件真正消失。

## 别名

我有很多别名。其中一些很常见:

```
ci = commit
co = checkout 
```

Enter fullscreen mode Exit fullscreen mode

因为我从来没有记住*修正*中有多少个 *m* ，所以我有:

```
mend = commit --amend 
```

Enter fullscreen mode Exit fullscreen mode

### 日志

你会发现许多人试图得到一个丰富多彩且有用的东西。

以下是我对此的看法:

```
lg = log --color --graph --pretty=format:'%Cgreen%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
lgs = log --graph --pretty=format:'%Cgreen%h%Creset - %s %C(yellow)%d' --abbrev-commit 
```

Enter fullscreen mode Exit fullscreen mode

诀窍是拨动`pretty format`弦。您可以在 git 文档中找到所有相关信息。

### 摘樱桃

有时你想从开发分支中挑选一个 bug 修复到发布分支。在这种情况下，添加`-x`选项会导致原来的 sha1 不会丢失:

```
$  git cherry-pick -x develop
$  git log
Critical bug fix

(cherry picked from commit af123ed) 
```

Enter fullscreen mode Exit fullscreen mode

为了确保我不会忘记`-x`，我创建了专用的别名:

```
ck = cherry-pick
ca = cherry-pick --abort
cx = cherry-pick -x 
```

Enter fullscreen mode Exit fullscreen mode

### Rebase

当你运行`git rebase -i`时，git 会提示你运行`git rebase --skip`，或者`--continue`。有时你会想中止。我这样做是为了在任何情况下，我只需要输入两个字母:

```
ri = rebase -i
rc = rebase --continue
rs = rebase --skip
ra = rebase --abort 
```

Enter fullscreen mode Exit fullscreen mode

(注意运行`--abort`的别名以字母`a`结尾，就像`cherry-pick --abort`的别名一样)

通常，我要么在工作，在`origin`有一个中央存储库，要么我在为一个开源项目做贡献，我有一个`upstream`源。

这意味着我需要两个不同的别名:

```
ro = rebase -i origin/master
ru = rebase -i upstream/master 
```

Enter fullscreen mode Exit fullscreen mode

### 去吧

`go`就是`reset --hard`。只是几乎*从来不*用`git reset`不带这个选项，所以这个更得心应手:

```
go = reset --hard 
```

Enter fullscreen mode Exit fullscreen mode

### @{u}

`@{u}`是一种特殊的语法，表示“当前分支所跟踪的的远程 ref”。所以如果你在`master`上，这通常是`origin/master`

由于`@{u}`很难输入，我有几个别名，都以`u`结尾:

```
gou = reset --hard @{u}
logu = log @{u}
diffu = diff @{u} 
```

Enter fullscreen mode Exit fullscreen mode

同样的原因，我经常需要和`origin/master`比较，所以这次别名都是以`o`结尾:

```
logo = log origin/master
diffo = diff origin/master 
```

Enter fullscreen mode Exit fullscreen mode

## 助手脚本

### 作别名

Git 允许插入小段 bash 代码，而不仅仅是替换字符串，当您用 bang:
开始正确的值时

```
[alias]
prune-merged = !git branch --merged | grep dm/ | grep -v "\\*" | xargs -n1 git branch -d 
```

Enter fullscreen mode Exit fullscreen mode

在工作中，我们都用我们的首字母作为我们的 dev 分支的前缀，但我经常忘记在完成后删除它们。

这个别名查找所有完全合并的、以`dm/`开始的本地分支，并删除它们。

注意我们如何使用`xargs -n1`来绕过`git branch -d`只接受一个参数的事实。

### 作为独立文件

默认情况下，当你运行`git foo`时，git 会在`$PATH`中的任何地方寻找一个名为`git-foo`的可执行文件并运行它。

(如果你想知道，像`fetch`或`push`这样的内置命令在`/usr/lib/git-core/`中)

您可以将它与别名结合起来，为您编写的助手脚本取一个好听的名字，同时仍然需要键入更少的字母。

### 懒推

比如，我有:

```
[alias]
fp = fpush 
```

Enter fullscreen mode Exit fullscreen mode

然后在我有一个名为`bin/git-fpush` :
的脚本

```
#!/bin/bash

set -e

function main() {
  if [[ -z "$1" ]] ; then echo "Missing file name"
    return 1
  fi
  if [[ ! -f "$1" ]] ; then echo "$1 is not a regular file"
    return 1
  fi git add $1
  git commit -m "Update $1"
  git push
}

main "$@" 
```

Enter fullscreen mode Exit fullscreen mode

这意味着当我键入`git fp foo`，`git`会将别名展开为`git push`，然后运行`git-fpush`脚本。

我主要在私有存储库中使用这个脚本，当我想要的只是提交一个文件，而不是真正的消息。

同样，我有另一个脚本，它尽一切努力在一个命令中完成所有的更改:

```
#!/bin/bash

set -e

function main() {
  if [[ -z "$1" ]] ; then echo "Missing commit message"
    return 1
  fi git commit --all --message "$1"
  git push
}

main "$@" 
```

Enter fullscreen mode Exit fullscreen mode

```
[alias]
cp = commit-and-push 
```

Enter fullscreen mode Exit fullscreen mode

### Rebase

最后，我有一个助手脚本来重定最后“n”次提交的基础，因为，你可能已经猜到了，我花了很多时间来重定基础。

于是`git rebase -i HEAD~5`变成了`git r 5`。

同样的想法，一个 bash 脚本:

```
#!/bin/bash

set -e

function main() {
  if [[ -z "$1" ]] ; then echo "Usage git r <number of commits>"
    return 1
  fi git rebase --interactive "HEAD~$1"
}

main "$@" 
```

Enter fullscreen mode Exit fullscreen mode

还有一个别名:

```
[alias]
r = rebase-n-commits 
```

Enter fullscreen mode Exit fullscreen mode

### 返回顶层目录

我经常使用这个命令。下面是实现:

```
function gcd() {
  topdir=$(git rev-parse --show-toplevel)
  if [[ $? -ne 0 ]]; then
    return 1
  fi cd "${topdir}/${1}"
} 
```

Enter fullscreen mode Exit fullscreen mode

假设我是资源库的根是`foo`，我在`foo/src`，`gcd`会把我送到`foo`，`gcd include`送到`foo/include`。

注意对`git rev-parse`的调用，它允许您不要试图复制 git 用来查找顶层目录的逻辑(提示:这比您想象的要难)

## Gui 工具

我从 shell 中执行大部分 git 命令，但有时我需要一个图形界面(并使用鼠标)

### gitk

我在需要时使用 gitk:

*   对几个分支的历史有一个高层次的看法。(为此使用`gitk --all`)

*   重写几个分支的历史。从 gitk 可以很容易地检查各种分支，应用精选，重置分支到其他提交等等。

*   查找添加或删除给定字符串的提交，并只选择在一个给定文件中所做的更改。

在后台使用`git log`的`-S`选项。

顺便说一句，`gitk`理解`git log`所理解的所有选项。因此，您可以使用:`gitk -- src/foo`来只显示给定子目录中的提交。

### git GUI

当我知道我在文件中留下了不想在下次提交时使用的东西时，我会使用 git-gui:调试日志、注释，...

我喜欢你能以非常直观的方式选择大块或小线条的事实。

我还经常使用“恢复对此文件所做的更改”功能，因为我直接看到了可能会丢失的更改，所以我对不覆盖重要的内容更有信心。

除此之外，我还添加了一些配置选项，以便在顶部菜单中有更多可用的操作:

```
[guitool "pull-rebase"]
    cmd = git pull --rebase

[guitool "clean"]
    cmd = git clean -fd
    confirm = true

[guitool "reset"]
    cmd = git reset --hard
    confirm = true 
```

Enter fullscreen mode Exit fullscreen mode

请注意我是如何让`confirm = true`执行“危险”操作的，这样`git-gui`会显示一个弹出窗口，以便事先确认。

最后一个是在编写提交消息时激活英语拼写检查:

```
[gui]
    spellingdictionary = en_US 
```

Enter fullscreen mode Exit fullscreen mode

我对 Neovim 也有类似的配置:

```
augroup spell
  autocmd!
  autocmd filetype gitcommit  :setlocal spell spelllang=en
augroup end 
```

Enter fullscreen mode Exit fullscreen mode

## Neovim

拼图的最后一块是与 Neovim 的交互。

我用蒂姆·波普的《vim-逃犯》来做这个。

它有大量的功能。

我用它在我的状态行中显示一个当前分支:

```
set statusline=%{VimBuddy()}\ [%n]\ %<%f\ %{fugitive#statusline()}%h%m%r%=%-14.(%l,%c%V%)\ %P\ %a 
```

Enter fullscreen mode Exit fullscreen mode

是的，你可以通过设置一个变量来配置你的状态行，不需要专门的插件… <sup id="fnref2">[2](#fn2)</sup>

注意，大多数命令只有在逃犯检测到您正在编辑来自 git 存储库的文件时才有效。

下面是我最常用的命令，接下来是它们的效果，假设我正在编辑一个名为`foo.c`的文件:

*   `:Gwrite`运行`git add foo.c`
*   `:Gread`:从最近一次提交中恢复`foo.c`的内容(又名:撤消所有未暂存的更改)
*   `:Gmove foo2.c`:运行`git mv foo.c foo2.c`，切换到`foo2.c`缓冲区，不告诉你“foo.c 不再可用”) <sup id="fnref3">[3](#fn3)</sup>
*   `:Gdiff`:显示`foo.c`中的差异。默认情况下，起点是当前分支，但是你可以使用`:Gdiff origin/master`来选择一个不同的起点。
*   `:Gblame`:运行`git blame foo.c`，当光标位于行尾时按‘Enter’键，显示匹配提交的详细信息。
*   `:Ggrep`:同`:grep`，但使用内置的`git grep`命令。由于在 git 存储库中我*不是*的情况很少，所以我也倾向于使用 [Ag.vim](https://github.com/rking/ag.vim) 。
*   `:Gcd`:与之前描述的`gcd`命令效果完全相同:)

# 结论

git 需要花费相当多的时间来学习，但是它也提供了大量定制其行为的方法。

我希望我已经给了你所有可能的想法。

下次见！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)，了解更多与我联系的方式。

* * *

1.  我试图直接使用 Neovim 作为 git mergetool，但我发现它太令人困惑了。 [↩](#fnref1)

2.  `VimBuddy()`部分可以在 asci NEMA 上的动作[中看到](https://asciinema.org/a/47001) [↩](#fnref2)

3.  顺便说一句，Tim Pope 的 vim-太监的‘Move’命令也做同样的事情。 [↩](#fnref3)