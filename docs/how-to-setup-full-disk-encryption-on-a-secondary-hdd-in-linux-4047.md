# 如何在 Linux 中的辅助硬盘上设置全磁盘加密

> 原文：<https://dev.to/lobo_tuerto/how-to-setup-full-disk-encryption-on-a-secondary-hdd-in-linux-4047>

假设你得到一个全新的 *2TB/4TB/8TB/XXTB* 硬盘，你想把它作为一个安全的备份设备。

这意味着你要加密你放进去的一切。

所以，假设你已经在你的电脑上安装了这个驱动器；让我们为 **FDE** (全磁盘加密)做准备吧。

# 正确识别驱动器名称

打开终端，输入:

```
sudo fdisk -l 
```

Enter fullscreen mode Exit fullscreen mode

您将看到连接到电脑的储存设备及其分区的列表(如果有)。

你需要确认你刚刚连接的那个。如果您的设备大小不同，这非常容易，因为这可以准确定位您想要使用的驱动器。

* * *

**请确保**您正确识别了该驱动器，因为下面的过程将擦除**驱动器上的所有内容**，**没有恢复的机会**。你已经被警告了！

我没有全新的驱动器，而是一个旧的 2TB 的 T2。

对于我来说，使用`sudo fdisk -l`的这个设备的数据看起来是这样的:

```
Disk /dev/sda: 1.8 TiB, 2000398934016 bytes, 3907029168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes 
```

Enter fullscreen mode Exit fullscreen mode

所以，我的驱动是`/dev/sda`。你的名字可能是`/dev/sdb`、`/dev/sdX`或者其他完全不同的名字。

这取决于你的驱动器的连接接口:是 SATA、IDE 还是 NVMe 驱动器？

小心注意。

* * *

您还可以使用`lsblk`命令查看所有块设备及其分区的列表:

```
lsblk 
```

Enter fullscreen mode Exit fullscreen mode

示例输出:

```
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
sda 8:0 0 1.8T 0 disk 
```

Enter fullscreen mode Exit fullscreen mode

# 向驱动器写入零

出于安全原因，并验证你的驱动器没有突出的问题，首先，建议写零。

为此，我们将使用古老的`dd`命令:

```
sudo dd if=/dev/zero of=/dev/sda bs=100M status=progress conv=fdatasync 
```

Enter fullscreen mode Exit fullscreen mode

完成后，您会看到类似这样的内容:

```
2000368435200 bytes (2.0 TB, 1.8 TiB) copied, 12140 s, 165 MB/s
dd: error writing '/dev/sda': No space left on device
19078+0 records in
19077+0 records out
2000398934016 bytes (2.0 TB, 1.8 TiB) copied, 12259.6 s, 163 MB/s 
```

Enter fullscreen mode Exit fullscreen mode

根据您的驱动器类型，写入速度会有很大差异。

对我来说，它是一个通过 SATA 连接的旧的 2TB 驱动器。

用了 *12259.6 秒* == *204.32 分钟* == *3.4 小时*被 0 填满。

# 创建分区表

我们用`fdisk`开盘吧。

```
sudo fdisk /dev/sda 
```

Enter fullscreen mode Exit fullscreen mode

你会看到这样的东西:

```
Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xed42f188.

Command (m for help): 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到，它自动为我们创建了一个 **DOS** 分区表。

让我们把它改成一个 **GPT** 分区表，如果你想看到可用的选项，输入`m`寻求帮助:

```
Command (m for help): m

Help:

  DOS (MBR)
   a toggle a bootable flag
   b edit nested BSD disklabel
   c toggle the dos compatibility flag

  Generic
   d delete a partition
   F list free unpartitioned space
   l list known partition types
   n add a new partition
   p print the partition table
   t change a partition type
   v verify the partition table
   i print information about a partition

  Misc
   m print this menu
   u change display/entry units
   x extra functionality (experts only)

  Script
   I load disk layout from sfdisk script file
   O dump disk layout to sfdisk script file

  Save & Exit
   w write table to disk and exit
   q quit without saving changes

  Create a new label
   g create a new empty GPT partition table
   G create a new empty SGI (IRIX) partition table
   o create a new empty DOS partition table
   s create a new empty Sun partition table 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到我们需要输入`g`作为 **GPT** 分区。

让我们这样做，然后`w`将分区表写入`/dev/sda`磁盘:

```
Command (m for help): g
Created a new GPT disklabel (GUID: D12345B9-D963-44A4-448812B7...).

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks. 
```

Enter fullscreen mode Exit fullscreen mode

