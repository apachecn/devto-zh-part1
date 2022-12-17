# 修正流浪者的热重装

> 原文：<https://dev.to/tomcentrate/fixing-hot-reloads-on-vagrant>

我在虚拟化开发环境时遇到的一个常见问题是必须手动重启服务器。

每当我在本地开发时，一切都很快，变化出现在第一次刷新时。但是当我使用虚拟机时，重启服务器的挫败感大大降低了我的工作效率。

## 这个问题

许多热重新加载依赖于检测文件何时在您的目录中发生了变化。通常，我们有一些东西监视目录，并监听变化。

每当一个文件被修改，系统内核就会发出一个通知，让程序知道有什么东西被修改了。在 Linux 系统中，我们有`inotify`，在 OSX，它是`fsevents`。

因为我们使用的是虚拟机，所以我们的程序只监听虚拟机上发出的通知。

## 解决方案 1:转发文件更改事件

一个推荐的解决方案是添加一个流浪者插件，负责将文件事件转发到虚拟机。

目前，我们正在使用[travel-fs notify](https://github.com/adrienkohlbecker/vagrant-fsnotify)来转发事件。

安装插件后，我们。

1.  将 fsnotify:true 添加到我们的同步文件目录中。

```
 config.vm.synced_folder ".", "/vagrant", fsnotify: true 
```

1.  重启机器

2.  在单独的选项卡中开始`vagrant fsnotify`。

```
 vagrant fsnotify 
```

## 解决方案二:轮询

以前的建议是切换到*轮询*，偶尔检测修改日期的变化。在版本 4 之前，这是 Ruby on Rails 的默认检测方法。

在文件事件更新不可用的情况下，这是一种备用方法。您可以检查您的开发服务器的启动系统选项，并查看轮询选项是否存在。

我不建议这样做，因为轮询会占用更多的内存和 CPU，但是如果 inotify 转发不可用:

在这个 [stackoverflow 帖子](http://stackoverflow.com/questions/33681004/webpack-dev-server-reload-doesnt-work-on-virtual-box/33699702)中，他们通过`vagrant rsync-auto`启用了轮询。