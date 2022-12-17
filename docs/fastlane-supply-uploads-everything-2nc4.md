# 浪子供应上传一切！

> 原文：<https://dev.to/luisramos1337/fastlane-supply-uploads-everything-2nc4>

自从我发现了 [`fastlane`](https://fastlane.tools/) ，我就一直用它来部署 Android 和 iOS 应用。令人惊讶的是，事情可以变得如此简化:在部署一个版本时，它为您节省了大量时间；它支持与您最喜欢的分销平台(yay Crashlytics)集成；您可以轻松地将设置从一个应用程序复制到另一个应用程序！再开心不过了。

这里有一个使用 [`fastlane`](https://fastlane.tools/) 的 Android 开发人员的提示，把这个添加到你的`before_all`步骤:

```
before *all do
  sh "rm -f ../app/build/outputs/apk/*/\_/\*.apk"
end 
```

我发现当使用`supply`部署 Android 构建时，该工具会尝试上传它在`build/outputs`文件夹中找到的任何内容。通常你得到的错误是不相关的，比如:

```
Google Api Error: apkUpgradeVersionConflict:
APK specifies a version code that has already been used. 
```

如果你检查你的 google play 控制台和你的 build.gradle 文件，这显然是不正确的。查看输出(在`GRADLE_ALL_APK_OUTPUT_PATHS`变量处)，似乎 supply 正在尝试上传他找到的每个 apk，包括调试版本和其他版本。添加这一行将为您节省一些时间，并避免您发出“为什么版本不匹配？?"