操作发生后，它会自动退出。

重新打开驱动器，输入以下命令:

```
sudo fdisk /dev/sda
n
p
w 
```

Enter fullscreen mode Exit fullscreen mode

1.  **n** = >新分区—接受所有默认值，因此它会占用设备上的所有可用空间
2.  显示分区信息
3.  **w** = >写修改并退出

这是上面命令的输出:

```
Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Command (m for help): n
Partition number (1-128, default 1):
First sector (2048-3907029134, default 2048):
Last sector (2048-3907029134, default 3907029134):

Created a new partition 1 of type 'Linux filesystem' of size 1.8 TiB.

Command (m for help): p
Disk /dev/sda: 1.8 TiB, 2000398934016 bytes, 3907029168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: D12345B9-D963-44A4-448812B7...

Device Start End Sectors Size Type
/dev/sda1 2048 3907029134 3907027087 1.8T Linux filesystem

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks. 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你用`sudo fdisk -l`查看你的驱动器信息，你会看到这样的内容:

```
Disk /dev/sda: 1.8 TiB, 2000398934016 bytes, 3907029168 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disklabel type: gpt
Disk identifier: D12345B9-D963-44A4-448812B7...

Device Start End Sectors Size Type
/dev/sda1 2048 3907029134 3907027087 1.8T Linux filesystem 
```

Enter fullscreen mode Exit fullscreen mode

* * *

好吧！如果不想加密，您可以直接使用该驱动器。

但我猜你是来吃*饼干的，*所以继续读下去吧！

# 加密硬盘

`/dev/sda1`分区的加密时间到了:

```
sudo cryptsetup -v -y luksFormat /dev/sda1 
```

Enter fullscreen mode Exit fullscreen mode

输入密码并确认。

请注意/确保您**没有**忘记这一点，否则您将再也无法访问您的数据。我已经警告过你了——再说一遍，我知道。重要的是*。*

上面命令的输出:

```
WARNING!
========
This will overwrite data on /dev/sda1 irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase for /dev/sda1:
Verify passphrase:
Command successful. 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个新的 ext4 文件系统

让我们解锁分区:

```
sudo cryptsetup luksOpen /dev/sda1 encrypteddrive 
```

Enter fullscreen mode Exit fullscreen mode

> 你可以把`encrypteddrive`改成任何你喜欢的名字。

它会要求你输入密码——你手边就有，不是吗？

* * *

如果一切正常，您将看到驱动器列在`/dev/mapper`处，就像这样:

```
total 0
lrwxrwxrwx 1 root root 7 Jan 25 15:01 encrypteddrive -> ../dm-2 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们在上面创建一个 **ext4** 文件系统:

```
sudo mkfs.ext4 /dev/mapper/encrypteddrive 
```

Enter fullscreen mode Exit fullscreen mode

输出如下:

```
mke2fs 1.43.8 (1-Jan-2018)
Creating filesystem with 488377873 4k blocks and 122101760 inodes
Filesystem UUID: e227bc87-b2e4-44f4-bf8f-240e4d16bcc1
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736...

Allocating group tables: done
Writing inode tables: done
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done 
```

Enter fullscreen mode Exit fullscreen mode

## 挂载分区

创建您将要用来与驱动器进行交互的目录:

```
mkdir ~/mynewdrive
sudo mount /dev/mapper/encrypteddrive ~/mynewdrive/
sudo chown -R $USER:$USER ~/mynewdrive/ 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

一旦您关闭并卸载驱动器，您复制到`~/mynewdrive`的任何内容都将被加密并保持安全。

* * *

这使我们……

## 卸载并保护您的数据

为了干净地关闭并保护您的数据，您可以这样做:

```
cd # make sure you are not inside the drive
sudo umount /dev/mapper/encrypteddrive
sudo cryptsetup luksClose /dev/mapper/encrypteddrive 
```

Enter fullscreen mode Exit fullscreen mode

### 不能卸载

```
umount: /home/yolo/mynewdrive: target is busy. 
```

Enter fullscreen mode Exit fullscreen mode

如果您无法卸载驱动器，请确保您不在其中，无论是在终端上还是在文件浏览器上。

还应该确保没有任何正在运行的操作——比如未完成的文件复制等。

## 重新挂载加密分区

你如何在以后重新安装你的驱动器？

```
sudo cryptsetup luksOpen /dev/sda1 encrypteddrive
sudo mount /dev/mapper/encrypteddrive ~/mynewdrive 
```

Enter fullscreen mode Exit fullscreen mode

完成备份后，不要忘记卸载并关闭它！