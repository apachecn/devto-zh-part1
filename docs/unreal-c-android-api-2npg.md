# 尝试从 Unreal C++中减少 Android API 调用的痛苦

> 原文：<https://dev.to/shiena/unreal-c-android-api-2npg>

# 前言

以前在[尝试从 UE4 的 C++呼叫 Android API 的](http://qiita.com/shiena/items/7b9d0de97a733b1a5101)中尝试从 Unreal C++呼叫 Android API，Android NDK 感觉很痛苦。 但是，UE4 作为[Unreal Plugin Language](http://qiita.com/shiena/items/fe0e4cc1de4ddbaa60f0) (以下简称为 UPL )提供了扩展安卓端的手段。 这次用它来减轻从 Unreal C++调用 Android API 的痛苦。

# 将 Java 的代码赶出 GameActivity.java

使用 AndroID NDK 时的痛苦在于必须通过`FindClass()`或`GetMethodID()`获取各 id，或者在调用方法后通过`DeleteLocalRef()`删除参照。 那样的话，Java 的代码自然会被赶出 Java 一侧吧。

以下是在 UPL 中，将获取 Pictures 路径的方法添加到 GameActivity.java 中，以了解从 C++到调用该方法的设置和代码。

## CppTest。Build.cs

在 UE4 中创建 C++项目时，必定会创建`Build.cs`。
在其中读取 UPL 的 XML。

```
// Fill out your copyright notice in the Description page of Project Settings.

using UnrealBuildTool;
// ここから追加
using System.IO;
// ここまで追加

public class CppTest : ModuleRules
{
    public CppTest(TargetInfo Target)
    {
        PublicDependencyModuleNames.AddRange(new string[] { "Core", "CoreUObject", "Engine", "InputCore" });

        PrivateDependencyModuleNames.AddRange(new string[] {  });

        // ここから追加
        if (Target.Platform == UnrealTargetPlatform.Android)
        {
            string ProjectPath = Utils.MakePathRelativeTo(ModuleDirectory, BuildConfiguration.RelativeEnginePath);
            AdditionalPropertiesForReceipt.Add(new ReceiptProperty("AndroidPlugin", Path.Combine(ProjectPath, "CppTest_UPL.xml")));
        }
        // ここまで追加
    }
} 
```

## CppTest_UPL.xml

在此文件中，将用于获取 Pictures 文件夹路径的方法添加到 GameActivity.java 中。 虽然写 XML 中的 Java 代码片段很恶心，但是我很忍耐。
详情请参考[Unreal Plugin Language 参考](http://qiita.com/shiena/items/fe0e4cc1de4ddbaa60f0)。

```
<?xml version="1.0" encoding="utf-8"?>

<root xmlns:android="http://schemas.android.com/apk/res/android">

  <init>
    <!-- APK 作成時にログを出力する -->
    <log text="CppTest init"/>
  </init>

  <!-- APK 作成時にログ出力を有効にする -->
  <trace enable="true"/>

  <gameActivityImportAdditions>
    <insert>
      import android.os.Environment;
    </insert>
  </gameActivityImportAdditions>

  <gameActivityClassAdditions>
    <insert>
      public String AndroidThunkJava_GetPicturesPath() {
          File f = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
          return f.getPath();
      }
    </insert>
  </gameActivityClassAdditions>

</root> 
```

## umyblueprintfunctionlibrary . h

头文件与[尝试从 UE4 的 C++中调用 Android API 的](http://qiita.com/shiena/items/7b9d0de97a733b1a5101)相同。

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

## umyblueprintfunctionlibrary . CPP

终于是 C++的主体了。
以前引用了 Java 端的多个类和成员变量，但这次只添加了一个方法，所以稍微轻松了一些。
如果想对 Java 的实例调用方法，我认为这已经是极限了。

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
        jclass GameActivity = FAndroidApplication::GetGameActivityThis();
        jmethodID getPicturesPathMethod = Env->GetStaticMethodID(GameCls, "AndroidThunkJava_GetPicturesPath", "()Ljava/lang/String;");

        jstring pathString = (jstring)Env->CallObjectMethod(GameActivity, getPicturesPathMethod, nullptr);
        Env->DeleteLocalRef(getPicturesPathMethod);

        const char *nativePathString = Env->GetStringUTFChars(pathString, 0);
        result = FString(nativePathString);

        Env->ReleaseStringUTFChars(pathString, nativePathString);
        Env->DeleteLocalRef(pathString);
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

# 从 GameActivity.java 中调用本机方法

下面解说游戏启动后，C++端接收不变值的情况。

## CppTest。Build.cs

`Source/CppTest/CppTest.Build.cs`是一样的，所以省略。

## CppTest_UPL.xml

这次定义本机方法`nativeSetPicturesPath()`，在 Android 的`onCreate()`方法中传递 Pictures 文件夹的路径。

```
<?xml version="1.0" encoding="utf-8"?>

<root xmlns:android="http://schemas.android.com/apk/res/android">

  <init>
    <!-- APK 作成時にログを出力する -->
    <log text="CppTest init"/>
  </init>

  <!-- APK 作成時にログ出力を有効にする -->
  <trace enable="true"/>

  <gameActivityImportAdditions>
    <insert>
      import android.os.Environment;
    </insert>
  </gameActivityImportAdditions>

  <gameActivityClassAdditions>
    <insert>
      public native void nativeSetPicturesPath(String PicturesPath);
    </insert>
  </gameActivityClassAdditions>

  <gameActivityReadMetadataAdditions>
    <insert>
      File f = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
      nativeSetPicturesPath(f.getPath());
    </insert>
  </gameActivityReadMetadataAdditions>

</root> 
```

## umyblueprintfunctionlibrary . h

由于 Pictures 文件夹的路径会通过 Java 本机方法的参数传递，因此添加了用于保存该路径的变量`PicturesPath`，不再使用 JNI 函数，因此删除了`GetPicturesPathJNI`函数。

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
    static FString PicturesPath;

    UFUNCTION(BlueprintPure, Category = "MyBPLibrary")
    static FString GetPicturesPath();
}; 
```

## umyblueprintfunctionlibrary . CPP

C++的主体只有`PicturesPath`的 getter 函数和用 Java 声明的本机方法的实现，与最初相比短了很多。

```
// Fill out your copyright notice in the Description page of Project Settings.

#include "CppTest.h"
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
extern "C"
{
    JNIEXPORT void JNICALL Java_com_epicgames_ue4_GameActivity_nativeSetPicturesPath(JNIEnv* jenv, jobject thiz, jstring picturesPath)
    {
        const char* javaChars = jenv->GetStringUTFChars(picturesPath, 0);

        UMyBlueprintFunctionLibrary::PicturesPath = FString(UTF8_TO_TCHAR(javaChars));

        //Release the string
        jenv->ReleaseStringUTFChars(picturesPath, javaChars);
    }
}
#endif 
```

# 总结

使用 UPL，可以减轻安卓 API 调用的痛苦！
因为 Android NDK 也准备了各种各样的函数，所以我想几乎没有特意叫 Android API 的机会，但我想如果能帮到谁就好了。