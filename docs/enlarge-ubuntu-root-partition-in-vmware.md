# 在 vmware 中扩大 ubuntu 根分区

> 原文：<https://dev.to/dawncold/enlarge-ubuntu-root-partition-in-vmware>

我有一个 30Gb 根分区的 ubuntu vmware 客户机，但从生产环境中恢复数据后这还不够，所以我必须扩大它。

*   关闭 ubuntu
*   在 vmware 来宾设置上扩大分区
*   启动 ubuntu，最好用文本模式
*   `sudo fdisk /dev/sda`(我在 ubuntu 只有一个设备，`sda`)
*   用`d`命令删除交换分区和根分区(是，删除所有分区)
*   使用`n`命令创建根分区(主分区)，注意开始选择器必须与旧的根分区相同(默认为好)，结束选择器是您想要的分区大小，例如`+45G`是 45Gb 的分区
*   (可选)如果需要，使用`n`命令创建交换分区，然后使用`t`命令更改交换分区类型，交换类型代码为`82`
*   用`w`写修改
*   重启 ubuntu
*   `sudo resize2fs /dev/sda1`，将`sda1`放大到默认的新大小，例如 45Gb
*   (可选)`sudo mkswp /dev/sda2`如果创建交换分区
*   (可选)更改`/etc/fstab`中的交换分区记录，新的交换分区 uuid 或标签可在`sudo blkid`中找到