# 从 Git 历史中清除文件

> 原文：<https://dev.to/gerbrandvd/purging-files-from-git-history-2j89>

像我一样，大多数开发人员使用 Git 进行版本控制。即使对于个人项目，拥有可用的文件历史记录也是很好的。在 git 中删除一个文件将导致它从您的工作目录中删除，而该文件仍然存在于历史中。通常这是您想要的，但是有时您可能想要完全删除一个文件，包括它的历史。

有一个命令可以做到这一点，但通常对于 git 来说，它并不完全是*直观的*。要改变历史，您可以使用[过滤器-分支](https://git-scm.com/docs/git-filter-branch)命令。Filter branch 可以提供一个过滤器，这是一个在每次提交时执行的 shell 命令。

*   要从历史记录中清除某个文件，就好像它从未存在过一样，请执行:

`git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch MYFILE public' HEAD`

在这里您可以用一个或多个您想要删除的文件替换 *MYFILE* 。

*   如果要清除整个目录，包括其内容，请添加-r 开关:

`git filter-branch -f --index-filter 'git rm --cached --ignore-unmatch MYDIRECTORY public' HEAD`

如果您签入了一个大文件，或者签入了一个不应该公开的公共存储库中的文件，那就太好了。有关更多信息，请阅读 git 手册或现有的大量教程。例如，git-filter 还可以用来将文件从一个存储库移动到另一个存储库[，同时保留历史](http://gbayer.com/development/moving-files-from-one-git-repository-to-another-preserving-history)。