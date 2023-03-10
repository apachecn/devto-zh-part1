# 创建 Android 应用快捷方式

> 原文：<https://dev.to/adammc331/creating-android-app-shortcuts>

Android 应用程序快捷方式是 Android 7.1 (API 25)中添加的一个特殊功能。快捷方式是当你长按启动器图标时出现的特殊动作，它将触发一个特定的意图。通过这篇文章，你将学习如何创建静态和动态的应用程序快捷方式。唯一的先决条件是你的应用是针对 API 25 或更高版本的。

#### 静态快捷键

静态应用快捷方式是通过 XML 定义的快捷方式。这意味着它们是在编译时创建的，只要你的应用程序在用户的设备上，它们就会保持一致。这种情况的常见用例是在应用程序中启动一个经常使用的活动，而不是启动器活动。

让我们首先看看如何在 XML 中定义一个快捷方式:

```
<?xml version="1.0" encoding="utf-8"?>
<shortcuts xmlns:android="http://schemas.android.com/apk/res/android">
    <shortcut
        android:enabled="true"
        android:icon="@mipmap/ic_launcher"
        android:shortcutId="static_shortcut"
        android:shortcutLongLabel="@string/static_shortcut_long_label"
        android:shortcutShortLabel="@string/static_shortcut_short_label">
        <intent
            android:action="android.intent.action.VIEW"
            android:targetClass="com.androidessence.appshortcuts.MainActivity"
            android:targetPackage="com.androidessence.appshortcuts" />
    </shortcut>
</shortcuts> 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段位于 xml-v25 文件夹中名为 shortcuts.xml 的文件中。属性名称使这变得非常直接，但下面是一个快速运行:

*   启用-是否可以使用您的快捷方式。
*   图标-出现在快捷方式上的图标。
*   shortcutId -用于在代码中引用您的快捷方式的 Id。
*   长/短标签-显示在快捷方式上的标签。显示哪个标签取决于所用设备的屏幕尺寸。
*   意图-当你的快捷方式被点击时应该启动的意图。在这种情况下，只是启动我们的主要活动。

但是，为了让这个快捷方式在长时间按下后出现，我们需要向我们的启动器活动添加一些元数据。在 AndroidManifest.xml 文件中找到您的默认活动，并添加以下信息:

```
<meta-data
   android:name="android.app.shortcuts"
   android:resource="@xml/shortcuts" /> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！此时您可以运行该应用程序，您应该会看到一个静态快捷方式出现。

#### 动态快捷键

创建启动器活动时，会通过 Java 代码添加动态应用程序快捷方式。因此，用户可以更改、禁用和定制它们。动态应用程序快捷方式的一个用例是一个消息应用程序，例如，你可以为你的用户的前 5 个联系人创建一个快捷方式，打开与被点击的联系人的消息线程。

创建动态快捷方式非常简单，但您可能需要调整它来创建特定于您的应用程序的快捷方式。首先，只需使用[快捷信息。构建器](https://developer.android.com/reference/android/content/pm/ShortcutInfo.html)类来创建一个快捷方式，就像我们在 XML 中所做的一样:

```
Intent googleIntent = new Intent(Intent.ACTION_VIEW, Uri.parse("https://www.google.com"));

ShortcutInfo shortcut = new ShortcutInfo.Builder(this, "dynamic_shortcut")
   .setShortLabel(getString(R.string.dynamic_shortcut_short_label))
   .setLongLabel(getString(R.string.dynamic_shortcut_long_label))
   .setIcon(Icon.createWithResource(this, R.mipmap.ic_launcher))
   .setIntent(googleIntent)
   .build(); 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以得到我们的[快捷方式管理器](https://developer.android.com/reference/android/content/pm/ShortcutManager.html)并添加快捷方式:

```
ShortcutManager shortcutManager = getSystemService(ShortcutManager.class);
shortcutManager.addDynamicShortcuts(Collections.singletonList(shortcut)); 
```

Enter fullscreen mode Exit fullscreen mode

现在你也创建了一个动态应用程序快捷方式！如果你再次运行该应用程序，当你长按启动器图标时，你也会看到这个快捷方式。需要注意的一点是，我们刚刚创建的图标位于活动的 onCreate()方法中。这意味着它不会在安装后立即出现，但会在用户第一次打开应用程序后出现。

#### 继续发展

这只是一个如何创建快捷方式的快速入门指南，但你可能还有更多问题。我如何改变它们？我如何禁用它们？如果用户锁定一个快捷方式会发生什么？我可以追踪快捷方式的分析吗？

这些问题中的许多可以在关于应用程序快捷方式的官方文档中找到答案。

[Caren Chang](https://twitter.com/calren24) 在波士顿 Droidcon 上做了一个关于应用快捷方式的精彩演示，但在发布这段视频时，视频还没有发布。我会试着更新博文。

这篇博文使用的示例应用程序可以在 [GitHub](https://github.com/androidessence/App-Shortcuts) 上找到。