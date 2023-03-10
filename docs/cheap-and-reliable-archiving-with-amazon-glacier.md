# 利用 Amazon Glacier 实现廉价可靠的归档

> 原文：<https://dev.to/ggarnier/cheap-and-reliable-archiving-with-amazon-glacier>

最初发布于[古伊列梅·卡尼尔的博客](https://blog.guilhermegarnier.com/2017/07/cheap-and-reliable-archiving-with-amazon-glacier/)。

像 [WannaCry 勒索软件](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack)这样的事件暴露了做备份的重要性，这一点通常被很多人遗忘。

当有人谈到备份我们的个人文件时，我们通常会想到像 [Dropbox](https://www.dropbox.com) 和 [Google Drive](https://drive.google.com) 这样的服务。但是如果你有超过几个 GB 的数据，它们的价格是合理的。还有很多其他的解决方案。大多数都更便宜，但并不总是可靠的——想象一下，如果你将所有的个人数据备份到一个小型的未知服务中，几个月后，该公司破产了。或者一个安全漏洞暴露了你所有的个人数据！当然，这也可能发生在 Dropbox 和 Google Drive 身上，但这种可能性要小得多，因为这是两家大型而严肃的公司。

其中一个替代品是亚马逊冰川。这是一项不太受欢迎的亚马逊数据存档服务。您应该注意到它的工作方式不同于通常的备份解决方案。例如，当你注册 Dropbox 时，你可以在电脑或手机上安装一个应用程序，或者使用网络界面即时访问你的文件并上传新文件。冰川要低得多。它没有网页界面，应用程序，甚至没有命令行工具！只有一个 API，你可以用来检查你的文件，下载或上传。

还有:下载和上传速度非常慢。而要下载一个文件，你首先得要求一个文件检索的工作；下载将在几个小时内可用！！！

这似乎是一个糟糕的服务，为什么要使用它呢？因为非常非常便宜！除了请求的[额外费用](https://aws.amazon.com/glacier/pricing/)外，您每月只需为存储支付每 GB 0.004 美元。即使速度慢且难以使用，这也是亚马逊提供的服务，这让你相信它不会突然消失。

话虽如此，Glacier 并不是一项保存你可能立即需要的数据的服务。但是对于您可能近期不需要访问的内容，它是理想的选择。想想你的家庭照片:当你想访问它们时，你可能不会马上需要它们；你等几个小时都没问题。

Glacier 也是“备份的备份”的一个很好的选择。如果你想对备份神经质(你应该这样！)，您可以在那里存档备份的副本。

## 用法

使用 Glacier 最简单的方法是与第三方客户合作。我喜欢[亚马逊-冰川-cmd-界面](https://github.com/uskudnik/amazon-glacier-cmd-interface)。设置好基本配置后，您可以创建一个保险箱并上传您的文件:

```
$ glacier-cmd mkvault my-disaster-backup
$ glacier-cmd upload my-disaster-backup my-file1 my-file2 ... 
```

Enter fullscreen mode Exit fullscreen mode

要列出保管库中的归档文件:

```
$ glacier-cmd inventory <vaultname> 
```

Enter fullscreen mode Exit fullscreen mode

处理库存检索作业需要几个小时。您可以使用
检查其状态

```
$ glacier-cmd listjobs <vaultname>
+------------------------------------------------------+---------------+--------------+--------------------+--------------------------+------------+
|                      VaultARN                        |    Job ID     | Archive ID   |       Action       |        Initiated         |   Status   |
+------------------------------------------------------+---------------+--------------+--------------------+--------------------------+------------+
| arn:aws:glacier:us-west-2:483413266890:vaults/backup | QYqdvM4k8q... |    None      | InventoryRetrieval | 2017-07-24T15:47:48.310Z | InProgress |
+------------------------------------------------------+---------------+--------------+--------------------+--------------------------+------------+ 
```

Enter fullscreen mode Exit fullscreen mode

当作业状态变为`Succeeded`时，再次运行清点命令检查存档列表。

要下载档案，您首先需要在库存中检查其 id:

```
$ glacier-cmd inventory <vaultname>
Inventory of vault: arn:aws:glacier:us-west-2:483413266890:vaults/backup
Inventory Date: 2017-07-05T11:22:15Z

Content:
+---------------+---------------------+----------------------+------------------+------------+
|  Archive ID   | Archive Description |       Uploaded       | SHA256 tree hash |    Size    |
+---------------+---------------------+----------------------+------------------+------------+
| uFg2FE_guu... | file1.tar.gz        | 2017-03-31T14:29:17Z | b41922e1a2...    | 1342622251 |
| 43Wjk63Dcu... | file2.tar.gz        | 2017-03-31T17:18:28Z | 2346170d22...    | 2347810677 |
+---------------+---------------------+----------------------+------------------+------------+
This vault contains 2 items, total size 2.5 GB. 
```

Enter fullscreen mode Exit fullscreen mode

然后，使用档案 id:
创建档案检索作业

```
$ glacier-cmd getarchive <vaultname> <archive id>
+-----------+---------------+
|   Header  |    Value      |
+-----------+---------------+
|   JobId   | Xa17IAadQG... |
| RequestId | cPcomv_vTf... |
+-----------+---------------+ 
```

Enter fullscreen mode Exit fullscreen mode

当下载可用时(您可以使用`glacier-cmd listjobs <vaultname>`检查其状态)，使用
下载

```
$ glacier-cmd download <vaultname> <archive id> --outfile <filename> 
```

Enter fullscreen mode Exit fullscreen mode