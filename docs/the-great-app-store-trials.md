# 伟大的应用商店试验

> 原文：<https://dev.to/gaiety/the-great-app-store-trials>

[今天是我作为一名公开的应用商店开发者的第一天](https://play.google.com/store/apps/details?id=io.cordova.myspells)。这是我的一个梦想，不仅仅是在网上发布我的作品，而是可以放在口袋里而不需要浏览器的东西。有些故事讲述的是伟大的英雄通过令人难以置信的力量战胜危险的挑战；这一个是发布一个网络应用到 Play 商店的问题和解决方案。

## 第一幕:构建应用

我选择了由 [Vue.js](https://vuejs.org/) 驱动的 [Quasar 框架](http://quasar-framework.org/)，内置了 [Cordova](https://cordova.apache.org/) 移动转换。**选择能够很好地配合的工具**让我确信，如果我*阅读文档*并付出努力**，我最终会成功**。

**发布常**。我给任何人的建议，对于业余爱好者的项目来说更容易遵循。点击你的**最低可行产品，然后向公众发布**应用程序。

### 问题:便宜又容易地发布一个 Web 应用

我不是服务器管理员。我已经建立了我的应用程序，只是希望看到它在网上公开消费。

#### 解:Netlify

开源项目可以直接从 Github repo 免费自动部署和构建到 [Netlify](https://www.netlify.com/) 服务器上。一旦托管后，您将获得一个简单的 URL 来包含在您的项目中。

### 问题:漏洞

它们会发生，错误是应用程序开发的自然组成部分。在你的电脑上保留一个待办事项列表似乎与我们生活的互联开源世界背道而驰。

#### 解决方案:Github 问题

在记录错误方面保持透明有很多好处。 [Github 问题](https://guides.github.com/features/issues/)是一个很好的工具。

*   像待办事项一样简单。
*   伟大的实践，尤其是如果你*按照[永远](http://semver.org/)T3】写*干净提交*。*
*   用户知道出了什么问题，并有机会帮助自己修复漏洞。
*   **为项目**形成了一个公共历史，而*则鼓励随着应用程序的发展而修改日志*。

## 第二幕:打造安卓系统

总的来说，Quasar 让 Android 的编译变得简单而愉快。运行他们的移动开发工具发现了几个特定于移动设备的错误，我记录了这些错误，然后进行了修复。

Quasar 的简单工具对于快速反馈来说是很棒的，但是与在你的手机上运行的真正的调试 APK 相比，这根本不算什么。幸运的是，有文件可以让你用科尔多瓦包装你的 Quasar 应用。

### 问题:跑科尔多瓦建 APK

科尔多瓦完成工作后，文档很快就消失了。

> 你发现自己在一个假想知识的世界里，处在一个观点和工具的狼窝里。

通过 [Android Studio](https://developer.android.com/studio/index.html) 进行模拟是可能的，但我永远无法让它与我的非 Android Studio 应用程序一起工作。

#### 解决方案:Cordova 编译到设备。

经过一番挖掘，在我的 Android 设备上编译最成功的方法是:

1.  `quasar build`来自我的**根**。
2.  `quasar run --device`从 **cordova 目录**下载，同时我的**手机插上**的电源 **USB 调试**开启。

这将自动编译并在您连接的 Android 设备上打开应用程序。

*P.S .这里可能还需要 Android Studio。*

##### 也...克服 USB 调试

在现代 Android 手机上启用 USB 调试是显而易见的反面。幸运的是，[这篇 Android USB 调试指南](https://www.kingoapp.com/root-tutorials/how-to-enable-usb-debugging-mode-on-android.htm)解释了这个过程。

##### 也...克服 USB 电缆差异

我遇到过 USB 调试拒绝工作的时候。原来*有些 usb 线只传输电力，不传输数据*。使用正确的 USB 电缆类型，避免因为愚蠢的原因浪费时间。

#### 问题:本地 Ajax 调用不起作用

JSON 不能像我们在 Netlify 上那样在本地获取。我不希望把我所有的数据都放在 web 应用程序上来降低初始加载速度。

#### 解决方案:只为应用构建烘焙数据。

对于我的 web 应用程序，唯一关心的是第一次加载时的应用程序大小。在手机上这不成问题。事实上，烘焙数据意味着更容易的离线解决方案。

我写了一个[数据处理器](https://github.com/sharpshark28/my_spells/blob/master/build/process_spells.js)节点脚本，可以将我的数据编译成 JSON 文件或者 JS 可导入模块(用`export default ...`)以及一些 [package.json 构建脚本](https://github.com/sharpshark28/my_spells/blob/master/package.json)，以简化开发和发布过程。在应用程序中，我总是导入 JS 模块，如果有`data.length > 0`我就使用它，否则我会尝试获取 JSON 文件。

这需要努力，但我确信我的网络和安卓应用都能快速启动。

## 第三幕:出版

恭喜你！你有一个运行在 Android 上的应用程序。接下来，你必须摸索通过 *app 发布流程*。我已经尽力把步骤、问题和解决方法列在下面了。

### 第一步:你想要钱和我的灵魂？

第一步是用你的谷歌用户注册一个出版商账户。这包括同意许多服务条款，并支付 25 美元的费用，这使你免受垃圾软件和垃圾邮件的潜在冲击，否则它们可能会到达 Play Store。相对简单明了的*和必要的邪恶*才能进步。

### 问题:可释放的 APK 文件

到目前为止*我们一直在使用一个调试 APK* 文件。这对释放来说不够好。

#### 解决方案:科尔多瓦构建

`cordova build`将创建一个 android-release-unsigned.apk，顾名思义，这是一个**未签名，但准备发布 APK** 。

### 问题:签署 APK 文件

这个过程中记录最少的部分之一是在一个完全由 Android Studio 开发的应用程序之外签署 APK。

#### 解决方案:Android App Studio、Keystore、jarsigner

1.  安装 [Android Studio](https://developer.android.com/studio/index.html) 并启动
2.  打开现有项目...为应用程序选择 cordova 创建的项目目录
3.  构建->生成签名的 APK
4.  密钥存储路径:新建(按照屏幕提示)
    *   记下保存它的位置，以及密钥别名(key0 就可以了)
5.  关闭 Android Studio，我们将使用 jarsigner 来签署我们的 apk:

```
 jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <~/path/to/keystore.jks> <~/path/to/android-release-unsigned.apk> <KEY ALIAS> 
```

Enter fullscreen mode Exit fullscreen mode

### 问题:安装到 Android 设备上

让我们让它再次在我们的 Android 上运行，签名并准备发布。

`adb install ~/path/to/android-release-signed.apk`

#### 解决方案:安装`adb`

`adb`可以从 Android Studio 首选项屏幕安装:

1.  外观和行为>系统设置> Android SDK > SDK 工具
2.  查看 Android SDK 构建工具、Android SDK 平台工具、Android SDK 工具
3.  如果`adb`仍然不可用，请像这样更正您的路径:[https://stack overflow . com/questions/10303639/ADB-command-not-found](https://stackoverflow.com/questions/10303639/adb-command-not-found)

现在在你的 apk 上运行`adb install`会直接安装到你的设备上。

### 问题:zipalign

上传到 Play Store 时，您可能会看到一个通知，指出 apk 没有进行压缩。

#### 解决方案:zipalign it

不幸的是，这不是一个我们可以自己安装的工具，但它确实与 Android Studio 一起提供。

我们希望运行的命令类似于:

`zipalign -v 4 ~/path/to/file.apk ~/path/to/newfile.apk`

将`zipalign`替换为它在您机器上的路径，可能在您的 android studio sdk 目录下。

* * *

最后，我们有了一个**签名的、zipaligned 的、可发布的 APK 文件，为 Play Store** 做好了准备。按照网站上的提示，你的应用有望在一天内发布。

我鼓励其他人写下他们为 Play Store 开发应用的经历。我相信有更简单的方法可以达到同样的效果，而且更少的麻烦。最终，在 Play Store 上拥有一个已发布的应用程序是一种非常令人满意的体验，这篇文档将作为我所经历的尝试的一个提醒，我试图告诉其他人在使用一些对我有效的解决方案的过程中可能会遇到的问题。

被困在一个类似的项目中？在 Twitter @SharpShark28 上联系我