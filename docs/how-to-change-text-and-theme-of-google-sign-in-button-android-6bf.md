# 如何更改 Android 上 Google 的登录按钮的文字和主题？

> 原文：<https://dev.to/shobhit/how-to-change-text-and-theme-of-google-sign-in-button-android-6bf>

### 快速汇总

在这篇文章中，我将向你展示如何使用标准的`android:text`属性来改变谷歌登录按钮上的文本，这在谷歌的[登录按钮](https://developers.google.com/android/reference/com/google/android/gms/common/SignInButton)中是没有的。我还将向你展示，你如何在深色和浅色按钮主题之间切换。在文章的最后，包含了一个现成的小型 Android 库，以及一个示例应用程序来做同样的尝试。

### 要求

*   安卓工作室。
*   `compileSdkVersion 25`和`buildToolsVersion "25.0.3"`。这就是示例应用程序所使用的。您可以更改`build.gradle`中的值，以使用您已安装的 SDK 版本。
*   `minSdkVersion`支持的是`16`。

### 简介

您想为 Google 登录按钮提供本地化吗？也许您想将默认的“登录”文本改为“使用 Google 登录”。或者你想在“登录谷歌”和“注册谷歌”文本之间切换，这取决于它是一个登录还是注册流程。您可能已经知道，要在 Android 上设置文本`Button`，您可以在布局 XML 中使用`android:text="{string}"`属性。如果您想对 Google Sign-In 进行同样的操作，则该属性不可用。在 Stackoverflow 上，显然有很多类似[的问题，我可以在谷歌上编辑登录按钮的文本吗？](https://stackoverflow.com/questions/18040815/can-i-edit-the-text-of-sign-in-button-on-google)，已经有人问过这个问题了。大多数现有的答案是在写作时被黑客攻击的。

跟随我向你展示如何使用一个非常小的库，它是使用谷歌推荐的定制登录按钮的方式创建的，这将使你能够使用`android:text`属性来设置按钮上的任何文本。该库还使您能够轻松地将按钮的主题更改为亮或暗。

### 用法

1.  将以下内容添加到您的`app`模块级`build.gradle`文件中:

    ```
    dependencies {
        // To include this library.
        // For Android Studio 3.0 or above, use implementation keyword instead
        compile 'com.shobhitpuri.custombuttons:google-signin:1.0.0'
    } 
    ```

2.  在您的 XML 布局中，具有以下内容:

    ```
    <RelativeLayout
        ...
        xmlns:app="http://schemas.android.com/apk/res-auto">

        <com.shobhitpuri.custombuttons.GoogleSignInButton
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_centerInParent="true"
            android:text="@string/google_sign_up"
            app:isDarkTheme="true" />
    </RelativeLayout> 
    ```

### 库选项

如果您注意到 XML，就会发现它有两个属性:

*   `app:isDarkTheme="{Boolean}"`:这是为了让你在按钮的亮(灰白色)主题和暗(蓝色)主题之间切换。该库基于 Google 推荐的指导方针处理文本颜色、背景颜色和按钮按压时的颜色变化。

*   `android:text="{string}"`:设置自定义按钮上的文本。

| 轻音乐主题 | 黑暗主题 |
| --- | --- |
| ![Google Sign-In Light](img/1dafa86730f729e4172571295e6be445.png) | ![Google Sign-In Dark](img/2d706bc27bc6637dfa91cd63c5bbac54.png) |

### 为什么是图书馆？

“为什么要为此创建一个库呢？不是已经有很多图书馆了吗？”你会说。这个库是我试图在`SignInButton`上设置文本时遇到的问题的结果。我不喜欢使用黑客，既然你在这里，到目前为止，我想你也不喜欢。如果谷歌`SignInButton`的底层实现发生变化，黑客就会崩溃。文档中推荐的理想的 Google 解决方案是创建一个自定义按钮，正如[自定义登录按钮中提到的。](https://developers.google.com/identity/sign-in/android/custom-button)它规定了[品牌指南](https://developers.google.com/identity/branding-guidelines#sign-in-button)，其中包括为按钮使用自定义图标和图像，为徽标设置特定的文本大小、填充和其他注意事项。

如你所见，理想的解决方案需要一些额外的工作。我想写一些可重用的代码，可以拖放到我的任何项目中，并且开箱即用，而不是只为我的使用创建一个自定义按钮。这就是为什么我决定创建一个 3.93 KB 的小型库，这样任何面临这个问题的人都不需要花费时间来实现一个定制的解决方案，并且可以立即让定制的 Google 登录按钮工作。

### 源代码和示例 Android 应用程序

你可以在这里找到这个库的实现和一个使用这个库的示例 Android 应用程序:[https://github.com/shobhitpuri/custom-google-signin-button](https://github.com/shobhitpuri/custom-google-signin-button)。请随意对文章或库给出任何反馈。如果您遇到任何问题，我们非常欢迎您创建问题或提出拉动请求。

***注:本文使用的图片是谷歌的商标。它们只是用于教学目的。*T3】**