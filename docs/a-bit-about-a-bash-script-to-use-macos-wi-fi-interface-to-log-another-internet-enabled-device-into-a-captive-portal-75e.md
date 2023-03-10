# 关于使用 macOS 的 Wi-Fi 接口将另一个支持互联网的设备登录到强制门户的 bash 脚本

> 原文：<https://dev.to/ayharano/a-bit-about-a-bash-script-to-use-macos-wi-fi-interface-to-log-another-internet-enabled-device-into-a-captive-portal-75e>

*原载于[我的媒介账号](https://medium.com/@ayharano)于[此链接](https://medium.com/@AYHarano/a-bit-about-a-bash-script-to-use-macos-wi-fi-interface-to-log-another-internet-enabled-device-into-a2f5a511446)。*

你好。

根据 Felipe Brigatto 的建议，我写了一篇文章来分享我写的一个小剧本。

在企业环境中，主要是在酒店中，有一个系统用于在允许用户和设备使用互联网之前对他们进行身份验证(通常有限制)，该系统被称为 *[强制网络门户](https://en.wikipedia.org/wiki/Captive_portal)* 。该系统允许设备(智能手机、平板电脑、笔记本电脑、电子书、游戏机或其他支持互联网的设备)获取 IP 地址，但最初设备无法访问互联网，直到用户注册帐户并使用所选凭据登录门户。不幸的是，并非所有支持互联网的设备都提供有用的界面，让用户仅使用设备本身登录强制网络门户，例如 [Chromecast](https://store.google.com/product/chromecast_2015) 。因此，将此类设备登录到强制网络门户的一种方法是:

1.  将支持互联网的设备的 [MAC 地址](https://en.wikipedia.org/wiki/MAC_address)克隆到工作网络接口中；
2.  使用工作网络接口伪装成支持互联网的设备登录到目标强制网络门户；
3.  将原始 MAC 地址恢复到工作网络接口；和
4.  在强制网络门户的限制下使用支持互联网的设备，因为该设备已经过身份验证。

上述列表是使用工作设备登录强制网络门户的通用指南，该设备在登录目标强制网络门户时没有任何问题。

[我写的脚本](https://gist.github.com/ayharano/3ead655ca67192f8fda5d99deaec1d60)是为 Mac OS X / OS X / macOS 定制的，因为它使用了`networksetup`。但是只要用户知道他们操作系统的网络二进制文件来处理 Wi-Fi 接口，将它应用于其他[操作系统](https://en.wikipedia.org/wiki/Operating_system)应该不难。我喜欢写 [`bash`](https://www.gnu.org/software/bash/) 脚本，为此我遵循了来自 [Julia Evans](https://medium.com/@b0rk) 的 [Twitter 账户](https://twitter.com/b0rk)的一些提示，例如精彩的 [ShellCheck](https://www.shellcheck.net/) 。加油！[棉绒](https://en.wikipedia.org/wiki/Lint_%28software%29)很棒！

在终端中使用以下命令下载脚本并将其设置为可执行脚本:

```
$ # Download captive.sh
$ curl https://gist.githubusercontent.com/ayharano/3ead655ca67192f8fda5d99deaec1d60/raw/ > ~/captive.sh
$ # Set captive.sh as an executable script
$ chmod +x ~/captive.sh 
```

在使用本文中提到的脚本之前，请阅读以下内容，以检查该脚本到底是什么，以及它与到目前为止编写的脚本相比如何。