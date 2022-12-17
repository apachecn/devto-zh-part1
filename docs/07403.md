# 从初级到(稍微更高级的)使用别名的 git。

> 原文：<https://dev.to/spences10/moving-from-beginner-to-slightly-more-advanced-git-withaliases>

您使用 Git 越多，您就越熟悉在您的项目或您团队的项目的日常工作流程中使用的命令。

命令，如命名和创建要素分支，如果您有必要的权限，则发出“拉”请求或推送您的更改。

我每天都在使用它，其他使用 git 的人[我想]都是使用`git add .`命令，然后是`git commit -m 'my awesome feature'`和`git push`或者`git push origin <branch>`

在我使用 Git 的短暂时间里，我总是打出完整的命令[通常我的备忘单就在手边]，什么都不想，这就是你使用这个工具的方式，对吗？

嗯，这是我愚蠢的假设，直到我了解了 dotfiles，我从听 [toolsday.io](http://www.toolsday.io/) 播客中了解到了 [Chris](http://twitter.com/chrisdhanaraj) 和 [Una](http://twitter.com/una) 这是一个了解工具ðÿ'的好渠道，播客是关于 [Git Tools](http://www.toolsday.io/episodes/git.html) 听一下吧，这是一个很棒的节目。

这对我来说是一次非常酷的学习经历，现在我有了一个非常高效的 git 工作流ðÿš€

让我们回顾一下`.gitconfig`，你还记得第一次在你的电脑上设置 Git 时必须输入你的电子邮件地址和姓名吗？这些信息存储在您的`.gitconfig`文件中，您的文件将位于 Windows `C:\Users\yourusername\.gitconfig`或 Linux/Mac 上的`~/.gitconfig`用户文件夹中

如果你在你选择的文本编辑器中导航到该文件并打开它，你会在`[user]`标志下看到你的详细信息，这是我的:

```
[user]
  name = spences10
  email = spences10apps@gmail.com 
```

Enter fullscreen mode Exit fullscreen mode

我不确定您的配置中可能还有哪些其他选项，所以我们将只关注别名，可以使用别名，以便您可以缩短命令[或者如果您愿意，可以使它们更长]，但我完全支持减少击键次数，即使少一两次。

所以让我们回顾一下我在开始提到的常见命令:

```
git add .
git commit -m 'my awesome feature'
git push 
```

Enter fullscreen mode Exit fullscreen mode

所以对于别名，我们可以把它们缩短一点:

在你的`.gitconfig`文件中，如果在`[aliases]`部分还没有添加的话，我在我的用户信息上面有我的，然后添加一些别名:

```
[alias]
  a = add .
  c = commit -am
  p = push

[user]
    name = spences10
    email = spences10apps@gmail.com 
```

Enter fullscreen mode Exit fullscreen mode

因此，现在我们可以缩短向我们的一个回购协议添加变更的工作流程:

```
git add .
git commit -m 'my awesome feature'
git push 
```

Enter fullscreen mode Exit fullscreen mode

会变成:

```
git a
git c 'my awesome feature'
git p 
```

Enter fullscreen mode Exit fullscreen mode

你输入的内容并没有大幅减少，但你会惊讶地发现，你会很快习惯，并开始添加更多的内容。

以下是我目前的别名列表:

```
[alias]
  a = add .
  b = branch
  c = commit -am
  cl = clone
  co = checkout
  d = diff
  f = fetch
  i = init
  o = open # see: https://github.com/paulirish/git-open â™¥
  p = push
  pt = push --tags
  s = status
  t = tag 
```

Enter fullscreen mode Exit fullscreen mode

我在发表这篇文章时发现了一个新的版本是`clone --depth 1`,它只克隆了存储库的头部，而不是整个存储库，所以如果你在克隆 react，你只会得到主版本，而不是存储库中的其他 38 个分支。非常整洁的ðÿ'，所以可以伪装成更短的东西`git cl1d`？

毫无疑问，你会注意到我这里有一个链接，链接的内容是`o = open`那个小宝石属于[保罗·爱尔兰](https://github.com/paulirish)它是一个 npm 包，会弹出一个浏览器标签，指向你当前所在的存储库，很棒吧？

我相信有很多很多方法可以配置 Git，如果你看一下保罗·爱尔兰的 dotfiles repo，你会发现有很多方法可以配置 Git，我还在学习和寻找新的方法。

如果有什么我错过了，或者如果你有一个更好的方法，然后请让我知道ðÿ'

在 GitHub 上让我上 Twitter 或者 T2 问我任何问题

如果你喜欢这篇文章，或者它对你有任何帮助，请给它一个赞，不要忘记在ðÿ™œ社交媒体上分享它