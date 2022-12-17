# 如何编译构建 Linux 内核 2.6.xx

> 原文：<https://dev.to/laursencamilla/hi-im-laursen-j-camilla>

在这篇文章中，我想向你展示如何编译 Linux 内核。我将编译 2.6.39.1 版本，这是我写这篇文章时最新的稳定版本。

我使用 Debian GNU/Linux，Linux 2.6.32-5-amd64 和 grub2，但是通常你可以使用任何发行版来编译它。一般来说，编译和使用你自己的 Linux 内核是很容易的，但是如果你想更多地参与进来，并且想操作一些模块的话！会有点难。但是当你使用你自己编译的 Linux 时，它是如此令人兴奋。
以下是说明:

1.  从 Kernel.org 获得最新的 Linux 内核我使用的是 2.6.39.1 版本，这是我写这篇文章时最新的稳定版本。

2.  使用以下命令解压缩归档文件。

$ bzip2-DC Linux-2.6 . XX . tar . bz2 | tar xvf
用上面命令中的 XX 替换自己的版本。
解压后的目录下有 README 文件，可以帮助你定制编译。

1.  现在调用 menuconfig 函数来配置和定制要编译的内核。该命令将显示一个窗口，用于选择和取消选择要编译的不同模块。

$ $ make O =/home/name/build/kernel menuconfig
O =选项确定复制构建文件的目标目录。如果您对此命令使用此选项，您也应该将它用于其他命令。

1.  配置完成后，使用下面的命令创建内核。所有文件都将被复制到/home/name/build/kernel 中

$ $ make O =/home/name/build/kernel

1.  知道使用这个命令来安装模块。

$ $ sudo make O =/home/name/build/kernel modules _ install install
该命令会将以下文件复制到/boot 目录中。

config-2.6 . xx
system . map-2.6 . xx
Vmlinuz-2.6 . xx

1.  知道使用下面的命令来制作一个 Ram 磁盘。

$ $ CD/boot
$ $ mkinitramfs-o initrd . img-2.6 . xx 2.6 . xx
注意:有些发行版中应该使用 mkinitrd-o initrd . img-2.6 . xx 2.6 . xx 命令来制作 ram 磁盘。

1.  最后，如果您使用 grub2，使用下面的命令更新 grub 并将新编译的内核引入 grub。这个命令将自动扫描/boot 目录并更新/boot/grub/grub.cfg。

$$更新-grub2

注意:如果你使用 grub 1.x，使用 menu.lst 来引入新编译的内核。现在重启你的系统。您将看到一个新项目，其中包含编译后的内核版本。在 grub 菜单中。只需选择它并启动您的系统，享受使用您自己编译的 Linux。我在许多软件开发公司工作过，我惊讶地发现大多数程序员和开发人员不知道 linux 的这些字符串。