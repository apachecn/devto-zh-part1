# 跨 Android 应用模块维护库版本

> 原文：<https://dev.to/adammc331/maintain-library-versions-across-modules>

每次新版本发布时都要更新支持库的版本号，维护你的应用程序已经够难的了，更不用说考虑你可能使用的任何其他第三方库了。如果您有多个模块，这尤其令人痛苦，因为您必须更新每个`build.gradle`文件中的版本。幸运的是，我们可以利用项目级的 gradle 文件使其更易于维护。

# 在项目 Gradle 文件中定义变量

在项目的根文件`build.gradle`中，您可以定义一些 [ExtraPropertyExtensions](https://docs.gradle.org/current/dsl/org.gradle.api.plugins.ExtraPropertiesExtension.html) ，它们将被不同的模块重用。看起来应该是这样的:

```
 allprojects {
        repositories {
            jcenter()
        }

        ext {
            //Android
            androidBuildToolsVersion = "25.0.2"
            androidMinSdkVersion = 16
            androidTargetSdkVersion = 25
            androidCompileSdkVersion = 25

            //Libraries
            supportLibraryVersion = "25.3.1"
            playServicesVersion = "10.2.1"
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一些可重用的变量，我们希望这些变量在各个模块中保持一致。

# 定义模块中的全局配置引用

在每个模块的`build.gradle`文件中，在“android”块中，我们需要定义一个对我们刚刚创建的全局配置的引用。我们可以用下面一行代码做到这一点:

```
 android {
        def globalConfiguration = rootProject.extensions.getByName("ext")

        ...
    } 
```

Enter fullscreen mode Exit fullscreen mode

因此，有两种方法可以使用这些属性。当试图单独引用它们时，对于构建工具或编译 sdk 版本等属性，可以使用属性键:
进行引用

```
android {
    ...
    compileSdkVersion globalConfiguration["androidCompileSdkVersion"]
    buildToolsVersion globalConfiguration["androidBuildToolsVersion"]
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想将版本号作为编译语句的一部分，您可以简单地使用 gradle 的字符串插值:

```
 compile "com.android.support:appcompat-v7:${supportLibraryVersion}" 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！一个简单的小技巧就是你所需要的在模块间保持一致的库版本。关于进一步研究这个问题的一些注意事项:

*   以上所有代码的要点可以在[这里](https://gist.github.com/AdamMc331/a1dd4e8503c0cf86e0165c5f14c308ba)找到。
*   一个更深入这个主题并重用编译语句(不仅仅是编译号)的项目，可以在[这里](https://github.com/android10/Android-CleanArchitecture)找到。这是我参加的一次脸书讨论会的结果。