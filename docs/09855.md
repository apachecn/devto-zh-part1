# 在 Mercurial (Hg) Push 上生成 Jekyll 站点

> 原文：<https://dev.to/danieljsummers/generating-a-jekyll-site-on-mercurial-hg-push>

我们最近[将一个在 WordPress 托管多年的博客](https://techblog.djs-consulting.com/2017/tech-blog-v4.html)转移到了 [Jekyll](////jekyllrb.com) ，并在帖子中提到我们计划分享这次迁移的方方面面；这是这些帖子的第一篇。

## 背景

使用基于数据库的解决方案，更新网站内容很容易；当用户通过用户界面更新内容时，会在请求页面或帖子时提供新内容。然而，对于静态站点,“更新”从技术上讲是对生成站点的基础文件的任何改变。不过，通常这是由源代码控制提交和推送到主存储库来标记的。Jekyll 为之开发的产品 GitHub Pages 以此为标志重新生成网站。不过，我们没有使用 GitHub*，我们使用了 [Mercurial](////www.mercurial-scm.org) (Hg)进行源代码控制，主存储库位于不同的服务器上，而不是提供站点服务的服务器。

有几个原因我们不希望使用 GitHub 托管我们的站点，但没有一个与这种工作方式相关。

## 选项

由于需要在每个站点的主存储库收到推送后重新生成站点，我们考虑了几个不同的选项。

1.  当推送发生时，在 Hg 服务器上重新生成站点，然后使用`ssh`和`scp`删除 web 服务器上的旧文件，并将新文件复制到 web 服务器上。
2.  在 Hg 服务器上设置一个沙箱，在每次推送发生时进行更新和重新生成，并在 web 服务器上运行`rsync`来经常检查更新。
3.  当推送发生时，通知 web 服务器，并让它重新生成站点。

第一个选项有可能与 SSH 速率限制(T2)相冲突，而且有可能比选项 3 需要更多的数据传输。第二个选项的优点是在 Hg 服务器上运行一个本地进程，但是会占用我们并不真正需要的磁盘空间；而且，当 Jekyll 重新生成一个站点中的所有页面时，`rsync`很可能会因为每次更新而转移所有数据，这就失去了它的一个好处。第三个选项要求将 Jekyll 安装在 web 服务器上，并使用它进行处理，这可能会占用可用于提供网页服务的周期。

最终，我们决定采用第三种方案。

## 脚本所有的东西

在 Hg 服务器上，在每个站点的主存储库中，我们将以下内容放入`.hg/hgrc` *(以下示例针对该站点)* :

```
[hooks]
incoming = /opt/jobs/notify.tech-blog.sh 
```

Enter fullscreen mode Exit fullscreen mode

…然后，`notify.tech-blog.sh` …

```
#!/bin/bash
ssh user@web.server touch /opt/jobs/jekyll/.tech-blog 
```

Enter fullscreen mode Exit fullscreen mode

这是 Hg 服务器上唯一需要的逻辑。现在，在 web 服务器上，我们需要逻辑来重新生成网站并使其活跃起来。由于我们有多个网站，我们写了一个脚本，有几个变量，所以它可以为其他网站复制。下面是`tech-blog.sh` :

```
##
## DJS Consulting Tech Blog Jekyll Build Script
##
## This will check out, build, and replace a Jekyll-generated site. Just update
## the parts under the "Env" heading for the specific site.
##

## Env
REPO=jekyll.tech-blog
DEST=/path/to/public/techblog
TRIGGER=.tech-blog
## /Env

cd /opt/jobs/jekyll

if [-e $TRIGGER]
then
  rm $TRIGGER

  ## Check out the site and build it
  hg clone ssh://user@hg.server/HgPath/$REPO $REPO
  cd $REPO
  jekyll build

  ## Copy it to the proper directory
  cd _site
  rm -r $DEST/*
  cp -r * $DEST

  ## Clean up
  cd ../..
  rm -r $REPO
fi 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本并不完美；在攻击当前站点之前，它需要检查 Jekyll 构建过程的退出代码(通知一个失败的构建将是一个很好的补充)。然而，Jekyll 在开发和生产上都是相同的，并且只有一个提交者，这对于我们的目的来说很好。

最后，需要运行每个脚本来检查信号量(或者脚本调用的`TRIGGER`)的存在。下面的 cron 定义将每 4 分钟检查一次变化。

```
*/4 * * * * /opt/jobs/jekyll/tech-blog.sh > /dev/null 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

总的来说，我们对结果很满意。服务器间的通信是轻量级的，只需要从每个服务器发起一个`ssh`连接，所以我们不会遇到速率限制。随着工作在目标服务器上完成，目录中没有文件的时间(从`rm -r $DEST/*`到`cp -r * $DEST`完成的时间)非常短；如果通过网络重新填充目录，时间会长得多，如果我们在 web 服务器上添加一个临时区域，时间会更复杂。每个部分都可以单独运行，如果我们已经提交了一个未来日期的帖子，我们可以运行相同的`touch`命令来显示该帖子。

*本帖最初发表于[techblog.djs-consulting.com](https://techblog.djs-consulting.com/2017/generating-a-jekyll-site-on-mercurial-hg-push.html)T3】*