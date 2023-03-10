# 通过删除悬挂的 docker 卷来回收您的信息节点

> 原文：<https://dev.to/adamkdean/reclaim-your-inodes-by-deleting-dangling-docker-volumes-526j>

*最初发布于 2015 年 9 月 8 日* ( [更多信息](https://dev.to/adamkdean/migrating-to-dev-to-190)

*注:如今，你可以简单地使用`docker volume prune`，但这是为后人张贴的。这是一个在 2015 年引起许多问题的问题，确定管理 inode 使用的方法帮助了包括 Shopify 在内的许多平台，Shopify 将容器化带到了一个新的水平。*

今天早些时候，我遇到了一个奇怪的问题，Docker 构建开始失败，理由是没有空闲空间。当我看的时候，有几十亿字节的空闲空间。我对此做了进一步调查，发现詹金斯喜欢打开大量文件。实际上，新安装一次要 3 万美元。我以为这是我的问题，但不是。其他原因导致系统空间不足。

我深入研究了一下，发现问题出在 inodess 上，inode 是用于表示文件系统对象(如文件或目录)的数据结构。更准确地说，问题是明显缺少可用的 inodes。在可用的 290 万辆中，有 290 万辆*在用。那是百分之百。*

我有一种奇怪的感觉，悬挂的书与此有关，所以我看了看我有多少:

```
$ docker images -qf 'dangling=true' | wc -l
100 
```

Enter fullscreen mode Exit fullscreen mode

这些也是非常大的图像，由多个文件和目录组成。事实上，它已经耗尽了系统中所有可用的索引节点。重复一遍，这是 290 万个索引节点。

我可以通过运行`docker images -qf 'dangling=true' | xargs docker rmi`来清除它，这需要一段时间，但已经清除了。为了防止将来发生这种情况，我已经做了一些日志记录来监视 inode 的使用情况，并创建了一个小脚本供人们在我不在时使用，以防这种情况再次发生。

这里是`can-i-have-my-inodes-back-please.sh`:

```
#!/bin/bash
#
# Are your inodes all tied up?
# Would you like to make them liquid again?
# Run this script for up to instant relief
# (smallprint: maytakeuptotwentyminutesdependingonnumberofdanglingvolumes)

DANGLING_NUM=$(docker images -qf 'dangling=true' | wc -l)

read -p "Are you sure you want to remove $DANGLING_NUM dangling volumes? (Y/N) " prompt

if [[ $prompt == "y" || $prompt == "Y" || $prompt == "yes" || $prompt == "Yes" ]]; then
  docker images -qf 'dangling=true' | xargs docker rmi
else
  exit 0
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果您感兴趣，crontab 每小时运行一次这个脚本，`log-inode.sh`:

```
#!/bin/bash
INODE=$(df -i | sed -n 2p | awk '{ print $5 }')
DATE=$(date +'%m/%d/%Y %H:%M')
echo "$DATE,$INODE" 
```

Enter fullscreen mode Exit fullscreen mode

几周后，我将获取 csv 数据并将其导入 Excel，看看这个 inode 问题有多严重。也许每晚进行一次悬空卷清理会有所帮助。