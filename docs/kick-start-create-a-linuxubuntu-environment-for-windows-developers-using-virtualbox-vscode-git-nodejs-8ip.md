# 启动:使用 VirtualBox、VSCode、Git、NodeJS 为 Windows 开发人员创建一个 Linux(Ubuntu)环境

> 原文：<https://dev.to/bardev/kick-start-create-a-linuxubuntu-environment-for-windows-developers-using-virtualbox-vscode-git-nodejs-8ip>

我喜欢使用 Visual Studio 代码(VSCode)。由于它适用于 Linux 和 Mac，我想我应该在不同的环境中尝试一下。作为一名 Windows 开发人员，我想尝试一下 Linux。在过去，当我尝试使用 Linux 时，我被所有的工具、配置和其他必须学会的东西淹没了。

我很难找到当前关于设置 Linux 开发环境的初学者信息。我找到了一些零碎的东西，但没有任何东西能从头到尾展示如何开始。

在这篇文章中，我将描述如何在 Linux (Ubuntu)中设置一个开发环境。我将使用的主要技术如下:

*   Oracle virtual box-5 . 1 . 14 版
*   Linux Ubuntu 64 位:16.04
*   Visual Studio 代码(VSCode): 1.9.1
*   节点编号:6.9.5
*   Git: 2.7.4

这篇文章是关于建立一个基本的 Linux (Ubuntu)开发环境。我假设用户熟悉 Windows，并对管理虚拟机的虚拟机管理程序软件有非常基本的了解。

我使用 Windows 10 作为我的主机系统。我不认为你使用什么主机有什么关系，因为除了 VirtualBox，这篇文章中的所有东西都将安装在 Linux Ubuntu 上。

## 安装 VirtualBox

我们需要设法运行 Ubuntu。为此，我们需要一个虚拟机管理程序。虚拟机管理程序是运行虚拟机的软件。有许多可用的虚拟机管理程序(Hyper-V、VMWare、VirtualBox 等)。我选择 Oracle VirtualBox 的原因如下:

*   在 Windows 上运行
*   免费供个人使用
*   使用方便
*   非常受欢迎
*   大型社区

让我们通过访问 VirtualBox 网页来安装 VirtualBox:

[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)

