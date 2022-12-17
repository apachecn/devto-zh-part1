# 在 Raspberry Pi Zero W 上开始使用 Homebridge

> 原文：<https://dev.to/brandonb927/getting-started-with-homebridge-on-a-raspberry-pi-zero-w-242h>

这篇文章是我指导你在 macOS 上设置一个无头的 Raspberry Pi Zero W 的第一篇文章的延续。在我写完那篇文章之后，我的 rPi 并没有发生太多的事情，在我考虑将它投入使用的时候，它被搁置了几个月。然而，在接下来的几个月里，我对家庭自动化越来越感兴趣，尤其是对 [HomeKit](https://www.macrumors.com/guide/homekit-101-getting-started-beginners/) 感兴趣。我开始想，“我应该能够在我的 iPhone 7 Plus 上使用运行 iOS 11 的家庭应用程序来控制我家里所有的连接设备，而不是这些其他劣质应用程序！”。

在为家庭自动化做研究的时候，我偶然发现了 homebridge，它让我明白了使用 rPi 就是为了它。在安装和设置了几次(在安装和重试之间擦除 SD 卡)之后，我开始意识到设置是多么令人惊讶的简单！在这篇文章中，我将向你展示如何让你自己的 rPi 运行 homebridge，甚至推荐一些插件来使它有用。

首先，我开始清点我家里所有可以添加到 homebridge 以与 HomeKit 一起使用的东西。我最初计划只以某种方式集成我的 2016 三星智能电视和 Playstation 4，但一旦我安装并运行了所有东西，我就购买了几个 TP-Link HS100 智能 wifi 插头。wifi 插头在我当地的 BestBuy 以 20 美元/个的价格出售，比正常价格低 50%😱我不能说不，而且我已经注意到它们了，因为对 homebridge 插件的研究表明它们对系统有很好的支持，尽管 HomeKit 根本不支持它们。

### 安装节点和 homebridge

从这里开始用 npm [安装 node(不要从`apt-get`开始安装，因为 PPA 的包已经非常过时了)。npm 的一个替代方案是使用](https://github.com/sdesalas/node-pi-zero#v890)[纱线](https://yarnpkg.com/en/)，但是据我所知，这条路线还没有经过测试。

一旦安装了节点，继续安装 [homebridge](https://github.com/nfarina/homebridge) 。安装它就像安装一个全局 npm 包一样简单！

```
npm install -g homebridge 
```

Enter fullscreen mode Exit fullscreen mode

## 运行住宅桥

安装后，您只需调用可执行文件，就可以尝试从 cli 运行 homebridge。它会在`/home/<user>/.homebridge`中寻找配置，所以可以随意添加一个基本的配置文件进行测试。

然而，使用 homebridge 的理想方式是使用 systemd 在后台将它作为服务运行。这允许 homebridge 在启动时自动运行，并且不需要在您的计算机上打开外壳。我在这个设置中发现了一个警告，虽然很容易解决，但是 homebridge 可执行文件的路径对于设置它时必须创建的所需的`homebridge`系统用户来说是未知的。它没有在任何地方记录，但是可以通过向用户添加节点`bin`路径来修复。你可以通过在`/etc/profile.d`中创建一个新文件并使其可执行
来做到这一点

```
sudo touch /etc/profile.d/node.sh
sudo chmod +x /etc/profile.d/node.sh 
```

Enter fullscreen mode Exit fullscreen mode

其中的内容是

```
export PATH=$PATH:/opt/nodejs/bin 
```

Enter fullscreen mode Exit fullscreen mode

这是将`/opt/nodejs/bin`添加到每个用户配置文件的`PATH`环境变量中。这意味着系统用户`homebridge`现在可以正常运行 homebridge exectuable，而不需要完整的路径。另一种方法是在调用时指定可执行文件的完整路径。

### [T1】配置 homebridge](#configuring-homebridge)

此时，重新启动 pi，让系统用户选择 homebridge 可执行文件的路径并启动它。对于 homebridge，您没有任何配置设置可以使用(除非您跳到这里😉)而且它会抱怨这个。您可以使用
查看 homebridge 服务的实时日志流

```
sudo journalctl -u homebridge -f 
```

Enter fullscreen mode Exit fullscreen mode

创建文件`/var/lib/homebridge/config.json`,并用下面的启动器配置
填充它

```
{
  "bridge": {
    "name": "Homebridge",
    "username": "1A:2B:3C:4D:5E:6F",
    "port": 45525,
    "pin": "937-19-468"
  },
  "description": "SmartHome with Homebridge",
  "accessories": [...],
  "platforms": [...]
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 备注

*   使用[这个工具](https://www.miniwebtool.com/mac-address-generator/)在配置中生成一个随机的`username`值
*   一旦你确认 homebridge 可以独立运行，就用你想要的插件替换`...`

在你完成所有的修改后，确保`homebridge`用户拥有`/var/lib/homebridge`文件夹和
的所有权

```
sudo chown -R homebridge /var/lib/homebridge 
```

Enter fullscreen mode Exit fullscreen mode

**注意**这一步是*非常关键的*因为如果 homebridge 可执行文件不能将它的各种缓存写入文件夹，事情可能会在你身上发生！

### 延伸住宅桥

你会发现 Homebridge 本身并不是很有用，它真正的亮点是其社区支持的插件系统。只要在 npmjs.com 上搜索`homebridge-plugin`，就会有几百个插件可供选择。按受欢迎程度排序，前几页全是好插件。

以下是我正在使用/推荐的一系列产品:

*   [https://www.npmjs.com/package/homebridge-cmdswitch2](https://www.npmjs.com/package/homebridge-cmdswitch2)
    *   👆结合[https://github.com/dhleong/ps4-waker](https://github.com/dhleong/ps4-waker)
*   [https://www.npmjs.com/package/homebridge-samsungsmarttv](https://www.npmjs.com/package/homebridge-samsungsmarttv)
    *   [我正在进行的分叉](https://github.com/brandonb927/homebridge-plugin-samsungsmarttv)(等待 iOS 11 在 Home 应用中正确支持`Speaker`服务，它们是 HomeKit 规范的一部分，只是在撰写本文时尚未实现)
*   [https://www.npmjs.com/package/homebridge-tplink-smarthome](https://www.npmjs.com/package/homebridge-tplink-smarthome)(支持开关、灯泡和插头)

### 配合 iOS 11 Home app 使用 homebridge

你可能一直在等待的时刻:在 iOS Home 中设置 Homebridge 和“附件”(连接的设备)非常简单。应用内文档非常容易理解，如果您从终端(而不是基于服务的系统用户)运行 homebridge cli 工具，您只需扫描使用 Home 应用运行`homebridge`时生成的二维码。另一种主设置方法是，您必须输入您在`config.json`文件中配置的`pin`。

下面是最终产品的视频，以及它与 iOS 11 Home 应用程序的配合情况。该视频首先展示了应用程序中不同设备的触发方式(以及一些设备必须如何进行交互，特别是我的智能电视和 PS4，这有一些延迟，我稍后会详细介绍)，然后展示了如何使用 Siri 通过家庭场景与设备进行交互。

<video width="100%" height="320" controls=""><source src="https://dc1r9kxqg42ml.cloudfront.net/brandonb.ca/posts/getting-started-with-homebridge-on-a-raspberry-pi-zero-w-with-homekit/homebridge-ios-home-demo.mp4" type="video/mp4">

嗯，你的浏览器好像不支持 HTML5 视频。</video>