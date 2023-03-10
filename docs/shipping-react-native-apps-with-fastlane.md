# 将 React 本地应用程序与浪子一起发布

> 原文：<https://dev.to/carloscuesta/shipping-react-native-apps-with-fastlane>

*最初发布于 [carloscuesta 的博客](https://carloscuesta.me/blog/shipping-react-native-apps-with-fastlane/)T3*

用[**React**](https://facebook.github.io/react-native/)(*JavaScript*)构建原生应用是非常棒的。直到你不得不**发布新版本**，特别是，如果你不是`iOS`或`Android`本地开发者。手动**将应用程序**发送到商店**的过程**是一个**痛苦的**和**耗时的**体验，尽管它是有文档记录的。

这时**前来救援！在这篇文章中，我将解释如何自动化一个`iOS`ðÿ和`Android`ðÿ应用程序的发布过程。处理最常见的任务，例如:**代码签名**、**应用构建**、**测试版**等等！实际上**我们使用** **浪子**at[**Ulabox**](https://www.ulabox.com)来构建我们的 react-native 应用。**

 **> 浪子是发布你的 iOS 和 Android 应用的工具，它处理所有繁琐的任务，比如生成截图，处理代码签名，发布你的应用。

### 入门

在[安装浪子](https://github.com/fastlane/fastlane#installation)之前，确保你已经安装了最新的 Xcode 命令行工具，然后安装浪子。

```
$ brew cask install fastlane 
```

Enter fullscreen mode Exit fullscreen mode

安装完成后，在 react-native 项目的*根级别*下创建一个`fastlane/`ðÿ”文件夹。然后，在这个目录中创建一个名为`Fastfile`的文件。

快速文件是我们将要对**通道**进行编码的地方。一个通道包含一组 [**动作**](https://docs.fastlane.tools/actions/#code-signing) 的**组**，这些动作将被同步执行，以使流程自动化。一个**动作**，是一个执行任务的**功能**。

让我们从这个`Fastfile`基础模板开始，正如您可以看到的，有一个`before_all`挂钩，它基本上执行一个**健康检查**ðÿ'“通过三个动作，确保您在**最新** `master`分支上处于**干净**状态。

```
fastlane_version '2.53.1'

before_all do
  ensure_git_branch
  ensure_git_status_clean
  git_pull
end

platform :ios do
   # iOS Lanes
end

platform :android do
  # Android Lanes
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还定义了**两个平台**，我们将使用ðÿ和ðÿ，其中**将包含**用于**每个上下文**的**特定通道**。定义了[平台](https://github.com/fastlane/fastlane/blob/master/fastlane/docs/Platforms.md)之后，我们就可以像这样执行通道:`fastlane ios lane` `fastlane android lane`。

### 代码符号

#### iOS

编码标志的最佳方式是 **[匹配](https://github.com/fastlane/fastlane/tree/master/match)** ，在将匹配整合到车道之前，您必须:

1.  [Nuke](https://github.com/fastlane/fastlane/tree/master/match#nuke) 现有的配置文件和证书。
2.  [通过`init`选项设置匹配](https://github.com/fastlane/fastlane/tree/master/match#setup)。
3.  在使用 match 的`ios`平台上创建一个车道。

```
desc 'Fetch certificates and provisioning profiles'
lane :certificates do
  match(app_identifier: 'com.app.bundle', type: 'development', readonly: true)
  match(app_identifier: 'com.app.bundle', type: 'appstore', readonly: true)
end 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以运行`fastlane ios certificates`或者使用`cerfiticates`作为另一个车道的功能。`match`会自动将预置描述文件和证书存储在您的 OS X 钥匙串上。

#### 安卓

**自动**当你**在`Release`模式下使用`assemble`任务构建**一个 **Android** 应用时**应用**将被**签名**。但是首先您需要生成或获取签名密钥并将其添加到项目中，所以看一看[这个 facebook 指南](https://facebook.github.io/react-native/docs/signed-apk-android.html)就知道如何做了。

### 打造

#### iOS

为了生成一个**签名构建**，我们将创建一个球道，它使用我们之前创建的`cerficates`球道和 **[健身房](https://github.com/fastlane/fastlane/tree/master/gym)** 编译**我们的**应用程序**。在流程结束时，我们希望增加构建号，以便将我们的应用程序交付给 **beta 测试**服务。** 

```
desc 'Build the iOS application.'
private_lane :build do
  certificates
  increment_build_number(xcodeproj: './ios/name.xcodeproj')
  gym(scheme: 'name', project: './ios/name.xcodeproj')
end 
```

Enter fullscreen mode Exit fullscreen mode

#### 安卓

为了生成签名的`.apk`，我们将创建一个`build`通道。正如你所看到的，我们正在使用 **gradle** 动作，来清理项目并组装一个发布版本，包含 gradle 任务。

```
desc 'Build the Android application.'
private_lane :build do
  gradle(task: 'clean', project_dir: 'android/')
  gradle(task: 'assemble', build_type: 'Release', project_dir: 'android/')
end 
```

Enter fullscreen mode Exit fullscreen mode

然后**通过这个小任务连接`assembleRelease` [来自动完成](https://gist.github.com/carloscuesta/678668da906bb80bdd22c8fd690c4fc4)**这个`versionCode`凸起。

### 贝塔分布

#### iOS

测试飞行 这是 iOS beta 测试的必经之路。运行得非常好，尽管*开发者门户*有点混乱。有了 **[飞行员](https://github.com/fastlane/fastlane/tree/master/pilot)** 我们就可以**管理**我们的**试飞建造**。

`beta`通道将使用`build`通道向飞行员提供一个签名的`.ipa`，然后将提交和推送通过增加构建号产生的更改，最后将把**本地**构建上传到**试飞**。ðÿž‰

```
desc 'Ship to Testflight.'
  lane :beta do
    build
    pilot
    commit_version_bump(message: 'Bump build', xcodeproj: './ios/name.xcodeproj')
    push_to_git_remote
  end 
```

Enter fullscreen mode Exit fullscreen mode

#### 安卓

**安卓**使用 **[Playstore](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)** 分享`beta`构建。我们也可以通过 fastlane 实现自动化！

Android 的`beta`通道与 iOS 几乎相同，使用构建通道生成签名的`.apk`，提交`versionCode`更改，并使用 **[供应](https://github.com/fastlane/fastlane/tree/master/supply)** **将**本地构建升级为 Playstore 的*测试版*。

```
desc 'Ship to Playstore Beta.'
  lane :beta do
    build
    supply(track: 'beta', track_promote_to: 'beta')
    git_commit(path: ['./android/gradle.properties'], message: 'Bump versionCode')
    push_to_git_remote
  end 
```

Enter fullscreen mode Exit fullscreen mode

### 集成为脚本

我强烈建议将**快车道**添加为 [`npm scripts`](https://docs.npmjs.com/misc/scripts) ，使其成为你当前构建系统的一部分。

```
"scripts":  {  "ios:beta":  "fastlane ios beta",  "android:beta":  "fastlane android beta"  } 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！向菲利克斯·克劳斯 和所有支持快车道ðÿ'的人致敬**