<figure>[![VirtualBaox Download](img/ee9290ef50437b03c4e7492affaa30da.png "VirtualBaox Download")](http://www.bardev.com/wp-content/uploads/2017/02/image.png)

<figcaption>virtualbox 下载</figcaption>

</figure>

在 VirtualBox 下载网页上，下载 VirtualBox

下载 VirtualBox 后，安装它。

<figure>[![VirtualBaox Install](img/4cfc7be00f79ac561a68a371963178d6.png "VirtualBaox Install")](http://www.bardev.com/wp-content/uploads/2017/02/image-1.png)

<figcaption>virtualbox 安装</figcaption>

</figure>

## 下载 Linux Ubuntu 桌面

当我们使用 VirtualBox 创建虚拟机时，我们需要安装 Ubuntu 的镜像(iso)。我们会从 Ubuntu 的网站上得到这个 iso。下载桌面版的 Ubuntu 很重要。从这里下载 Ubuntu 桌面:

[https://www.ubuntu.com/download/desktop](https://www.ubuntu.com/download/desktop)

<figure>[![Ubuntu Desktop Download](img/7d5c8109af9aa808175da5f246627d80.png "Ubuntu Desktop Download")](http://www.bardev.com/wp-content/uploads/2017/02/image-2.png) 

<figcaption>Ubuntu 桌面下载</figcaption>

</figure>

**<u>注:</u>** 我们将运行 Ubuntu 64 位。如果你运行的是 Windows 10，安装 64 位版本的 Ubuntu 可能会有问题。我不确定 Windows 10 上是否默认安装了 Hyper-V，但如果安装了，如果要运行 64 位虚拟机，就需要卸载。

**注:**在调查 Docker for Windows 的时候，我偶然发现了这个

“Docker for Windows 需要微软 Hyper-V 才能运行。启用 Hyper-V 后，VirtualBox 将不再工作，但任何 VirtualBox 虚拟机映像将会保留。

<figure>[![Docker for Window and Hyper-V](img/312c687a88da076ac35d13f20585bfec.png "Docker for Window and Hyper-V")](https://res.cloudinary.com/practicaldev/image/fetch/s--XjdH2Oiu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.bardev.com/wp-content/uploads/2017/02/Docker-for-Window-and-Hyper-V.png)

<figcaption>[https://docs . docker . com/docker-for-windows/install/#/what-to-know-before-you-install](https://docs.docker.com/docker-for-windows/install/#/what-to-know-before-you-install)</figcaption>

</figure>

若要卸载 Hyper-V，请打开“打开或关闭 Windows 功能”。你可以通过进入开始菜单并输入“Turn”来完成

<figure>[![Turn Windows features on or off for Hyper-v](img/391106197b1ce091aee28641d5911fd7.png "Turn Windows features on or off for Hyper-v")](http://www.bardev.com/wp-content/uploads/2017/02/image-3.png) 

<figcaption>打开或关闭 Hyper-v 的 Windows 功能</figcaption>

</figure>

现在取消选中 Hyper-V，点击“确定”。这将需要几次重新启动。

<figure>[![Turn off Hyper-v in Windows](img/b83f849ddff81b7d29e8bedc9125fc73.png "Turn off Hyper-v")](http://www.bardev.com/wp-content/uploads/2017/02/image-4.png) 

<figcaption>关闭 Windows 中的 Hyper-v</figcaption>

</figure>

## 安装 Ubuntu 虚拟机

现在让我们开始有趣的部分。让我们把 Ubuntu 作为虚拟机安装在 VirtualBox 上。启动 VirtualBox。一旦 VirtualBox 启动，您应该会看到类似下图的内容。

<figure>[![Oracle VM VirtualBox Manager](img/e61c6e5decd371cdaf1be8c0009606c1.png "Oracle VM VirtualBox Manager")](http://www.bardev.com/wp-content/uploads/2017/02/SNAGHTML43b9101.png) 

<figcaption>Oracle 虚拟机管理器</figcaption>

</figure>

要创建虚拟机，请选择“新建”按钮。您应该会看到一个创建新虚拟机的表单(见下图)。在表单中输入一个名称。我用的名字是“UbuntuDemo”。一旦你输入“Ubuntu ”,注意“类型”和“版本”会被自动选中。另外，请注意版本是“64 位”。如果您没有看到“64 位”，请参考上面关于 Windows 10 和 Hyper-V 的说明。

**注意:**这里，我们没有在虚拟机上安装 Ubuntu。这个以后再做。这里只是配置和设置虚拟机。

**注意:** Ubuntu 最低和推荐的系统要求

<figure>[![Ubuntu Desktop 16 Minimum System Requirements](img/efc10bd776a4c7729d5bf627ce7c5b74.png "Ubuntu Desktop 16 Minimum System Requirements")](https://res.cloudinary.com/practicaldev/image/fetch/s--HlAA3MxM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.bardev.com/wp-content/uploads/2017/02/Ubuntu-Desktop-16-Minimum-System-Requirements.png)

<figcaption>[https://help . Ubuntu . com/community/Installation/system 需求](https://help.ubuntu.com/community/Installation/SystemRequirements)</figcaption>

</figure>

<figure>[![Ubuntu Desktop 16 Recommend System Requirements](img/66b52c77b1968074391a515e6bd5531d.png "Ubuntu Desktop 16 Recommend System Requirements")](https://res.cloudinary.com/practicaldev/image/fetch/s--UAZN-N6_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.bardev.com/wp-content/uploads/2017/02/Ubuntu-Desktop-16-Recommend-System-Requirements.png)

<figcaption></figcaption>

</figure>

Ubuntu 将在 2 GB 的内存上运行，但这是推荐的要求。我继续将这个 Ubuntu 虚拟机的内存设置为 4 GB。

将硬盘选项设置为“立即创建虚拟硬盘”，然后选择“创建”按钮。

<figure>[![Create Virtual Machine](img/b8176367369b5337572ee61f58d269ca.png "Create Virtual Machine")](http://www.bardev.com/wp-content/uploads/2017/02/image-5.png) 

<figcaption>创建虚拟机</figcaption>

</figure>

现在让我们创建硬盘。在下一个表单中，除了“文件大小”选项之外，您可以保留所有的默认值。Ubuntu 建议磁盘大小为 25 GB。为了安全，也为了能在 Ubuntu 虚拟机上安装额外的软件，我将文件大小设置为 30 GB。

我将其他一切都保留为默认值。现在选择“创建”按钮。

<figure>[![ Create Virtual Hard Disk](img/ef49a2208c2283364caabf33c389b02d.png "Create Virtual Hard Disk")](http://www.bardev.com/wp-content/uploads/2017/02/image-6.png) 

<figcaption>创建虚拟硬盘</figcaption>

</figure>

**更新:**我刚刚查看了 VirtualBox 虚拟媒体管理器中 Ubuntu 使用的实际磁盘空间大小。只有 4.86 GB。

[![VirtualBox Virtual Media Manager](img/afe7f80cf25380d2009beb282b38b5fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--offbVE5b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://www.bardev.com/wp-content/uploads/2017/02/VirtualBox-Virtual-Media-Manager-300x246.png)

## 配置 Ubuntu 虚拟机

在这里，我们将对虚拟机配置进行一些基本更改。我们将改变虚拟机使用的处理器数量，并在我们的 Windows 10 主机和 Ubuntu 虚拟机之间共享剪贴板。

**<u>注意:</u>** 要使这些更改生效，需要停止虚拟机，或者如果虚拟机正在运行，则需要重新启动。

**<u>注意:</u>** 在下面的 Oracle VM VirtualBox Manager 的映像中，有两个虚拟机。请忽略第一个“ubuntu”虚拟机。我们将创建“UbuntuDemo”虚拟机。

要更改虚拟机设置，从虚拟框中选择“UbuntuDemo”，然后选择“设置”按钮。

<figure>[![ Configure Oracle VM VirtualBox Managaer Settings](img/fe8e14f47692d28bb5ce35c6550f7ae4.png "Configure Oracle VM VirtualBox Managaer Settings")](http://www.bardev.com/wp-content/uploads/2017/02/image-7.png) 

<figcaption>配置 Oracle VM VirtualBox 管理器设置</figcaption>

</figure>

要启用剪贴板共享功能，请从左侧导航栏中选择“常规”。然后选择“高级”选项卡。现在将“共享剪贴板”和“拖放”的设置改为双向。

当你想从你的主机复制和粘贴文本到虚拟机或 visa-verse 时，这个特性就很方便了。

<figure>[![Configure Oracle VM VirtualBox Manager Settings Shared Clipboard & Drag n' Drop](img/2e3fde116b9dbf1d9fa1f1f76c396150.png "Configure Oracle VM VirtualBox Manager Settings Shared Clipboard & Drag n' Drop")](http://www.bardev.com/wp-content/uploads/2017/02/image-8.png) 

<figcaption>配置 Oracle VM VirtualBox 管理器设置共享剪贴板&拖放</figcaption>

</figure>

默认情况下，Ubuntu 虚拟机被配置为只使用一个处理器。在我的电脑上，只有一个处理器的 Ubuntu VM 运行起来有点慢，所以我把处理器增加到 3 个。根据您的计算机，您可能需要更改此设置。

要更改处理器数量，请在设置对话框中选择左侧导航栏中的“系统”。然后选择“处理器”选项卡。此时，您可以更改虚拟机将使用的处理器数量

<figure>[![Configure Oracle VM VirtualBox Manager Settings Processor Count](img/45820906bc57f30d3fdeb75bade944ca.png "Configure Oracle VM VirtualBox Manager Settings Processor Count")](http://www.bardev.com/wp-content/uploads/2017/02/image-9.png) 

<figcaption>配置 Oracle VM VirtualBox Manager 设置处理器数量</figcaption>

</figure>

## 安装 Ubuntu

现在我们已经配置了虚拟机，让我们启动 VM 并安装 Ubuntu。

要启动虚拟机，在 VirtualBox 管理器中选择“UbuntuDemo ”,然后选择“开始”按钮。

<figure>[![Start Ubuntu Virtual Machine from Oracle VM VirtualBox Manager](img/70cd6acfd7fe640cc5aa5aa6e334f5f4.png "Start Ubuntu Virtual Machine from Oracle VM VirtualBox Manager")](http://www.bardev.com/wp-content/uploads/2017/02/image-10.png) 

<figcaption>从 Oracle VM VirtualBox Manager 启动 Ubuntu 虚拟机</figcaption>

</figure>

虚拟机将在新窗口中启动。您将看到的第一个屏幕是一个选择启动盘的对话框。这是我们在虚拟机上安装 Ubuntu 的地方。我们需要告诉虚拟机 Ubuntu 桌面安装文件的位置。

在“选择启动盘”对话框中，选择右下角的小文件夹图标。将显示另一个对话框(未示出)来选择 Ubunto。要安装的 iso 文件。我的 iso 文件叫“Ubuntu-16 . 04 . 2-desktop-amd64 . iso”；您的文件名称可能略有不同。

一旦你找到了 Ubuntu。iso 文件，选择“开始”按钮。

<figure>[![Select Ubuntu file image to install on Virtual Machine](img/b90b93aa71e70b388beed5aad9a73a42.png "Select Ubuntu file image to install on Virtual Machine")](http://www.bardev.com/wp-content/uploads/2017/02/image-11.png) 

<figcaption>选择 Ubuntu 文件镜像安装在虚拟机上</figcaption>

</figure>

点击“开始”后，应该会提示你安装 Ubuntu。选择“安装 Ubuntu”按钮。

<figure>[![Setup Ubuntu Welcome](img/32b77fe1fa733aef42c706a73d36ecb5.png "Setup Ubuntu Welcome "Install Ubuntu"")](http://www.bardev.com/wp-content/uploads/2017/02/image-12.png) 

<figcaption>安装 Ubuntu 欢迎使用【安装 Ubuntu</figcaption>

</figure>

下面的对话相当简单。我不会一步一步地教你接下来的几个对话框，但是为了你的方便，我已经包括了这些对话框的截图。

<figure>[![ Ubuntu setup and Install - Preparing to install Ubuntu](img/3d306225a4b80d6bb297d66260eefdfb.png "Ubuntu setup and Install - Preparing to install Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-13.png) 

<figcaption>Ubuntu 设置和安装——准备安装 Ubuntu</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Installation Type](img/dda0bda420cfc2ed834a1443d9cd8596.png "Ubuntu setup and Install - Installation Type")](http://www.bardev.com/wp-content/uploads/2017/02/image-14.png) 

<figcaption>Ubuntu 设置和安装——安装类型</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Installation Type confirmation](img/e06701be2b4404d266a3a63632d72bc3.png "Ubuntu setup and Install - Installation Type confirmation")](http://www.bardev.com/wp-content/uploads/2017/02/image-15.png) 

<figcaption>Ubuntu 设置和安装——安装类型确认</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Where are you](img/aef4aa6c0505130fd78ea6644e12e3c4.png "Ubuntu setup and Install - Where are you")](http://www.bardev.com/wp-content/uploads/2017/02/image-16.png) 

<figcaption>Ubuntu 设置安装——你在哪</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Keyboard Layout](img/2afa900b494a01e9a042e9bda1d73b0a.png "Ubuntu setup and Install - Keyboard Layout")](http://www.bardev.com/wp-content/uploads/2017/02/image-17.png) 

<figcaption>Ubuntu 设置和安装——键盘布局</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Who Are you](img/5078512459a3779fcecc9cb9dcf142a5.png "Ubuntu setup and Install - Who Are you")](http://www.bardev.com/wp-content/uploads/2017/02/image-18.png) 

<figcaption>Ubuntu 设置和安装——你是谁</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Welcome to Ubuntu](img/e40ccea3733444980af5334daef919b5.png "Ubuntu setup and Install - Welcome to Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-19.png) 

<figcaption>Ubuntu 设置和安装——欢迎使用 Ubuntu</figcaption>

</figure>

<figure>[![Ubuntu setup and Install - Installation Complete - Restart](img/ea9d3529de820e1dc54f1b5b4c5b9f5a.png "Ubuntu setup and Install - Installation Complete - Restart")](http://www.bardev.com/wp-content/uploads/2017/02/image-20.png) 

<figcaption>Ubuntu 设置和安装——安装完成——重启</figcaption>

</figure>

选择“立即重新启动”后，您可能会收到一条消息“请取出安装介质…”。如果你收到这条信息，这意味着。iso 文件仍连接到虚拟机光驱。如果你不卸载 Ubuntu 就继续。iso 文件，您将再次经历安装过程。

<figure>[![VirtualBox Ubuntu. Please remove installation medium, then press ENTER](img/653b834aa836bad63743951d36835b14.png "VirtualBox Ubuntu. Please remove installation medium, then press ENTER")](http://www.bardev.com/wp-content/uploads/2017/02/image-21.png)

<figcaption>VirtualBox Ubuntu。请取出安装介质，然后按 ENTER</figcaption>

键</figure>

分离(删除)Ubuntu。iso 从虚拟机，在虚拟机窗口上选择“设备”—>“光驱”—>“从虚拟驱动器中移除磁盘”。现在按键盘上的“回车”键。现在 Ubuntu 就要启动了。

<figure>[![How to remove installation medium from VirtualBox](img/75c7b97534e5f28b0e228437eb80338c.png "How to remove installation medium from VirtualBox")](http://www.bardev.com/wp-content/uploads/2017/02/image-22.png) 

<figcaption>如何从 VirtualBox 中移除安装介质</figcaption>

</figure>

一旦 Ubuntu 启动，你应该会看到如下屏幕。Ubuntu 现已成功安装。

<figure>[![Ubuntu installation complete first screen](img/38c492ca693bf7205e44fb20668930d6.png "Ubuntu installation complete first screen")](http://www.bardev.com/wp-content/uploads/2017/02/image-23.png) 

<figcaption>Ubuntu 安装完成首屏</figcaption>

</figure>

**注意:**从 Ubuntu 关闭 Ubuntu，桌面右上角是当前时间、杂项按钮，还有一个电源按钮。选择电源按钮将显示一个菜单，其中有一个选项是“关机”。

<figure>[![Ubuntu Power Button](img/722aa779f8afab214bdb506646eae2d2.png "Ubuntu Power Button")](http://www.bardev.com/wp-content/uploads/2017/02/image-24.png) 

<figcaption>Ubuntu 电源按钮</figcaption>

</figure>

<figure>[![Ubuntu shut down](img/bef6a9103a21c82b3cd484aaca64ec78.png "Ubuntu shut down")](http://www.bardev.com/wp-content/uploads/2017/02/image-25.png) 

<figcaption>Ubuntu 关闭</figcaption>

</figure>

## 安装谷歌浏览器

我安装 Ubuntu 后做的第一件事就是安装谷歌 Chrome 浏览器。Firefox 默认安装在 Ubuntu 上，但如果你愿意，你可以安装另一个浏览器。通过安装谷歌浏览器，我展示了一些在 Ubuntu 上安装软件时会出现的对话框。

谷歌 Chrome 桌面下载

[https://www.google.com/chrome/browser/desktop/](https://www.google.com/chrome/browser/desktop/)

<figure>[![Download Google Chrome](img/5e57f0df0e1f794abb0ddc2e1d24b510.png "Download Google Chrome")](http://www.bardev.com/wp-content/uploads/2017/02/image-26.png) 

<figcaption>下载谷歌 Chrome</figcaption>

</figure>

一旦您选择下载，您将被提示选择您正在运行的 Linux 类型。我们运行的是 Debian 的衍生产品。因此，请选择包含“”的选项。黛比"

<figure>[![Download Google Chrome for Linux - 64 bit .deb for Debian and Ubuntu](img/416c106674f56dbd2afe1ab4a3231ba6.png "Download Google Chrome for Linux - 64 bit .deb for Debian and Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-27.png) 

<figcaption>下载谷歌浏览器 Linux 版–64 位。deb for Debian 和 Ubuntu</figcaption>

</figure>

选择“接受并安装”后，系统会提示您如何处理该文件。我通常只是打开文件，但如果你想，你可以保存文件。

由于我不希望再次安装谷歌浏览器，我将选择“打开方式”选项。

<figure>[![Chrome Download options](img/a4e24cb1a6f03751f4c119589489fb2e.png "Chrome Download options on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-28.png) 

<figcaption>Chrome 下载选项</figcaption>

</figure>

安装软件时，Ubuntu 会提示您确认是否信任正在安装的软件。如果您信任正在安装的软件，请选择“安装”按钮。

<figure>[![ Confirm installing software](img/0c1189f77ab97654f060d255742d75ce.png "Confirm installing software on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-29.png) 

<figcaption>确认安装软件</figcaption>

</figure>

现在，在认证对话框中输入您的密码“demo ”,继续安装您的软件。您的密码可能不同。

<figure>[![Authenticate installing software](img/abb1c11a1ac2a669c0d5ebf2a9a77196.png "Authenticate installing software on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-30.png) 

<figcaption>认证安装软件</figcaption>

</figure>

## 运行应用程序——谷歌浏览器

我想安装 Visual Studio 代码(VSCode)。但要做到这一点，我需要先用 Chrome 下载它。有几种方法可以启动应用程序。我将向您展示我启动不在应用程序栏上的程序的主要方式。

在桌面的左上角，有一个紫色的图标(见下图)。此图标打开“Dash”。Dash 提供了搜索应用程序、文件、音乐等的能力。因为我们要启动 chrome，所以选择“破折号”图标并在文本框中键入“Chrome”。将显示一个 Chrome 图标。选择 Chrome 图标启动 Chrome。

<u>**注意:**</u> 使用键盘上的 win 键可以打开“破折号”搜索提示。

**<u>注意:</u>** 显示的图标可以拖放到应用工具栏。

<figure>[![Run Chrome Application with Ubuntu Dash](img/9c38352ba3e8f5f19358a93e35d64d97.png "Run Chrome Application with Ubuntu Dash")](http://www.bardev.com/wp-content/uploads/2017/02/image-31.png) 

<figcaption>用 Ubuntu Dash</figcaption>

</figure>

运行 Chrome 应用

## 安装 Visual Studio 代码(VSCode)

现在让我们安装 Visual Studio 代码。但首先，我们需要下载它。

在 Chrome 或选择的浏览器中，导航至:

[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

选择下载。VSCode 的 deb (Debian)版本

<figure>[![Download Visual Studio Code (VSCode) for Ubuntu .dib](img/a44b770d615d27f482fe2b5c2ae82cc5.png "Download Visual Studio Code (VSCode) for Ubuntu .dib")](http://www.bardev.com/wp-content/uploads/2017/02/image-32.png) 

<figcaption>为 Ubuntu 下载 Visual Studio 代码(VSCode)。dib</figcaption>

</figure>

VSCode 的安装过程和我们上面做的安装 Chrome 是一样的。我将继续提供安装过程的图像。

<figure>[![Ubuntu Install Application](img/1a9a2b9a0a8039d9b0110a2b29e1a92f.png "Ubuntu Install Application")](http://www.bardev.com/wp-content/uploads/2017/02/image-33.png) 

<figcaption>Ubuntu 安装应用</figcaption>

</figure>

<figure>[![Authenticate installation with password](img/3567adf9e72eb65556fd51b900aece73.png "Authenticate installation with password on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-34.png) 

<figcaption>用密码</figcaption>

</figure>

认证安装

现在安装了 VSCode，我们可以运行它了。运行 VSCode 的过程与运行任何其他应用程序的过程相同。打开“破折号”搜索，在文本框中输入“代码”。

<figure>[![Run Visual Studio Code (VSCode) in Ubuntu using Dash](img/23e9266a496a6f685f243eedc838cdbb.png "Run Visual Studio Code (VSCode) in Ubuntu using Dash")](http://www.bardev.com/wp-content/uploads/2017/02/image-35.png) 

<figcaption>使用破折号</figcaption>

</figure>

在 Ubuntu 中运行 Visual Studio 代码(VSCode)

现在，VSCode 将启动。

## 安装 Node.js

Node.js 这几天很流行。我在大部分 web 开发中使用 Node。既然这是常用工具，那就装吧。

我们将使用类似于 Windows 命令提示符或 PowerShell 的工具来安装 Node，而不是从网站下载和安装。在 Ubuntu 中，这个工具叫做终端。

启动应用程序终端。打开“破折号”搜索，在文本框中输入终端。您可能会得到多个结果，但请选择名为“终端”的应用程序。

<figure>[![Ubuntu terminal using Dash](img/6cbefa37c18af551492c1ac617288382.png "Ubuntu terminal using Dash")](http://www.bardev.com/wp-content/uploads/2017/02/image-36.png) 

<figcaption>Ubuntu 终端使用破折号</figcaption>

</figure>

下面是使用包管理器安装 NodeJS 的文档。转到“基于 Debian 和 Ubuntu 的 Linux 发行版”一节

[https://nodejs.org/en/download/package-manager/](https://nodejs.org/en/download/package-manager/)

在节点文档中，它显示了运行以下命令来安装节点

<figure>[![Using Curl to install nodejs on Ubuntu](img/1b848dcef8f7c8117252f617f3324bba.png "Using Curl to install nodejs on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-37.png) 

<figcaption>使用 Curl 在 Ubuntu 上安装 nodejs</figcaption>

</figure>

如果您尝试运行上面的命令，您会得到一个错误，即没有安装“curl”。

<figure>[![Error running curl in Ubuntu terminal](img/10c3db8cb33a724fc4ed695730c0e466.png "Error running curl in Ubuntu terminal")](http://www.bardev.com/wp-content/uploads/2017/02/image-38.png)

<figcaption>Ubuntu 终端错误运行收拢</figcaption>

</figure>

让我们安装 Curl。

在终端中输入以下内容

sudo 易于安装 curl

<figure>[![installing curl using terminal in ubuntu](img/4bdf33b1dd45cd4bc8ccf7980aa55013.png "installing curl using terminal in ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-39.png) 

<figcaption>在 ubuntu 中使用终端安装 curl</figcaption>

</figure>

既然已经安装了 curl，现在我们可以尝试安装 Node。

首先，我们需要选择我们想要的节点版本，这是使用 Curl 完成的。在终端中使用以下命令。

curl-sL[https://deb.nodesource.com/setup_6.x](https://deb.nodesource.com/setup_6.x)| sudo-E bash-

<figure>[![using curl using to download nodejs on Ubuntu](img/d747fe379f20209e26a9070cbd6ea385.png "using curl using to download nodejs on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-40.png) 

<figcaption>使用 curl 在 Ubuntu 上下载 nodejs</figcaption>

</figure>

现在，要安装节点，请使用以下命令:

sudo apt-get install -y 节点

<figure>[![install nodejs using sudo and apt-get on Ubuntu](img/e4902c20fd803e066d26925a89d4c67e.png "install nodejs using sudo and apt-get on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-41.png) 

<figcaption>在 Ubuntu 上使用 sudo 和 apt-get 安装 nodejs</figcaption>

</figure>

现在，让我们验证节点是否已成功安装。

在“终端”中，键入“节点”。这将显示“>”。现在输入“4+4”然后回车。应该返回 8 的答案。我们现在已经确认安装了节点。要退出节点，请按两次[ctrl]+C。

<figure>[![confirming Nodejs was installed successfully on Ubuntu](img/ca093fb182189e0cc6be2b827db7a1da.png "confirming Nodejs was installed successfully on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-42.png) 

<figcaption>确认 Nodejs 在 Ubuntu</figcaption>

</figure>

上安装成功

## 安装 Git

开发人员使用的另一个常用工具是 Git。Git 是一个源代码库。

我们将像安装 Node 一样安装 Git，但是不使用 Curl。要安装 Git，请输入以下内容:

sudo apt-get 安装 git

<figure>[![installing git using terminal, sudo, and apt-get on Ubuntu](img/af5ba2f028e55033f2d112e935c40d0c.png "installing git using terminal, sudo, and apt-get on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-43.png) 

<figcaption>在 Ubuntu 上使用终端、sudo 和 apt-get 安装 git</figcaption>

</figure>

输入以下内容以确认 Git 已安装:

git 版本

<figure>[![confirming git install successfully using git --version on Ubuntu](img/b7f89258672c18d1c2aaef1edfc71076.png "confirming git install successfully using git --version on Ubuntu")](http://www.bardev.com/wp-content/uploads/2017/02/image-44.png) 

<figcaption>使用 Ubuntu 上的 git 版本</figcaption>

</figure>

确认 git 安装成功

## 结论

如果您从未使用过 Linux，并且想在 Linux 环境中开始开发，这可能会很困难。希望这篇文章已经帮助你在 Linux Ubuntu 中开始你的软件开发。

## 资源

平台虚拟化软件对比

[https://en . Wikipedia . org/wiki/Comparison _ of _ platform _ virtual ization _ software](https://en.wikipedia.org/wiki/Comparison_of_platform_virtualization_software)

如何在 Ubuntu 16.04 上安装 Git

[https://www . digital ocean . com/community/tutorials/how-To-Install-Git-on-Ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-16-04)

通过包管理器

[安装 node . js https://nodejs . org/en/download/package-manager/# debian-and-Ubuntu-based-Linux-distributions](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions)

Ubuntu–用 APT

【https://help.ubuntu.com/community/AptGet/Howto】T2 进行包管理

在 Linux 上运行 VS 代码

【https://code.visualstudio.com/docs/setup/linux T2】

帖子 [Kick-start:使用 VirtualBox、VSCode、Git、NodeJS 为 Windows 开发者创建 Linux(Ubuntu)环境](http://www.bardev.com/2017/02/19/kick-start-linux-ubuntu-visual-studio-code-vscode-for-windows-developers/)最早出现在[麦克巴洛(BarDev)](http://www.bardev.com) 上。