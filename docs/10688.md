# Unreal C++可以通过 Android 访问的文件夹有限，很痛苦(有解决方法)

> 原文：<https://dev.to/shiena/unreal-c-android-1k4b>

# 前言

用 Unreal C++访问 Android 的任意路径很麻烦。

# 导入 Victory Plugin 很痛苦

在“[尝试从 Unreal C++中减少 Android API 调用的痛苦性的](http://qiita.com/shiena/items/f07533702e2639a511c3)”中，现在可以从 Blueprint 中获取 Pictures 文件夹的路径，接下来将尝试获取该文件夹中图像的文件列表
幸运的是，[Victory Plugin](https://github.com/EverNewJoy/VictoryPlugin) 中有正好的函数。

[![VictoryPlugin.png](img/5aa710c4fcc1da463d8f01e133ccbf16.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JdZegjn9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/wonc5spi0xft0xbb5bw9.png)

但是，在 Windows 中，如果为了 Android 而构建，则可以正常工作，则会出错。
这是官方论坛的[Rama ' s extra blue print nodes for you as a plugin，No C++ Required！](https://forums.unrealengine.com/showthread.php?3851-(39)-Rama-s-Extra-Blueprint-Nodes-for-You-as-a-Plugin-No-C-Required!&p=53249&viewfull=1#post53249) 有解决方案，在 Build.cs 中添加设置后，内部版本现在会通过。
为什么其他插件不需要追加设定也可以构建…

```
// Fill out your copyright notice in the Description page of Project Settings.

using UnrealBuildTool;
using System.IO;

public class CppTest : ModuleRules
{
    public CppTest(TargetInfo Target)
    {
        PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore" });

        PrivateDependencyModuleNames.AddRange(new string[] {  });
        if (Target.Platform == UnrealTargetPlatform.Android)
        {
            string ProjectPath = Utils.MakePathRelativeTo(ModuleDirectory, BuildConfiguration.RelativeEnginePath);
            AdditionalPropertiesForReceipt.Add(new ReceiptProperty("AndroidPlugin", Path.Combine(ProjectPath, "CppTest_release_UPL.xml")));

            // ここから追加
            PrivateIncludePaths.AddRange(new string[] { "CppTest/Plugins/VictoryPlugin/Source/VictoryBPLibrary/Private" });
            DynamicallyLoadedModuleNames.AddRange(new string[] { "VictoryBPLibrary" });
            // ここまで追加
        }
        // Uncomment if you are using Slate UI
        // PrivateDependencyModuleNames.AddRange(new string[] { "Slate", "SlateCore" });

        // Uncomment if you are using online features
        // PrivateDependencyModuleNames.Add("OnlineSubsystem");

        // To include OnlineSubsystemSteam, add it to the plugins section in your uproject file with the Enabled attribute set to true
    }
} 
```

# fandroidplatformfile::pathtoandroidpaths()の罠

因为导入了 Victory Plugin，所以接下来试着适当地取了 png 文件的一览。

[![JoyFileIOGetFiles.png](img/839dd25ff5bec6a8319f78c491ff956d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kYD_Xmt3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/a134aedforr4zsdir42j.png)

但是，`Joy File Get Files`函数的结果是，Return Value 为 true，文件名的数组却为 0。
到底还是不知道原因，试着追了一下调味汁。 在 Android 依赖部分的[Fandroidplatformfile:: iteratedirectory ( )](https://github.com/EpicGames/UnrealEngine/blob/4.13.2-release/Engine/Source/Runtime/Core/Private/Android/AndroidFile.cpp#L1509)中未发现问题，因此启用了最终手段的调试日志。

```
diff --git a/Engine/Source/Runtime/Core/Private/Android/AndroidFile.cpp b/Engine/Source/Runtime/Core/Private/Android/AndroidFile.cpp
index 104d4e6..8b8e737 100644 --- a/Engine/Source/Runtime/Core/Private/Android/AndroidFile.cpp +++ b/Engine/Source/Runtime/Core/Private/Android/AndroidFile.cpp @@ -20,7 +20,7 @@

 DEFINE_LOG_CATEGORY_STATIC(LogAndroidFile, Log, All);

-#define LOG_ANDROID_FILE 0
+#define LOG_ANDROID_FILE 1 
 #define LOG_ANDROID_FILE_MANIFEST 0 
```

从只变更了上述内容的源构建的 UE4 构建 Android APP，用`adb logcat`确认日志。

```
D UE4     : FAndroidPlatformFile::IterateDirectory('/storage/emulated/0/Pictures')
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('/storage/emulated/0/Pictures') => AndroidPath = '/storage/emulated/0/Pictures'
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('/storage/emulated/0/Pictures') => LocalPath = '/storage/emulated/0/UE4Game/CppTest/storage/emulated/0/Pictures'
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('/storage/emulated/0/Pictures') => AssetPath = '/storage/emulated/0/Pictures'
D UE4     : [2016.11.03-09.15.36:314][  0]LogBlueprintUserMessages: [M_Default_C_1] 0 
```

*   Pictures 文件夹的完整路径:`/storage/emulated/0/Pictures`
*   用`FAndroidPlatformFile::PathToAndroidPaths`变换后的 Pictures 文件夹的完整路径:`/storage/emulated/0/UE4Game/CppTest/storage/emulated/0/Pictures`

嗯——一边想着一边再次调查源代码，即使指定了绝对路径，好像也会转换成以游戏用文件夹为根的路径……这是怎么回事……

即使再指定相对路径，也会被删除如下。 精明…

```
while (AndroidPath.StartsWith(TEXT("../")))
{
    AndroidPath = AndroidPath.RightChop(3);
} 
```

而且，从 IAndroidPlatformFile 类继承和复盖的成员函数总是会进行此转换，因此会影响吸收 FPaths 和 FFileHelper 等平台差异的类。

# 支票的回避策略

虽然知道了绝对传球和相对传球都不行，但是只有一种方式有了出路。
是相对于当前目录的相对路径。 具体情况如下。

*   希望参照的路径:`/storage/emulated/0/Pictures`
*   参数中传递的相对路径:`./../../Pictures`

这样确认了日志后…

```
D UE4     : FAndroidPlatformFile::IterateDirectory('./../../Pictures')
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('./../../Pictures') => AndroidPath = './../../Pictures'
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('./../../Pictures') => LocalPath = '/storage/emulated/0/UE4Game/CppTest/./../../Pictures'
D UE4     : FAndroidPlatformFile::PathToAndroidPaths('./../../Pictures') => AssetPath = './../../Pictures'
D UE4     : FAndroidPlatformFile::IterateDirectory('./../../Pictures').. LOCAL Visit: './../../Pictures/Screenshot_20160610-201604.png'
D UE4     : FAndroidPlatformFile::IterateDirectory('./../../Pictures').. LOCAL Visit: './../../Pictures/0.png'
D UE4     : [2016.11.03-09.15.36:315][  0]LogBlueprintUserMessages: [M_Default_C_1] 2 
```

拿到了！ 取得了 Pictures 文件夹的 png 文件的路径！

也就是说，以`/storage/emulated/0/UE4Game/CppTest`为起点，制作与当前目录的相对路径即可。
然后使用似乎是为此而准备的[FPaths::MakePathRelativeTo](https://docs.unrealengine.com/latest/INT/API/Runtime/Core/Misc/FPaths/MakePathRelativeTo/index.html) 就可以简单地制作相对路径。
(原来源为[myblueprintfunctionlibrary.CPP](http://qiita.com/shiena/items/f07533702e2639a511c3#umyblueprintfunctionlibrarycpp-1))

```
// Fill out your copyright notice in the Description page of Project Settings.

#include "CppTest_release.h"
#include "MyBlueprintFunctionLibrary.h"

#if PLATFORM_ANDROID
#include "Android/AndroidApplication.h"
#endif

FString UMyBlueprintFunctionLibrary::PicturesPath;

FString UMyBlueprintFunctionLibrary::GetPicturesPath()
{
    return UMyBlueprintFunctionLibrary::PicturesPath;
}

#if PLATFORM_ANDROID
/*
 * AndroidFile.cppに定義されている /storage/emulated/0/UE4Game/CppTest を返す関数
 * AndroidFile.hに定義されていないので、externで宣言しています。
 */
extern const FString &GetFileBasePath();

/*
 * 参照したい絶対パスを渡すと相対パスに変換する
 */
bool AndroidRelativePath(FString InPath, FString& OutPath)
{
    auto Source = InPath;
    auto InRelativeTo = GetFileBasePath();
    auto Result = FPaths::MakePathRelativeTo(Source, *InRelativeTo);
    OutPath = FPaths::Combine(TEXT("."), *Source);

    return Result;
}

extern "C"
{
    JNIEXPORT void JNICALL Java_com_epicgames_ue4_GameActivity_nativeSetPicturesPath(JNIEnv* jenv, jobject, jstring picturesPath)
    {
        const char* javaChars = jenv->GetStringUTFChars(picturesPath, 0);

        // Picturesの絶対パス
        auto RawPicturesPath = FString(UTF8_TO_TCHAR(javaChars));

        /*
         * ◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
         * ここでPicturesの絶対パスを相対パスに変換する
         * ◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆◆
         */
        AndroidRelativePath(RawPicturesPath, UMyBlueprintFunctionLibrary::PicturesPath);

        jenv->ReleaseStringUTFChars(picturesPath, javaChars);
    }
}
#endif 
```

但是，此方法是依赖于 AndroidFile.cpp 实现的通道，在以后的版本升级中可能无法使用。

# 总结

为什么这样限制呢，因为在 GitHub 的提交日志中也没有特别记载所以不知道，但是为了保存保存文件等，需要 SD 卡的权限，因为没有保护其他文件的机制，所以不能轻易访问。

用 UE4 做安卓 APP 很痛苦。