# 使用 ghbackup 自动备份 GitHub 存储库

> 原文：<https://dev.to/jorinvo/automating-the-backup-of-github-repositories-using-ghbackup>

我解释了我的设置，在我自己的服务器上保存我的存储库的备份。T3】

作为一名软件开发人员，GitHub 已经成为我工作的核心部分。GitHub 的工作人员做得很好。但是，即使是最好的公司，也有可能在某一天陷入困境。

不要把所有事情都放在一家公司身上，利用 Git 的分布式特性并为紧急情况保留自己的备份是一个好主意。这可以很容易地自动化，所以你再也不用担心了。

我是这样做的:

我决定安装一个小型的[数字海洋服务器](https://m.do.co/c/3a2428eee4cc)来备份我所有的储存库。最新的版本每天晚上都会被拿来，我会在[的空闲时间](https://slack.com/)得到通知。当通知不再出现时，我知道我的备份有问题。

我用 [ghbackup](https://qvl.io/ghbackup) 来做这个。有了 [ghbackup](https://qvl.io/ghbackup) ，我可以一次性备份我有权访问的所有公共和私有存储库，如果项目所有者决定将我从项目中移除，我仍然可以保留存储库的副本。

按照以下步骤创建一个类似的设置，或者根据自己的需要采用它:

我们将使用 [ghbackup](https://qvl.io/ghbackup) 、 [sleepto](https://qvl.io/sleepto) 和 [systemd](https://freedesktop.org/wiki/Software/systemd/) 。

*   首先，你需要一个至少具有**回购**范围的 [GitHub 令牌](https://github.com/settings/tokens)，还需要一个 [Slack Webhook URL](https://slack.com/apps/new/A0F7XDUAZ-incoming-webhooks) 。

*   以下所有步骤都发生在系统级。为了不在每个命令前加上前缀`sudo`，让我们以 *root:*
    的身份登录

```
sudo -s 
```

Enter fullscreen mode Exit fullscreen mode

*   `systemd`已经在大多数 Linux 系统上运行。安装 [ghbackup](https://qvl.io/ghbackup) 和 [sleepto](https://qvl.io/sleepto) 。在这个例子中，我们将可执行文件保存在目录`/opt`中

```
curl -sL https://github.com/qvl/sleepto/releases/download/v1.6/sleepto_1.6_linux_64bit.tar.gz | tar -xzf - -C /opt sleepto
curl -sL https://github.com/qvl/ghbackup/releases/download/v1.8/ghbackup_1.8_linux_64bit.tar.gz | tar -xzf - -C /opt ghbackup 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个包装脚本来运行备份。用您自己的值更新下面的变量:

```
touch /opt/ghbackup-job && chmod +x $_ && echo > $_ '#!/usr/bin/env bash

github_token=""
slack_hook=""
backup_dir="/srv/github-backup"

/opt/ghbackup -secret $github_token $backup_dir \
  && curl -X POST --data-urlencode \
  "payload={\"text\": \"Successful GitHub backup.\"}" \
  $slack_hook
' 
```

Enter fullscreen mode Exit fullscreen mode

*   创建新的单元文件，将创建的`ghbackup-job`作为重复服务运行。我们使用 [sleepto](https://qvl.io/sleepto) 在每天凌晨 1 点触发它**。**

```
touch /etc/systemd/system/ghbackup.service && chmod 644 $_ && echo > $_ '
[Unit]
Description=Github backup
After=network.target

[Service]
ExecStart=/opt/sleepto -hour 1 /opt/ghbackup-job
Restart=always

[Install]
WantedBy=multi-user.target
' 
```

Enter fullscreen mode Exit fullscreen mode

*   现在唯一要做的就是启用这项服务。我们确保它在系统启动时自动启动:

```
systemctl daemon-reload
systemctl start ghbackup
systemctl enable ghbackup 
```

Enter fullscreen mode Exit fullscreen mode

*   该服务现在应该正在运行:

```
systemctl status ghbackup 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该很快就会收到成功备份的通知！

您还可以触发 [sleepto](https://qvl.io/sleepto) 立即运行备份作业:

```
systemctl kill -s ALRM ghbackup 
```

Enter fullscreen mode Exit fullscreen mode

第一次备份完成后，您会在`/srv/github-backup`中找到存储库。

这是我如何设置我的 GitHub 备份。希望这对其他人也有帮助。

如果你遇到任何问题，请告诉我！

*注:本帖原帖发布于 [jorin.me](https://jorin.me/automating-github-backup-with-ghbackup/) 。*