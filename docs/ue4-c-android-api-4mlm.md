# 已尝试从 UE4 的 C++中调用 Android API

> 原文：<https://dev.to/shiena/ue4-c-android-api-4mlm>

# 前言

Unreal C++功能丰富，平台之间的差异也几乎被隐藏了，但有时无论如何都需要本机 API。
于是我们总结了使用 Android NDK 调用 Android API 的方法。
有几种方法，首先是完全使用 Andoird NDK 的方法。

# Preparation before

按照 Unreal Engine4 文档的[1 .安装 1.Android SDK 的](https://docs.unrealengine.com/latest/JPN/Platforms/Android/GettingStarted/1/index.html)安装 Android Works。

```
[ENGINE INSTALL LOCATION]\Engine\Extras\AndroidWorks\Win64\CodeWorksforAndroid-1R4-windows.exe 
```

利用它，可以集中安装开发 Android 所需的 Android SDK、Java 开发工具包、Ant Scripting Tool 和 Android NDK。
此处还安装了 Nsight Tegra，Visual Studio Edition 后，Visual Studio 的项目构成会有一些变化。 但是，在 UE 4.13.1 中没有太大的不同。
[安卓游戏开发参考根据](https://docs.unrealengine.com/latest/JPN/Platforms/Android/Reference/index.html)可以在设备上调试安卓游戏，所以我觉得根据需要安装比较好。

## 想叫的 Java 的代码

尝试在 Android NDK 上实现以下获取外部存储器 Pictures 文件夹路径的过程。

```
import android.os.Environment;
import java.io.File;

public String getPicturesPath() {
    File f = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
    return f.getPath();
} 
```

# 做 C++项目

Pictures 文件夹的路径不会改变，所以要建立函数库。
函数库的制作方法在 alwei 先生解说的[UE4 C++代码可以蓝印使用(函数库篇)](http://unrealengine.hatenablog.com/entry/2014/09/10/202645)中有全部记载，所以
同样是 CppTest 项目

# GetPicturesPath()を実装する

首先试着全面地安装。
JNI 的详细使用方法请参阅最后的参考链接。

```
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "Kismet/BlueprintFunctionLibrary.h"
#include "MyBlueprintFunctionLibrary.generated.h"

/**
 * 
 */
UCLASS()
class CPPTEST_API UMyBlueprintFunctionLibrary : public UBlueprintFunctionLibrary
{
    GENERATED_BODY()

public:
    UFUNCTION(BlueprintPure, Category = "MyBPLibrary")
    static FString GetPicturesPath();
}; 
```

```
// Fill out your copyright notice in the Description page of Project Settings.

#include "CppTest.h"
#include "MyBlueprintFunctionLibrary.h"

#if PLATFORM_ANDROID
#include "Android/AndroidApplication.h"
#endif

FString UMyBlueprintFunctionLibrary::GetPicturesPath()
{
    FString result;
#if PLATFORM_ANDROID
    JNIEnv* Env = FAndroidApplication::GetJavaEnv();

    if (nullptr != Env)
    {
        jclass EnvCls = Env->FindClass("android/os/Environment");
        jfieldID DirectoryPicturesField = Env->GetStaticFieldID(EnvCls, "DIRECTORY_PICTURES", "Ljava/lang/String;");
        jmethodID getExternalStoragePublicDirectoryMethod = Env->GetStaticMethodID(EnvCls, "getExternalStoragePublicDirectory", "(Ljava/lang/String;)Ljava/io/File;");

        jstring DirectoryPictures = (jstring)Env->GetStaticObjectField(EnvCls, DirectoryPicturesField);
        jobject externalStoragePublicDirectory = Env->CallStaticObjectMethod(EnvCls, getExternalStoragePublicDirectoryMethod, DirectoryPictures);
        Env->DeleteLocalRef(DirectoryPictures);
        Env->DeleteLocalRef(EnvCls);

        jclass FileCls = Env->FindClass("java/io/File");
        jmethodID getPathMethod = Env->GetMethodID(FileCls, "getPath", "()Ljava/lang/String;");
        jstring pathString = (jstring)Env->CallObjectMethod(externalStoragePublicDirectory, getPathMethod, nullptr);
        Env->DeleteLocalRef(externalStoragePublicDirectory);
        Env->DeleteLocalRef(FileCls);

        const char *nativePathString = Env->GetStringUTFChars(pathString, 0);
        result = FString(nativePathString);

        Env->ReleaseStringUTFChars(pathString, nativePathString);
        Env->DeleteLocalRef(pathString);
        Env->DeleteLocalRef(externalStoragePublicPath);
    }
    else
    {
#endif
        result = FString("");
#if PLATFORM_ANDROID
    }
#endif

    return result;
} 
```

它使用 FindClass ( )、GetStaticFieldID ( )和 GetStaticMethodID ( )来检索类、方法和字段。
然后，jclass / jobject / jstring 等对象会创建本地引用，但默认情况下最多只能使用 16 个，所以使用结束后用 DeleteLocalRef ( )删除。
有点太麻烦了。 。
而且这个代码作为 JNI 是不行的代码。
FindClass ( )、GetStaticFieldID ( )、GetStaticMethodID ( )会在内部发生反射，因此从 Blueprint 的 Tick 调用时负荷会变大，导致 APP 下降。

# 改进 JNI 调用

jclass / jfieldID / jmethodID 一旦确定就不会改变，因此可以缓存在静态变量等中。
然后使用 NewGlobalRef ( )保护不被 GC 的常用方法，以下来源提供了参考。
其他总结了 JNI 注意事项的页面记载在了最后的参考链接中，请看。

```
[ENGINE INSTALL LOCATION]\Engine\Source\Runtime\Core\Private\Android\AndroidJavaMediaPlayer.cpp
[ENGINE INSTALL LOCATION]\Engine\Source\Runtime\Core\Private\Android\AndroidMisc.cpp 
```

但是，这次想获取的 Pictures 文件夹的路径不会改变，所以可以将 JNI 实体调用函数设为 private，并将其保留在 public 函数的局部静态变量中，如下所示。

```
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "Kismet/BlueprintFunctionLibrary.h"
#include "MyBlueprintFunctionLibrary.generated.h"

/**
 * 
 */
UCLASS()
class CPPTEST_API UMyBlueprintFunctionLibrary : public UBlueprintFunctionLibrary
{
    GENERATED_BODY()

public:
    UFUNCTION(BlueprintPure, Category = "MyBPLibrary")
    static FString GetPicturesPath();

private:
    static FString GetPicturesPathJNI();
}; 
```

```
// Fill out your copyright notice in the Description page of Project Settings.

#include "CppTest.h"
#include "MyBlueprintFunctionLibrary.h"

#if PLATFORM_ANDROID
#include "Android/AndroidApplication.h"
#endif

FString UMyBlueprintFunctionLibrary::GetPicturesPath()
{
    static FString PicturesPath = UMyBlueprintFunctionLibrary::GetPicturesPath();
    return PicturesPath;
}

FString UMyBlueprintFunctionLibrary::GetPicturesPathJNI()
{
    FString result;
#if PLATFORM_ANDROID
    JNIEnv* Env = FAndroidApplication::GetJavaEnv();

    if (nullptr != Env)
    {
        jclass EnvCls = Env->FindClass("android/os/Environment");
        jfieldID DirectoryPicturesField = Env->GetStaticFieldID(EnvCls, "DIRECTORY_PICTURES", "Ljava/lang/String;");
        jmethodID getExternalStoragePublicDirectoryMethod = Env->GetStaticMethodID(EnvCls, "getExternalStoragePublicDirectory", "(Ljava/lang/String;)Ljava/io/File;");

        jstring DirectoryPictures = (jstring)Env->GetStaticObjectField(EnvCls, DirectoryPicturesField);
        jobject externalStoragePublicDirectory = Env->CallStaticObjectMethod(EnvCls, getExternalStoragePublicDirectoryMethod, DirectoryPictures);
        Env->DeleteLocalRef(DirectoryPictures);
        Env->DeleteLocalRef(EnvCls);

        jclass FileCls = Env->FindClass("java/io/File");
        jmethodID getPathMethod = Env->GetMethodID(FileCls, "getPath", "()Ljava/lang/String;");
        jstring pathString = (jstring)Env->CallObjectMethod(externalStoragePublicDirectory, getPathMethod, nullptr);
        Env->DeleteLocalRef(externalStoragePublicDirectory);
        Env->DeleteLocalRef(FileCls);

        const char *nativePathString = Env->GetStringUTFChars(pathString, 0);
        result = FString(nativePathString);

        Env->ReleaseStringUTFChars(pathString, nativePathString);
        Env->DeleteLocalRef(pathString);
        Env->DeleteLocalRef(externalStoragePublicPath);
    }
    else
    {
#endif
        result = FString("");
#if PLATFORM_ANDROID
    }
#endif

    return result;
} 
```

# 总结

在 Java 中只有几行代码在 NDK 中膨胀很多，不是很容易就能使用的。
但是请等一下。
如果使用从 UE4.10 追加的新的 Android plugin system 的话，可以在 Android 端的 Activity 上追加代码，所以可以大幅减少非本质部分的代码。
接下来我想写 Android plugin system 的使用方法。

# 参考链接

*   Unreal Engine4 文档
    *   [1 .安装安卓 SDK](https://docs.unrealengine.com/latest/JPN/Platforms/Android/GettingStarted/1/index.html)
    *   [安卓游戏开发参考](https://docs.unrealengine.com/latest/JPN/Platforms/Android/Reference/index.html)
*   Android NDK 入门、注意事项
    *   [面向安卓开发者的 JNI 入门](http://techbooster.jpn.org/andriod/application/7264/)
    *   [JNI 提示](https://developer.android.com/training/articles/perf-jni.html)
    *   [使用 Java Native Interface 的最佳实践](https://www.ibm.com/developerworks/jp/java/library/j-jni/)
*   使用 Android NDK 的插件项目
    *   [opaque multimedia/ProjectTangoPlugin](https://github.com/opaquemultimedia/ProjectTangoPlugin)
    *   [gameDNAstudio/parse platform](https://github.com/gameDNAstudio/ParsePlatform)