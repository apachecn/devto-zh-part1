# 填充 GCE 持久性磁盘

> 原文：<https://dev.to/dmfay/populating-gce-persistent-disks-434>

这是我没想到会这么复杂的事情之一，也找不到一步一步的指导。我只想在云端分配一个磁盘，然后在上面放些东西，对吗？这个想法是，然后我启动一个集群，我的高级应用服务器容器启动脚本将静态数据从磁盘中取出，并执行任何需要的初始化。听起来不太复杂。

但到目前为止，谷歌云引擎控制台中还没有“上传一些东西”按钮，而且`gcloud compute scp`只适用于虚拟机实例。没有什么东西会把你的数据放在一个永久性的磁盘上。所以，你必须创建一个:

1.  在计算引擎屏幕中启动新的虚拟机实例。你需要成为这个项目的`iam.serviceAccountUser`。
2.  运行后，登录:

    `gcloud compute --project "my-project" ssh --zone "us-central1-b" "my-new-instance"`

3.  `lsblk`照常显示设备。找到你的。现在格式化它:

4.  `sudo mkfs.ext4 -m 0 -F -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdwhatever`

5.  创建一个挂载点和`sudo mount -o discard,defaults /dev/sdwhatever /mnt/my-mount-point`。

6.  添加你的文件，然后清理你自己和`umount`的挂载点。

从“虚拟机实例”页面中删除实例。GCE 持久性磁盘不能以读/写模式连接到多个实例或容器，如果您要将它添加到具有多个副本的部署中，也需要注意这一点。