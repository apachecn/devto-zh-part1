# 如何安装 Ubuntu 软件(。deb)包。

> 原文：<https://dev.to/xeroxism/how-to-install-ubuntu-software-deb-package-from-the-command-line-3gn0>

[![software installation](img/cbea43e1775803efe8ee6b0056c70a72.png)T2】](https://i0.wp.com/fossnaija.com/wp-content/uploads/2017/01/how-to-install-software-in-linux.png?ssl=1)

有几种方法可以在 Ubuntu 中安装软件包或应用程序。常见的方法是通过 Ubuntu 软件中心。这个过程是最简单的。它基本上是点击式的。从 Linux 用户的角度来看，这个过程中涉及的许多复杂问题都是抽象的。

但是有些软件应用在官方 Ubuntu 软件库中是找不到的(也是安装不了的)。还有一些 Ubuntu 平台的应用发行商更愿意选择通过提供可下载的 Ubuntu/Debian 来发行他们的应用。deb)软件包。

这个 deb 包包含成功安装应用程序所需的所有依赖项，可以通过终端使用以下步骤来完成:

打开终端*(快捷键:*_**Ctrl+Alt+T**_*)*，输入命令:

**`sudo dpkg –i`** _ **`/path/to/deb/file`** _

“ *dpkg* ”实用程序是 Ubuntu(和其他基于 debian 发行版)中使用的一个低级打包工具，用于直接安装软件包(在本例中是。deb)。

然后进入；

**T2`sudo apt-get install –f`**

有几种方法可以让你在 Ubuntu 上安装软件应用程序。常见的方法是通过 Ubuntu 软件中心。这个过程是最简单的。它基本上是点击式的。从 Linux 用户的角度来看，这个过程中涉及的许多复杂问题都是抽象的。

但是有些软件应用在官方 Ubuntu 软件库中是找不到的(也是安装不了的)。还有一些 Ubuntu 平台的应用发行商更愿意选择通过提供可下载的 Ubuntu/Debian 来发行他们的应用。deb)软件包。

这个 deb 包包含成功安装应用程序所需的所有依赖项，可以通过终端使用以下步骤来完成:

打开终端*(快捷键:*_**Ctrl+Alt+T**_*)*，输入命令:

**`sudo dpkg –i`** _ **`/path/to/deb/file`** _

“ *dpkg* ”实用程序是 Ubuntu(和其他基于 debian 发行版)中使用的一个低级打包工具，用于直接安装软件包(在本例中是。deb)。

然后最后在命令行上输入；

**T2`sudo apt-get install –f`**

快乐的 Linux！

帖子[如何安装 Ubuntu 软件(。deb)包。](https://fossnaija.com/install-ubuntu-deb-package-command-line/)最早出现在[福斯奈亚](https://fossnaija.com)上。