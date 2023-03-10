# 使用 git 挂钩来改进您的日常工作流程

> 原文：<https://dev.to/fedekau/using-git-hooks-to-improve-your-day-to-day-workflow-4nl5>

*最初发表于 [WyeWorks 博客](https://wyeworks.com/blog/2018/1/3/using-git-hooks-to-improve-your-day-to-day-workflow/)。*

如果你开发软件已经有一段时间了，你可能已经注意到有很多事情可能出错，无论你多么努力尝试，总有一些事情你可能会忘记，因为毕竟，我们只是在做一项极其困难的任务的人类:告诉计算机做什么。

在这篇文章中，我将展示我们在 WyeWorks 的一些项目中使用的一些 **git-hooks** ,通过防止糟糕的提交甚至离开他们的计算机，使开发人员的生活更加轻松。我将涵盖*林挺*、*测试*和*提交格式化*用例。

## 什么是 git-hooks？

> 像许多其他版本控制系统一样，Git 有办法在某些重要动作发生时触发定制脚本。这些钩子有两组:*客户端*和*服务器端*。客户端钩子由提交和合并等操作触发，而服务器端钩子运行在网络操作上，如接收推送的提交。你可以出于各种原因使用这些钩子。

## 安装挂钩

> 这些钩子都存储在 Git 目录的 hooks 子目录中。在大多数项目中，那是`.git/hooks`。当您通过运行`git init`初始化一个新的存储库时，Git 会用一堆示例脚本填充 hooks 目录，其中许多脚本本身就很有用；但是它们也记录每个脚本的输入值。所有的例子都是作为 shell 脚本编写的，并加入了一些 Perl，但是任何适当命名的可执行脚本都可以很好地工作——您可以用 Ruby 或 Python 或任何您熟悉的语言编写它们。如果你想使用捆绑的钩子脚本，你必须重命名它们；他们的文件名都以`.sample`结尾。
> 
> 要启用一个 hook 脚本，将一个文件放在您的`.git`目录的 hooks 子目录中，该文件被适当地命名(没有任何扩展名)并且是可执行的。从这一点出发，它应该被称为。

在这篇文章中，我们将只使用一些客户端钩子来执行定制脚本。接下来是我最喜欢的挂钩和用例:

### 预提交

> 在您键入提交消息之前，`pre-commit`钩子首先运行。它用于检查即将提交的快照，查看您是否忘记了什么，确保测试运行，或者检查您需要在代码中检查的任何内容。从这个钩子中退出非零值会中止提交，尽管您可以用`git commit --no-verify`绕过它。您可以做一些事情，比如检查代码风格(运行 lint 或类似的东西)，检查尾部空白，或者检查关于新方法的适当文档。

```
#!/usr/bin/env bash

if ! bundle exec rubocop -D -E -S -c .rubocop.yml; then echo "Offences were found. Your commit will not pass the CI."
  echo "The commit was aborted, but if you really want to commit anyway run: git commit --no-verify -m 'Commit message'"
  exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们使用[Rubocop](https://github.com/bbatsov/rubocop)(Ruby 的一个林挺宝石)来检测代码是否有问题，如果有，我们让用户知道并退出状态代码 1 以防止代码被提交。

### 提交-消息

> `commit-msg`钩子接受一个参数，这个参数是包含开发人员编写的提交消息的临时文件的路径。如果这个脚本非零退出，Git 将中止提交过程，因此在允许提交之前，您可以使用它来验证项目状态或提交消息。您可以使用它来检查您的提交消息是否符合所需的模式，或者以某种有用的方式修改您的提交。

```
#!/usr/bin/env bash

if grep -q -i -e "WIP" -e "work in progress" $1; then read -p "You're about to add a WIP commit, do you want to run the CI? [y|n] " -n 1 -r < /dev/tty
    echo if echo $REPLY | grep -E '^[Nn]$' > /dev/null; then echo "[skip ci]" >> $1
    fi
fi 
```

Enter fullscreen mode Exit fullscreen mode

这个例子在开发人员输入的提交消息中搜索某个模式，如果找到该模式，它假设提交是一个进行中的代码，并要求开发人员确认他是否想在 CI 中运行这个代码，如果用户说 *no* ，那么钩子修改提交以添加一个*【跳过 CI】*标签。此标签表示持续集成服务，如 CircleCI、TravisCI 等。我们不想为这个提交运行测试套件。

### 预推送

> `pre-push`钩子在 git 推送期间运行，在远程 refs 被更新之后，但在任何对象被传输之前。它接收遥控器的名称和位置作为参数，并通过 stdin 接收要更新的 refs 列表。您可以使用它在推送发生之前验证一组 ref 更新，或者中止对特定分支的推送，非零退出代码将中止推送。

```
#!/usr/bin/env bash

protected_branch='master'
current_branch=$(git symbolic-ref HEAD | sed -e 's,.*/\(.*\),\1,')

if [ $protected_branch = $current_branch ]; then read -p "You're about to push master, is that what you intended? [y|n] " -n 1 -r < /dev/tty
    echo if echo $REPLY | grep -E '^[Yy]$' > /dev/null; then exit 0 # push will execute
    fi exit 1 # push will not execute
else exit 0 # push will execute
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果开发人员试图推送到像 *master* 这样的重要分支，这个脚本将要求确认，如果开发人员确认，那么推送将照常发生，否则它将阻止分支被推送。

## 在项目中设置挂钩

由于`.git`文件夹本身不在版本控制之下，钩子只对安装它们的开发人员可用，这对于一些钩子来说可能足够好了，但对于另一些钩子来说则不然。

例如，如果您想为所有开发人员保护一个分支，那么这最好是在版本控制之下，并且当开发人员签出代码时，他们有可用的版本。嗯，这很容易做到！

如果你的项目有一个设置脚本，你只需要添加`git config core.hooksPath <path-to-hooks-folder>`就可以了，这将使你的库在那个文件夹中寻找钩子，你可以在版本控制下的库中找到它。在此之后，当开发人员安装项目时，挂钩将会就位并工作。

## 结论

有了这三个挂钩，您可以确保开发人员不会将会失败或不必要运行的代码推送到您选择的 CI 中，您可以运行林挺软件并避免不必要的推送到受保护的分支。

它还将通过避免失败的 CI 运行来节省资源，并且将加速您的反馈循环，因为所有的检查将在您的本地机器上立即运行。

这将让你的思维自由地思考更重要的事情，避免其他错误，而不是专注于这些简单的事情，这些事情很容易自动化。

## 学分

引用的对 **git-hooks** 的解释和定义摘自 git-hooks 的[官方文档。更多可用的钩子参见他们的文档。](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)