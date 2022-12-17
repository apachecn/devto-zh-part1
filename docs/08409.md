# 从终端打开回购的 GitHub 项目页面

> 原文：<https://dev.to/shayde/open-the-github-project-page-of-a-repo-from-terminal>

我发现自己经常需要进入我在 GitHub 上参与的项目的主页。我已经把重要的项目做了书签，在谷歌上快速搜索项目名称和“github . com”*应该会把我带到正确的地方，但这可能很耗时，并且需要大量的上下文切换。我决定自动化这个过程，以便不中断我的工作流程。*

 *为此，我编写了一个快速的 **bash 函数**来执行自动化，将它放在我的`.bashrc`文件中，并将其分配给别名`github` :

```
alias github=GitHub

function GitHub()
{
    if [ ! -d .git ] ; 
        then echo "ERROR: This isnt a git directory" && return false; 
    fi git_url=`git config --get remote.origin.url`
    if [[ $git_url != https://github* ]] ;
        then echo "ERROR: Remote origin is invalid" && return false;
    fi url=${git_url%.git}
    open $url
} 
```

Enter fullscreen mode Exit fullscreen mode

通俗地说，这段代码的作用如下:

*   首先，我们通过检查`.git/`目录的存在来查看我们是否在 git 存储库的根目录下。
*   接下来，我们从`git config`中获取`remote.origin.url`属性。这是与项目关联的远程 git repo 的 URL。对于 GitHub 项目，这采取的形式是:`https://github.com/[USERNAME]/[PROJECT_NAME].git`
*   如果`remote.origin.url`不是来自 GitHub，我们就不能保证打开它的能力，并抛出一个错误。
*   最后，我们从 URL 中删除`.git`,并使用 macOS 中的`open`命令在给定的 URL 打开浏览器。

现在，任何时候我在从 GitHub 克隆的项目的基础目录中，我都可以通过运行`github`直接从终端打开它的项目页面。

这是我第一次真正探索将 bash 函数和别名合并到我的工作流中的力量。我现在正在积极地寻找那些我经常执行的可以自动化的任务，并将根据需要尝试为它们创建 bash 函数。

这个代码片段和我为[创建的另一个获取工作目录大小的代码片段](https://github.com/ShaydeNofziger/development_scripts/blob/master/bash/getDirectorySize.sh)都在 GitHub 上，我计划将来也发布我创建的所有代码片段！请随意贡献和分享你自己的！

一些激发讨论的问题:

*   开发人员经常执行哪些可以从自动化中受益的操作？

*   您使用过 bash 函数来自动化您的工作流吗？如果是这样，请随意分享例子，以便其他人可以学习！如果没有，你认为自己将来会使用它们吗？*