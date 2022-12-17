# 如何自己编写类似“git 刷新”的 git 命令？

> 原文：<https://dev.to/shobhit/git-refresh-4hn>

### **快速总结**

你使用 git 版本控制吗？如果你从事软件开发，我肯定你在某个时候用过它。你能想出你一天中多次使用 git 执行的动作吗？举个例子，我每天用 remote master 更新我的本地代码很多次。每次都需要几个步骤。在这篇文章中，我将向你展示如何用两个简单的步骤编写你自己的定制命令。然后，您将能够将许多 git 命令组合成一个命令。在文章的最后，有一个到 [GitHub 库](#github-repository)的链接。在那里你可以找到许多现成的定制 git 命令，比如`git refresh`和`git switch`。

### **简介**

作为工程师，我们有简化和自动化任务的内在愿望。虽然其他人可能会称之为懒惰，但这样做的目的是减少做日常工作的努力。让我们将它应用到 git 动作中，并以`git refresh`为例。在发展过程中，你经常会到达一个你想记住的阶段。当这种情况发生时，提交更改并将代码推送到您的远程分支是一个好主意。在创建提交之前，习惯上用远程基本分支更新本地分支。这有助于在发出拉请求时避免任何冲突。在大型团队中，远程分支经常更新，导致您的本地分支落后。因此，随着团队规模的扩大，这一步变得更加必要。要执行上述用远程主机更新本地主机的任务，平均来说，您需要执行以下六个步骤:

```
 # Stash the changes in the current branch
   git stash
   # Checkout master branch 
   git checkout master
   # Pull from remote master and rebase
   git pull --rebase origin master
   # Go back to the previous branch where you were working
   git checkout current-branch
   # Update current local branch from the local master
   git rebase master
   # Apply the stashed changes
   git stash apply 
```

Enter fullscreen mode Exit fullscreen mode

对于一个你一天要执行很多次的任务，“肯定有更好的方法。还有凯文！”

[![](img/21c3e03562d5814c8d148f64f0b3c5f0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9UPs7Yi8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ug4d6int3hqrrcc6atkq.gif)

### **创建自定义命令**

对于我们的示例`git refresh`，我们需要告诉`git`从远程主机更新我们当前的分支。

**第一步**:创建一个名为`git-refresh`的文件。在`/Users/user/Documents/gitScripts`将文件保存在一个文件夹中。在`git-refresh`文件中写下以下内容。注释解释了这些步骤:

```
#!/bin/sh

# Check if params are enough to go ahead.
remoteBranch=$1
test -z "$remoteBranch" && echo "ERROR: Please provide the source remote branch." 1>&2 && exit 1

# Find which is your current branch
if currentBranch=$(git symbolic-ref --short -q HEAD)
then
    echo On branch "$currentBranch"
    echo "Pulling updates from the remote branch $remoteBranch ..."

    # Stash current changes
    git stash
    # Checkout remote branch from where you want to update. 
    git checkout "$remoteBranch"
    # Pull the branch to update it
    git pull --rebase origin "$remoteBranch"
    # Checkout current branch which you were on before.
    git checkout "$currentBranch"
    # Rebase the changes
    git rebase "$remoteBranch"
    # Apply the stashed changes
    git stash apply

    echo "Updated the $currentBranch with changes from $remoteBranch"
else
    echo ERROR: Cannot find the current branch!
fi 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**:将目录路径添加到您的环境`PATH`。对于 Linux/Mac，你可以编辑你的`bash_profile`。在开头添加以下行:

```
 #!/bin/bash
    # For git commands
    export PATH=$PATH:/Users/user/Documents/gitScripts
    # Other existing export statements.
    # End of file 
```

Enter fullscreen mode Exit fullscreen mode

`source`要应用更改的文件:

```
 source ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

和...搞定了。

### **用法**

```
git refresh master 
```

Enter fullscreen mode Exit fullscreen mode

### 
  
**GitHub 库**

请在下面的 git 存储库中找到定制命令的脚本。[https://github.com/shobhitpuri/git-refresh](https://github.com/shobhitpuri/git-refresh)示例自定义命令包括`git refresh`、`git switch`和`git pushremote`。

您可以按照上述步骤创建自己的命令。创建一个新文件`git-yourcommand`，并在 shell 脚本中编写命令。您可以根据需要传递参数。请随意写下您的反馈，让我知道您创建了哪些令人敬畏的自定义命令。

* * *

*这篇文章最初发表于[shobhitpuri.com](http://www.shobhitpuri.com)。*