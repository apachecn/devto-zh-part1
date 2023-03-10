# 我的 git 存储库太大了！

> 原文：<https://dev.to/funkysi1701/my-git-repository-is-too-large-5c29>

今天我克隆了我的一个 git 库，花了很长时间才下载完。看看下载了什么，很容易看出原因。的。git 文件夹的大小超过 500Mb。

我知道这是怎么发生的。这个存储库创建于 2013 年，我把它作为一个垃圾场，用来存放许多与代码相关的东西，这些东西本不应该被提交。

自 2013 年以来，我学到了很多关于编码和 git 的知识，所以 git 中文件的当前版本并不太差。但是 git 保留了每个文件的更改历史，所以像这样的不良做法被保留了下来。

对此我能做些什么？谷歌有什么建议？发现了这篇[博文](http://stevelorek.com/how-to-shrink-a-git-repository.html)。

它给出了列出存储在 git 中的所有大文件的方法以及删除它们的方法。因为我是唯一一个定期提交这个库的人，所以我觉得尝试一下没有问题。

我将在这里总结这些步骤。

```
git clone url 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要所有的远程分支，这样就有一个 bash 脚本来运行

```
#!/bin/bash
for branch in `git branch -a | grep remotes | grep -v HEAD | grep -v master`; do
git branch –track ${branch##*/} $branch
done 
```

Enter fullscreen mode Exit fullscreen mode

另一个 bash 脚本列出了前 10 个大文件

```
#!/bin/bash
#set -x
# Shows you the largest objects in your repo’s pack file.
# Written for osx.
#
# @see http://stubbisms.wordpress.com/2009/07/10/git-script-to-show-largest-pack-objects-and-trim-your-waist-line/
# @author Antony Stubbs
# set the internal field spereator to line break, so that we can iterate easily over the verify-pack output
IFS=$’\n’;
# list all objects including their size, sort by size, take top 10
objects=`git verify-pack -v .git/objects/pack/pack-*.idx | grep -v chain | sort -k3nr | head`
echo “All sizes are in kB. The pack column is the size of the object, compressed, inside the pack file.”
output=“size,pack,SHA,location”
for y in $objects do
# extract the size in bytes
size=$((`echo $y | cut -f 5 -d ‘ ‘`/1024))
# extract the compressed size in bytes
compressedSize=$((`echo $y | cut -f 6 -d ‘ ‘`/1024))
# extract the SHA
sha=`echo $y | cut -f 1 -d ‘ ‘`
# find the objects location in the repository tree
other=`git rev-list –all –objects | grep $sha`
#lineBreak=`echo -e “\n”`
output=“${output}\n${size},${compressedSize},${other}”
done
echo -e $output 
```

Enter fullscreen mode Exit fullscreen mode

运行脚本后，您将看到最大文件的详细信息。我有*。msi 和*。exe 文件。要删除它们，请运行以下命令，其中 filename 是需要删除的文件的路径。

```
git filter-branch –tag-name-filter cat –index-filter ‘git rm -r –cached –ignore-unmatch filename’ –prune-empty -f — –all 
```

Enter fullscreen mode Exit fullscreen mode

要回收磁盘空间，运行以下命令

```
rm -rf .git/refs/original/
git reflog expire –expire=now –all
git gc –prune=now
git gc –aggressive –prune=now 
```

Enter fullscreen mode Exit fullscreen mode

现在将您的更改推回到远程服务器。

```
git push origin –force –all
git push origin –force –tags 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你做一个克隆，它将比以前小得多，你可以更快地回到编码，而不必等待。