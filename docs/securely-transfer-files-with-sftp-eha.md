# 使用 SFTP 安全传输文件

> 原文：<https://dev.to/jrfrazier/securely-transfer-files-with-sftp-eha>

SFTP(安全文件传输协议)与 FTP(文件传输协议)相似，都允许您在本地计算机和远程系统之间传输文件。与 FTP 相比，SFTP 的优势在于 SFTP 包含了宋承宪和 T2。这确保了您的本地机器和远程系统之间的连接将被加密。在大多数情况下，SFTP 应该在 FTP 上使用，因为它增加了一层安全性。

您可以下载许多 GUI 客户端，并使用它们通过 SFTP 传输文件。然而，在本教程中，我将演示 SFTP 在命令行的基本用法。

## 利用 SFTP

**1。通过 sftp 连接到远程服务器**

在这个例子中，我将向您展示如何使用 SFTP 在您的本地机器和远程系统之间建立初始连接。

```
sftp user@<remote system> 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**确保从本地机器上要传输文件的目录运行上述命令是很重要的。否则，每次运行传输或导入文件的命令时，都必须在本地服务器上定义完整路径。

当您连接到远程服务器时，您应该会在您的热感器中看到以下 SFTP 提示:

```
sftp> 
```

Enter fullscreen mode Exit fullscreen mode

上述提示表明您现在已连接到远程服务器，并准备开始传输文件。

在任何时候，如果你需要帮助，你只需在提示符下输入`help`,就会出现一个可用命令的列表，并附有每个命令的简要说明。

例如:

```
sftp> help Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing
lumask umask Set local umask to 'umask'
mkdir path                         Create remote directory
progress                           Toggle display of progress meter
put [-afPpRr] local [remote]       Upload file
pwd Display remote working directory
quit                               Quit sftp
rename oldpath newpath             Rename remote file
rm path                            Delete remote file
rmdir path                         Remove remote directory
symlink oldpath newpath            Symlink remote file
version                            Show SFTP version
!command Execute 'command' in local shell
!                                  Escape to local shell
?                                  Synonym for help 
```

Enter fullscreen mode Exit fullscreen mode

**2。将文件从本地机器传输到远程系统**

在这个例子中，我将演示如何把一个文件和一个目录从你的本地机器转移到一个远程系统。

在开始传输文件之前，最好确保您位于正确的目录中。我们可以在你的终端上输入命令`pwd`来完成这项工作。

例如:

```
sftp> pwd 
```

Enter fullscreen mode Exit fullscreen mode

您的输出应该是您想要将文件转移到的当前工作目录。如果不是，您可以使用`cd`命令切换到合适的目录。

例如:

```
sftp> cd <name or path to directory> 
```

Enter fullscreen mode Exit fullscreen mode

将文件从本地机器传输到远程系统时，您将使用`put`命令。

以下示例演示了如何将文件从本地机器传输到远程系统:

```
sftp> put <local file> 
```

Enter fullscreen mode Exit fullscreen mode

上述命令将从您的本地计算机传输一个文件，并将其放在远程系统上您当前连接到的目录中。

如果您需要将整个目录转移到远程系统，您必须使用`-r`选项，然后输入您想要转移的目录的名称:

例如:

```
sftp> put -r <local directory> 
```

Enter fullscreen mode Exit fullscreen mode

**3。将文件从远程系统导入本地机器**

在这个例子中，我将演示如何将文件或目录从远程系统导入到您的本地机器。

将文件/目录从远程服务器导入本地机器时，您将使用命令`get`。

以下示例演示了如何将文件从远程系统导入到本地机器:

```
sftp> get <remote file> 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果您想将目录及其内容从远程服务器导入本地机器，您可以使用`-r`操作:

例如:

```
sftp> get -r <remote directory> 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部了！我提供的命令应该足以让你使用 SFTP。同样，如果你需要帮助，你可以随时在你的终端键入`help`。此外，如果您想更深入地了解该工具的用法和可用选项，您只需在任何 Unix/Linux 终端中键入`man sftp`即可调出 SFTP 手册。