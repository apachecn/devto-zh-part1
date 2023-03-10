# 使用 ibm blue mix 云对象存储的热备份和冷备份

> 原文：<https://dev.to/greyhoundforty/hot-and-cold-backups-using-ibm-bluemix-cloud-object-storage>

## 概述

在这次演练中，我们将了解如何利用`rsnapshot`和`s3cmd`在 IBM Bluemix 云对象存储(S3)中进行本地“热”备份和“冷”备份。如果需要的话,`rsnapshot`包将用于生成主机系统和远程 linux 系统的备份。`s3cmd`实用程序用于将这些备份推送到 IBM Bluemix 云对象存储(S3)。

### 先决条件

*   一台或多台安装了 rsnapshot、rsync 和 s3cmd 的 linux 服务器。
    *   RHEL/CentOS:yum install s3cmd rsnapshot rsync(您可能需要添加 epel 存储库，如所述[http://www . tecmint . com/how-to-enable-epel-repository-for-rhel-CentOS-6-5/](http://www.tecmint.com/how-to-enable-epel-repository-for-rhel-centos-6-5/))
    *   Ubuntu/Debian: apt-get 安装 s3cmd rsnapshot rsync
*   SSH-在您的主备份服务器上生成的密钥。在本指南中称为“备份服务器”
*   服务器防火墙上 SSH 端口打开。Rsnapshot 使用 rsync，而 rsync 又使用 SSH 从远程主机提取备份，因此您需要确保您有适当的端口列入白名单。

### 服务器:

*   Backupserver -运行 rsnapshot 的主服务器。本地和远程备份存储在文件存储装载上，每天推送一次到 COS
*   bck1 和 bck2 -我们还需要备份两台服务器。我们的备份服务器将使用 rsync 提取备份。

### 生成 ssh-key 并复制到远程主机

有关生成服务器公共 ssh-key 以及如何将其复制到远程服务器的指南，请参见我们的 KnowledgeLayer 文章[生成和使用 SSH-key 进行远程主机认证](http://knowledgelayer.softlayer.com/procedure/generating-and-using-ssh-keys-remote-host-authentication)。

## [T1】设置和配置 rsnapshot](#setting-up-and-configuring-rsnapshot)

rsnapshot 实用程序将用于将本地系统和远程主机备份到一个目录中。这将允许我们压缩这些备份，并使用`s3cmd`实用程序将它们发送到云对象存储(S3)。

我们还提供了一个您也可以使用的示例`rsnapshot.conf`文件。示例文件具有以下默认值:

*   备份存储在/backups/目录中。如果目录不存在，Rsnapshot 可以创建该目录。
*   保留方案设置为保留 6 个 alpha 备份、7 个 beta 备份和 4 个 gamma 备份。我们会稍微涉及一下语法，但会更深入。
*   Rsnapshot 将备份/home、/etc 和/usr/local。您需要对此进行调整，以满足您的需求。

[示例 rsnapshot.conf](https://raw.githubusercontent.com/greyhoundforty/COSTooling/master/rsnapshot.conf)

关于 rsnapshot.conf 的一个注意事项 rsnapshot 配置文件在制表符和空格方面非常挑剔。编辑文件时，请始终使用制表符。如果有问题，running `rsnapshot configtest`会向您显示有问题的行。

要使用示例配置文件，请运行以下命令:

```
$ mv /etc/rsnapshot.conf{,.bak}
$ wget -O /etc/rsnapshot.conf https://raw.githubusercontent.com/greyhoundforty/COSTooling/master/rsnapshot.conf 
```

Enter fullscreen mode Exit fullscreen mode

这里的语法分解为所有本地`alpha`备份将转到/backups/alpha。远程服务器 bck1 的 X/localhost 和`alpha`备份将转到/backups/alpha。X/bck1。

```
root@backuptest:~# grep snapshot_root /etc/rsnapshot.conf
snapshot_root   /backups/

backup  /home/      localhost/
backup  /etc/       localhost/
backup  /usr/local/ localhost/
backup  root@10.176.18.15:/var/ bck1/ 
```

Enter fullscreen mode Exit fullscreen mode

通过运行命令`rsnapshot configtest`测试 rsnapshot 配置。您还可以通过传递`-t`标志来进行模拟备份，这将向您展示在运行备份作业时`rsnapshot`实际上会做什么。

```
root@backuptest:~# rsnapshot configtest
Syntax OK

root@backuptest:~# rsnapshot -t alpha
echo 14407 > /var/run/rsnapshot.pid
/bin/rm -rf /backups/alpha.5/
mv /backups/alpha.4/ /backups/alpha.5/
mv /backups/alpha.3/ /backups/alpha.4/
mv /backups/alpha.2/ /backups/alpha.3/
mv /backups/alpha.1/ /backups/alpha.2/
/bin/cp -al /backups/alpha.0 /backups/alpha.1
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    /home/ /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded /etc/ \
    /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    /usr/local/ /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    --rsh=/usr/bin/ssh root@10.176.18.15:/var/ /backups/alpha.0/bck1/
touch /backups/alpha.0/ 
```

Enter fullscreen mode Exit fullscreen mode

### 运行测试备份

如果配置测试和模拟运行没有返回任何错误，请继续运行您的第一个`alpha`备份作业。*注意:*默认情况下，`rsnapshot`命令在运行备份作业时不会产生任何输出。如果你想实时看到它在做什么，传递`-v`旗。

```
root@backuptest:~# rsnapshot -v alpha
echo 25845 > /var/run/rsnapshot.pid
/bin/rm -rf /backups/alpha.5/
mv /backups/alpha.4/ /backups/alpha.5/
mv /backups/alpha.3/ /backups/alpha.4/
mv /backups/alpha.2/ /backups/alpha.3/
mv /backups/alpha.1/ /backups/alpha.2/
/bin/cp -al /backups/alpha.0 /backups/alpha.1
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    /home/ /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded /etc/ \
    /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    /usr/local/ /backups/alpha.0/localhost/
/usr/bin/rsync -a --delete --numeric-ids --relative --delete-excluded \
    --rsh=/usr/bin/ssh root@10.176.18.15:/var/ /backups/alpha.0/bck1/
touch /backups/alpha.0/
rm -f /var/run/rsnapshot.pid

root@backuptest:~# ls -l /backups/alpha.0
total 8
drwxr-xr-x 3 root root 4096 Feb 10 17:01 bck1
drwxr-xr-x 5 root root 4096 Feb  8 00:00 localhost 
```

Enter fullscreen mode Exit fullscreen mode

### 添加其他服务器并调整您的日程

现在您已经测试了 rsnapshot，继续添加额外的服务器到`rsnapshot.conf`并配置您的备份频率。rsnapshot 实用程序使用了术语`alpha, beta, gamma, and delta`，但是您可以将它们理解为:

```
alpha = hourly backups
beta = daily backups 
gamma = weekly backups 
delta = monthly backups 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，rsnapshot 附带以下保留方案。

```
retain  alpha   6
retain  beta    7
retain  gamma   4
#retain delta   3 
```

Enter fullscreen mode Exit fullscreen mode

这意味着您的服务器将保留 6 个每小时备份、7 个每日备份和 4 个每周备份。例如:当`alpha`备份作业第 7 次运行时，最旧的备份被转出并删除，因此它只有 6 个`alpha`备份。rsnapshot 实用程序在 Debian/Ubuntu 上还附带了一个默认的 cron.d 文件。对于 RHEL/Centos，软件包不附带默认的 cron 文件。在这两种情况下，你都需要运行下面的命令

```
$ wget -O /etc/cron.d/rsnapshot https://raw.githubusercontent.com/greyhoundforty/COSTooling/master/rsnapshotcron 
```

Enter fullscreen mode Exit fullscreen mode

```
0 */4   * * *       root    /usr/bin/rsnapshot alpha
0 21    * * *       root    /usr/bin/rsnapshot beta
0  3    * * 1       root    /usr/bin/rsnapshot gamma
# 30 2  1 * *       root    /usr/bin/rsnapshot delta 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，`alpha`作业每 4 小时运行一次，测试版每天晚上 9 点运行，依此类推。这是您想要定制以满足您需求的地方。rsnapshot 完成后，我们现在将继续配置`s3cmd`。

## 配置 s3cmd

python 脚本是一个开源工具，允许*nix 或 osx 机器与 S3 兼容的服务对话。安装好实用程序后，客户所要做的就是下载我们的示例`.s3cfg`文件，并使用他们的 COS 访问和密钥更新它:

```
$ wget -O $HOME/.s3cfg https://raw.githubusercontent.com/greyhoundforty/COSTooling/master/s3cfg 
```

Enter fullscreen mode Exit fullscreen mode

需要更新的 5 行是 2、30、31 和 55(如果使用我们的示例. s3cfg 文件)。对于第 30 行和第 31 行，您需要用您正在使用的云对象存储(s 3)端点替换`cos_endpoint`。一旦所有行都更新了来自客户门户的 COS (S3)详细信息，您就可以通过发出命令`s3cmd ls`来测试连接，该命令将列出帐户上的所有存储桶。

```
$ s3cmd ls                                                                                                                                                  
2017-02-03 14:52  s3://backuptest
2017-02-03 21:23  s3://largebackup
2017-02-07 20:49  s3://po9bmbnem531ehdreyfh-winbackup
2017-02-07 17:44  s3://winbackup 
```

Enter fullscreen mode Exit fullscreen mode

### 创建我们的备份桶

为了创建存储备份的存储桶，我们将使用`s3cmd mb`命令。

**关于存储桶的一些注意事项** -云对象存储(S3)每个帐户有 100 个存储桶的限制。如果您将每个备份设置为创建自己的存储桶或每月存储桶，请记住这一点。存储桶名称必须符合 DNS。名称长度必须在 3 到 63 个字符之间，必须由小写字母、数字和破折号组成，必须是全局唯一的，并且不能显示为 IP 地址。确保唯一性的一种常见方法是在存储桶名称后面附加一个 UUID 或其他独特的后缀。

```
$ s3cmd mb s3://coldbackups/
Bucket 's3://coldbackups/' created 
```

Enter fullscreen mode Exit fullscreen mode

### 将备份推送到云对象存储(S3)

要手动将备份推送到云对象存储(S3)，我建议使用 tar 压缩备份目录，并附上日期戳，以便在需要从云对象存储(S3)提取备份进行恢复时更容易排序。

```
tar -czf $(date "+%F").backup.tar.gz /backups/
s3cmd put $(date "+%F").backup.tar.gz s3://coldbackups/ 
```

Enter fullscreen mode Exit fullscreen mode

为了自动化这个过程，您可能希望使用 cron 作业来压缩备份，并定期将它们发送到云对象存储(S3)。

**关于保留的说明** -云对象存储(S3)目前不支持保留方案。这意味着你推送到 COS (S3)的任何内容都将保留在那里，直到你删除它。

## 从云对象存储中恢复文件(S3)

要从云对象存储(S3)恢复文件或目录，您需要使用`get`命令来下拉备份。一旦文件或目录被下载到您的服务器，您可以使用`cp`、`rsync`或`mv`来恢复文件。如果文件来自使用`rsnapshot`备份的远程主机，您可以使用`scp`或`rsync`将其移回原始主机系统。

### 拉单个文件或压缩备份

默认情况下，您从云对象存储(S3)下载的文件将存储在您当前所在的目录中。

```
$ sc3md get s3://bucket/path/to/file 
```

Enter fullscreen mode Exit fullscreen mode

您也可以指定下载文件的存储目录:

```
$ s3cmd get s3://bucket/path/to/file /local/path/ 
```

Enter fullscreen mode Exit fullscreen mode

### 拉一个目录

为了拉一个目录以及所有的子项，使用`--recursive`标志

```
$ s3cmd get s3://bucket/ /local/path/ --recursive 
```

Enter fullscreen mode Exit fullscreen mode

## 自动化流程

我创建了一个 bash 脚本，可以用来设置`rsnapshot`和`s3cmd`。这个脚本是按原样提供的，您可以随意进行任何修改。 [backup_script.sh](https://github.com/greyhoundforty/COSTooling/blob/master/backup_script.sh)