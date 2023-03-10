# 备份 FreeNAS 以支撑 B2

> 原文：<https://dev.to/just_insane/backing-up-freenas-to-backblaze-b2-1ajh>

如果你使用 FreeNAS，很可能是因为你在乎你的数据。数据安全性的一部分是确保数据的可用性。为此，您需要确保备份上述数据。从 FreeNAS 备份数据通常有两种合理的方法。一是本地备份(使用 ZFS 复制)，二是云备份。

在本文中，我们将了解如何设置云备份来备份 B2，这是一种类似于亚马逊 S3 的经济的云备份解决方案。

## 第一步:报名

在此注册一个 Backblaze 账户[。创建帐户后，转到“我的设置”选项卡，在“启用的产品”下，选中 B2 云存储旁边的框。这将使您的帐户能够使用 B2。](https://www.backblaze.com/b2/sign-up.html)

## 第二步:创建一个桶

启用 B2 帐户后，您需要创建一个存储桶(存储文件的位置)。为此，在屏幕左侧，选择 B2 云存储下的“Buckets”。然后，选择创建存储桶。此外，在此页面上，请确保单击“显示帐户 ID 和应用程序密钥”，并记下您的帐户 ID，然后单击“创建应用程序密钥”。另外，请记下这一点，因为您将无法再次看到它，并且在我们稍后设置 rclone 时会用到它。

### 步骤 2a:设置上限和警报

虽然这一步是可选的，但强烈建议您这样做，以便在您的帐户上发生任何您可能没有预料到的费用时得到通知。我把每个部分的上限定为每天 1 美元。这将为您提供 6Tb 的存储空间和大量的 API 调用。

### 第 2b 步:查看你的账户

看看你的 Backblaze 账户，这里有一个很棒的入门指南。

## 第三步:在 FreeNAS 上建立一个 FreeBSD 监狱

1.  登录到你的 FreeNAS 图形用户界面，并前往监狱部分。
2.  点击“添加监狱”。
3.  输入监狱名称。我把我的叫做“B2-备份”。
4.  点击确定，你的监狱将被创建。请注意，这可能需要一点时间。如果需要，你可以关闭对话框，监狱将在后台创建。
5.  点击你的监狱，然后点击左下角的“外壳”按钮。这将为监狱打开一个 shell 会话。
6.  输入`vi /etc./rc.conf`，将`sshd_enable="NO"`改为`sshd_enable="YES"`。这将启用 SSH 到监狱。

！FreeBSD 使用 vim 作为文本编辑器，使用`i`插入文本，`del`删除剩余的行，使用箭头键滚动。按`ESC`键保存并退出，然后按`:we`保存并退出。

！您将需要运行`passwd root`并重启，以便拥有 SSH 访问权限，以及`/etc/ssh/sshd_config`中的`PermitRootLogin yes`。

！！！！此时，如果您更喜欢在 FreeNAS GUI 中使用 shell，您可以切换到 SSH。

1.  使用`pkg install wget`安装`wget`，这将允许您下载 rclone 二进制文件。
2.  下载最新的 rclone 二进制:`cd /tmp && wget https://downloads.rclone.org/rclone-v1.37-freebsd-amd64.zip`。
3.  运行`unzip rclone-v1.37-freebsd-amd64.zip`来提取二进制文件。！！！！撰写本文时，rclone 1.37 版是最新的稳定版本
4.  通过运行`cd rclone-v1.37-freebsd-amd64 && cp ./rclone /usr/bin`将 rclone 可执行文件复制到`/usr/bin`

### 步骤 3a:向监狱添加存储

1.  在 Jail 中创建一个新的文件夹结构，我把我的放在`/mnt/storage`中，在那里你将挂载你的 FreeNAS 数据存储。最好为每个要装载的数据集创建一个文件夹。
2.  在 FreeNAS GUI 中，转到 Jails 选项卡，然后转到 Storage 子选项卡。
3.  单击“添加存储”
4.  选择要添加存储空间的监狱。
5.  选择源数据集。
6.  选择目的地(这将是您在步骤 3a-1 中创建的 jail 中的文件夹结构)。
7.  也可以选择只读。
8.  保持选中“创建目录”。
9.  单击“确定”。
10.  对每个要备份到 B2 的数据集重复步骤 3a-4 至 3a-9。

## 步骤 4:配置 rclone

1.  运行`rclone config`启动 rclone 的配置
2.  按下`n`创建一个新的遥控器(遥控器是 rclone 用来知道将文件拷贝/同步到哪里的)。
3.  输入名字，我选择`b2`。
4.  按下`3`。
5.  从您的 B2 帐户输入您的帐户 ID。
6.  从您的 B2 帐户输入您的应用程序密钥。
7.  将端点留空。
8.  按`y`保存配置。

### 步骤 4a:配置加密

1.  遵循步骤 4 中的步骤 1-3。！请注意，将这个新遥控器命名为不同于以前的遥控器。
2.  按下`5`。
3.  输入您在步骤 4 第 3 步中创建的遥控器的名称，然后输入您的存储桶的名称。比如，在我的情况下。
4.  选择是否要加密文件名，选择`1`不会加密文件名。选择`2`加密文件名。我选择`2`。
5.  选择`y`键入您自己的密码，选择`g`随机生成一个强密码。如果你选择`g`，你会得到一个选项，选择你想要生成多强的密码。
6.  为盐创建一个密码。如果您在上一节中选择了输入自己的密码，建议您这样做。请注意，出于安全考虑，这些密码不应相同。
7.  选择`y`接受配置。

！！注意:默认情况下，rclone 配置文件不加密，应用程序密钥和加密密码以明文形式存储。建议为配置文件设置密码，和/或确保 rclone.conf 文件的安全性。

！！如果您需要从 B2 恢复加密文件，您需要两个密码(如果您设置了两个)，否则您的文件将完全不可恢复。

### 步骤 4b:创建 bash 脚本

在这一节中，我们将研究如何创建用于 cron 的 bash 脚本，以便将本地存储的任何更改备份到 B2。

1.  在`/root`中创建一个新文件，我把我的叫做`rclone-cron.sh`。
2.  复制以下内容:

`!/bin/sh
if pidof -o %PPID -x "rclone-cron.sh"; then
exit 1
fi
echo starting storage sync
rclone copy {/path/to/local/storage} {name of your crypt remote}: -v --log-file={/path/to/log/file} --min-age 15m --copy-links
exit`

让我们稍微分析一下，看看这个脚本实际上做了什么。

`!/bin/sh` -使用`sh`终端运行脚本。

`if pidof -o %PPID -x "rclone-cron.sh"; then` -如果脚本当前正在运行，则:

`exit 1` -当前不运行脚本。如果您的初始备份需要一段时间才能运行，这很好，因为它不会再次尝试运行 rclone。

`fi` -关闭 if 语句。

`echo start storage sync` -打印到克隆开始的终端。

`rclone copy {/path/to/local/storage} {name of your crypt remote}: -v --log-file={/path/to/log/file} --min-age 15m --copy-links` -使用复制参数运行`rclone`(不删除本地删除的文件，或者将`copy`更改为`sync`以在 B2 上保留精确的副本(从 B2 删除本地删除的文件))。使用`-v`标志来表示详细程度。`--log-file={/path/to/log/file}`告诉 rclone 在哪里创建日志文件。`--min-age 15m`告诉 rclone 不要同步不到 15 分钟的文件，这有助于确保复制的文件可能是完整的，而不是半完整的。`--copy-links`告诉 rclone 跟随 slinks。

`exit` -复制完成后退出脚本。

### 步骤 4c:创建 cron 条目

1.  运行`crontab -e`打开 cron 编辑器。
2.  输入`0 1 * * * /root/rclone-cron.sh`——这将每天运行一次我们在 4b 中创建的脚本。

## 第五步:运行脚本！

1.  `chmod +x /root/rclone-cron.sh` -使脚本可执行
2.  `cd /root/ && ./rclone-cron.sh` -运行脚本。！rclone 不在后台运行。建议在 tmux 或类似的环境中运行脚本，或者等待 crontab 运行，因为如果您像我一样有很多数据，初始备份可能需要很长时间。

### 步骤 5a:检查 B2 控制台，看看它是否在工作。

1.  登录到你的 back blaze 帐户，看看你的桶。您应该看到文件正在被复制到 B2。

关于设置 rclone 以在 FreeNAS 上备份到 B2 的指南到此结束。Rclone 可以备份到许多云提供商，如果 Backblaze 不适合你，可以看看不同的提供商。