# 虚幻插件语言リファレンス

> 原文：<https://dev.to/shiena/unreal-plugin-language-5bpi>

# 前言

在 UE4 中编写用于 Android 的插件时，有时会想要添加第三方库或更改配置文件。
在嵌入了插件的项目中进行那样的变更很困难。
于是 Unreal Plugin Language 用构建工具进行了这些设定，即使是很讲究的插件也能轻松导入。
以下介绍使用示例和参考资料。

# Use case

认为便宜就好的人，从使用案例来看可能会更早。
安装了 GitHub 和 UE4 的文件夹都可以看到，所以记载两个路径。

※因为为了访问※UE4 的源代码需要注册，所以请参考[引擎的源代码获取和构建步骤的总结 UE4.6 修订版](http://historia.co.jp/archives/1327)。 无需注册即可查看第三方插件。

*   GitHub 的 UE4 源
    *   GearVR 插件
        *   [GearVR。Build.cs](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GearVR/Source/GearVR/GearVR.Build.cs#L41-L45)
        *   [GearVR_APL.xml](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GearVR/Source/GearVR/GearVR_APL.xml)
    *   GoogleVR 插件
        *   GoogleVRController 模块
            *   [GoogleVRController。Build.cs](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GoogleVR/GoogleVRController/Source/GoogleVRController/GoogleVRController.Build.cs#L63-L69)
            *   [Google vrcontroller _ APL . XML](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GoogleVR/GoogleVRController/Source/GoogleVRController/GoogleVRController_APL.xml)
        *   GoogleVRHMD 模块
            *   [GoogleVRHMD。Build.cs](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GoogleVR/GoogleVRHMD/Source/GoogleVRHMD/GoogleVRHMD.Build.cs#L68-L74)
            *   [GoogleVRHMD_APL.xml](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Plugins/Runtime/GoogleVR/GoogleVRHMD/Source/GoogleVRHMD/GoogleVRHMD_APL.xml)
*   安装的 UE4 的源

    *   GearVR 插件

        ```
        [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GearVR\Source\GearVR\GearVR.Build.cs
        [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GearVR\Source\GearVR\GearVR_APL.xml 
        ```

    *   GoogleVR 插件

        *   GoogleVRController 模块

            ```
            [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GoogleVR\GoogleVRController\Source\GoogleVRController\GoogleVRController.Build.cs
            [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GoogleVR\GoogleVRController\Source\GoogleVRController\GoogleVRController_APL.xml 
            ```

        *   GoogleVRHMD 插件

            ```
            [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GoogleVR\GoogleVRHMD\Source\GoogleVRHMD\GoogleVRHMD.Build.cs
            [ENGINE INSTALL LOCATION]\Engine\Plugins\Runtime\GoogleVR\GoogleVRHMD\Source\GoogleVRHMD\GoogleVRHMD_APL.xml 
            ```

*   第三方插件

    *   Tango 4 虚幻插件
        *   [TangoPlugin。Build.cs](https://github.com/opaquemultimedia/ProjectTangoPlugin/blob/master/Source/TangoPlugin/TangoPlugin.Build.cs#L55-L58)
        *   [TangoPlugin_APL.xml](https://github.com/opaquemultimedia/ProjectTangoPlugin/blob/master/Source/TangoPlugin/TangoPlugin_APL.xml)
    *   解析平台插件
        *   [ParsePlatform。Build.cs](https://github.com/gameDNAstudio/ParsePlatform/blob/master/Source/ParsePlatform/ParsePlatform.Build.cs#L60)
        *   [ParsePlatform_APL.xml](https://github.com/gameDNAstudio/ParsePlatform/blob/master/Source/ParsePlatform/ParsePlatform_APL.xml)

# 虚幻插件 Languageとは

通用语言以基于 XML 的语言提供以下功能:

*   复制文件、文件夹(主要是库和资源)
*   删除文件
*   AndroidのAndroidManifest.xml、proguard、GameActivity.javaにコード追加
*   在 iOS 的 Info.plist 中添加代码

这个 XML 用`Build.cs`读入。

## Build.cs

UPL 的对象平台有以下两种。

| 平台 | 接收属性的属性名称 |
| --- | --- |
| 机器人 | android 插件 |
| ios | IOs 插件 |

对于 Android，模仿 GearVR 或 GoogleVR 插件，用以下代码读取 XML 就可以了吧。

```
if (Target.Platform == UnrealTargetPlatform.Android)
{
    string PluginPath = Utils.MakePathRelativeTo(ModuleDirectory, BuildConfiguration.RelativeEnginePath);
    AdditionalPropertiesForReceipt.Add(new ReceiptProperty("AndroidPlugin", Path.Combine(PluginPath, "CppTest_APL.xml")));
} 
```

## 虚幻插件 Languageのリファレンス

目前，参考资料只是在[UnrealPluginLanguage.cs](https://github.com/EpicGames/UnrealEngine/blob/master/Engine/Source/Programs/UnrealBuildTool/System/UnrealPluginLanguage.cs) 中用注释记述的内容，以此为基础进行解说。

### 空定义的 XML

```
<?xml version="1.0" encoding="utf-8"?>
<root xmlns:android="http://schemas.android.com/apk/res/android">
<!-- ここに追加していく -->
</root> 
```

### 调试

```
<trace enable="true"/> 
```

可以设定是否启用日志。

* * *

```
<dumpvars/> 
```

将在 XML 中设定的变量值输出到日志中。

* * *

```
<log text="..."/> 
```

将 text 指定的消息输出到日志。
在各个部分输出的话，很容易确认动作。

## number

*   `$B(name)` : boolean
*   `$I(name)`:整数
*   `$S(name)`:字符串
*   `$E(name)` : XMLのelement

可以将变量`name`作为各个类型的值进行参照。

## 已定义的变量

*   `$S(Architecture)`:体系结构名称(`armeabi-armv7a`、`arm64-v8a`、`x86`、`x86_64`)
*   `$S(PluginDir)`:读取了 XML 文件的文件夹名称
*   `$S(EngineDir)`:引擎的文件夹名称
*   `$S(BuildDir)`:每个平台的构建文件夹名称(在 Intermediate 文件夹中)
*   `$S(ProjectDir)`:项目的文件夹名称
*   `$B(Distribution)`:如果是分散构建，则设置为 true。
*   `$S(Output)`:后述的每个章节都会设定其内容，所以可以动态变更。

## 共同的部分

### 初始化

```
<init></init> 
```

每个体系结构只运行一次的部分。
可以定义和操作变量，可以用`<log text="..."/>`进行日志记录。

## 安卓用的部分

以下部分在`ReceiptProperty()`中指定`AndroidPlugin`后适用。

### androidmanifestUpdates

```
<androidManifestUpdates>
    <addPermission android:name="..."/>
    <addFeature android:name="..."/>
    <addLibrary android:name="..."/>

    <addElement tag="..." name="..."/>
    <removeElement tag="..."/>
    <addElements tag="..."></addElements>

    <addAttribute tag="..." name="..." value="..."/>
    <removeAttribute tag="..." name="..."/>

    <loopElements tag="..."></loopElements>
</androidManifestUpdates> 
```

在`[PROJECT LOCATION]/Intermediate/Android/APK/AndroidManifest.xml`中添加`<uses-permission>`、`<uses-feature>`、`<uses-library>`。
另外，还可以添加或删除任意的要素、属性。

### proguardAdditions

```
<proguardAdditions>
    <insert>...</insert>
</proguardAdditions> 
```

标签内操作的内容反映在`[PROJECT LOCATION]/Intermediate/Android/APK/proguard-project.txt`中。

### 预构建副本

```
<prebuildCopies>
    <copyFile src="..." dst="..."/>
    <deleteFiles filespec="..."/>
</prebuildCopies> 
```

用于在构建前复制 ndk-build 所需的库和文件。

### 资源副本

```
<resourceCopies>
    <copyFile src="..." dst="..."/>
    <deleteFiles filespec="..."/>
</resourceCopies> 
```

用于在 ndk-build 之后复制所需的文件。
Java 的内部版本是在制作 APK 时，所以要添加的 Java 源文件就这样复制即可。

### [T1】AAR imports](#aarimports)

```
<AARImports>
    <insert>...</insert>
</AARImports> 
```

因为`aar-imports.txt`被读取到`$(Output)`中，所以可以用`<insert></insert>`等追加 AAR。

### GameActivity.java 更新用的部分

将 Android 添加到构建目标时，将复制以下 Java 源文件:

*   复制来源:`[ENGINE INSTALL LOCATION]\Engine\Build\Android\java\src\com\epicgames\ue4\GameActivity.java`
*   复制到:`[PROJECT LOCATION]\Intermediate\Android\APK\src\com\epicgames\ue4\GameActivity.java`

此文件包含几个占位符，用于在创建 APK 时添加您自己的代码，并且可以使用以下标记:

**注意**
`GameActivity.java`生成后，即使变更 XML 的 Java 代码也不会反映出来。
删除复制对象的`GameActivity.java`后，在下一个 APK 制作时将反映变更。

#### gameactivitymportadditions

```
<gameActivityImportAdditions>
    <insert>...</insert>
</gameActivityImportAdditions> 
```

在 GameActivity 中添加 import 时使用。
复制源的占位符为`$${gameActivityImportAdditions}$$`。

#### 游戏活动类添加

```
<gameActivityClassAdditions>
    <insert>...</insert>
</gameActivityClassAdditions> 
```

用于向 GameActivity 添加成员变量和成员方法。
复制源的占位符为`$${gameActivityClassAdditions}$$`。

#### 游戏活动开始添加

```
<gameActivityOnStartAdditions>
    <insert>...</insert>
</gameActivityOnStartAdditions> 
```

在 GameActivity 的`onStart()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnStartAdditions}$$`。

#### 游戏活动 ReadMetadataAdditions

```
<gameActivityReadMetadataAdditions>
    <insert>...</insert>
</gameActivityReadMetadataAdditions> 
```

通过 GameActivity 的`onCreate()`从 AndroidManifest.xml 读取设定时使用。
复制源的占位符为`$${gameActivityReadMetadataAdditions}$$`。

#### 游戏活动 OnCreateAdditions

```
<gameActivityOnCreateAdditions>
    <insert>...</insert>
</gameActivityOnCreateAdditions> 
```

在 GameActivity 的`onCreate()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnCreateAdditions}$$`。

#### 游戏活动

```
<gameActivityOnDestroyAdditions>
    <insert>...</insert>
</gameActivityOnDestroyAdditions> 
```

在 GameActivity 的`onDestroy()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnDestroyAdditions}$$`。

#### 游戏活动异常

```
<gameActivityOnStopAdditions>
    <insert>...</insert>
</gameActivityOnStopAdditions> 
```

在 GameActivity 的`onStop()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnStopAdditions}$$`。

#### 游戏活动缺席加

```
<gameActivityOnPauseAdditions>
    <insert>...</insert>
</gameActivityOnPauseAdditions> 
```

在 GameActivity 的`onPause()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnPauseAdditions}$$`。

#### 游戏活动

```
<gameActivityOnResumeAdditions>
    <insert>...</insert>
</gameActivityOnResumeAdditions> 
```

在 GameActivity 的`onResume()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnResumeAdditions}$$`。

#### 游戏活动活性和结果相加

```
<gameActivityOnActivityResultAdditions>
    <insert>...</insert>
</gameActivityOnActivityResultAdditions> 
```

在 GameActivity 的`onActivityResult()`中追加处理时使用。
复制源的占位符为`$${gameActivityOnActicityResultAdditions}$$`。

#### soLoadLibrary

```
<soLoadLibrary>
    <loadLibrary name="..." failmsg="..."/>
    <insert>...</insert>
</soLoadLibrary> 
```

用于添加要在 GameActivity 中加载的本机库。
想进行细致的处理时也可以使用`<insert></insert>`。
复制源的占位符为`$${soLoadLibrary}$$`。

## iOS 用的部分

以下部分在`ReceiptProperty()`中指定`IOSPlugin`后适用。

#### iosPListUpdates

```
<iosPListUpdates></iosPListUpdates> 
```

将此部分的内容导出为`[PROJECT LOCATION]/Intermediate/IOS/[GAME NAME]-Info.plist`。

## 各种操作

### Number definition

```
<setBool result="..." value="..."/>
<setInt result="..." value="..."/>
<setString result="..." value="..."/>
<setElement result="..." value="..."/>
<setElement result="..." xml="..."/> 
```

将`value`的值代入`result`指定的变量。

* * *

```
<setBoolFromProperty result="..." ini="..." section="..." property="..." default="..."/>
<setIntFromProperty result="..." ini="..." section="..." property="..." default="..."/>
<setStringFromProperty result="..." ini="..." section="..." property="..." default="..."/> 
```

从 ini 文件读取值。
不存在文件、章节、属性时代入`default`。

### calculus

```
<setBoolNot result="..." source="..."/>
<setBoolAnd result="..." arg1="..." arg2="..."/>
<setBoolOr result="..." arg1="..." arg2="..."/>
<setBoolIsEqual result="..." arg1="..." arg2="..."/>
<setBoolIsLess result="..." arg1="..." arg2="..."/>
<setBoolIsLessEqual result="..." arg1="..." arg2="..."/>
<setBoolIsGreater result="..." arg1="..." arg2="..."/>
<setBoolIsGreaterEqual result="..." arg1="..." arg2="..."/> 
```

将逻辑运算的结果代入`result`指定的变量。

* * *

```
<setIntAdd result="..." arg1="..." arg2="..."/>
<setIntSubtract result="..." arg1="..." arg2="..."/>
<setIntMultiply result="..." arg1="..." arg2="..."/>
<setIntDivide result="..." arg1="..." arg2="..."/> 
```

将算术运算的结果代入`result`指定的变量。

* * *

```
<setStringAdd result="..." arg1="..." arg2="..."/>
<setStringSubstring result="..." source="..." start="..." length="..."/>
<setStringReplace result="..." source="..." find="..." with="..."/> 
```

将字符串操作的结果代入`result`指定的变量。

* * *

```
<setIntLength result="..." source="..."/> 
```

将`source`的字符串长度代入`result`指定的变量。

* * *

```
<setIntFindString result="..." source="..." find="..."/> 
```

将`source`到`find`的字符串一致的索引代入`result`指定的变量。

* * *

```
<setBoolStartsWith result="..." source="..." find="..."/>
<setBoolEndsWith result="..." source="..." find="..."/>
<setBoolContains result="..." source="..." find="..."/> 
```

将分别以`find`指定的字符串开始、结束、是否包含的检查结果代入`result`指定的变量。

### 操作 Output 变量

```
<replace find="..." with="..."/> 
```

用`with`的字符串置换与变量`$(Output)`到`find`一致的字符串。

* * *

```
<insert>...</insert>
<insertValue value="..."/>
<insertNewLine/> 
```

在变量`$(Output)`中添加标签内的字符。
主要用于 proguardAdditions 和 GameActivity.java 部分。

### Imperial structure

```
<if condition="...">
    <true>
        <!-- executes if boolean variable in condition is true -->
    </true>
    <false>
        <!-- executes if boolean variable in condition is false -->
    </false>
</if> 
```

可以用`condition`指定条件，用`true`标签和`false`标签设定各自的处理。

* * *

```
<isArch arch="armeabi-armv7">
    <!-- do stuff -->
</isArch> 
```

可以检查体系结构的快捷节点。 这个可以进行与以下同等的操作。

```
<setBoolEquals result="temp" arg1="$S(Architecture)" arg2="armeabi-armv7">
<if condition="temp">
    <true>
        <!-- do stuff -->
    </true>
</if> 
```

* * *

```
<isDistribution>
    <!-- do stuff -->
</isDistribution> 
```

可以检查是否是分布式构建。
这也和`isArch`一样是快捷方式。

* * *

```
<return/> 
```

处理在此结束。

* * *

```
<while condition="...">
    <!-- do stuff -->
</while>

<break/>
<continue/> 
```

是循环处理。 还有`break`和`continue`。

### XML operation

```
<addElement tag="..." name="..."/>
<addElements tag="..."> body </addElements>
<removeElement tag="..."/>
<setStringFromTag result="..." tag="..." name="..."/>
<setStringFromAttribute result="..." tag="..." name="..."/>
<addAttribute tag="..." name="..." value="..."/>
<removeAttribute tag="..." name="..."/>
<loopElements tag="..."> instructions </loopElements> 
```

可以添加和删除元素、添加和删除属性、获取元素和属性值，以及对重复元素执行操作。
主要用于设定像 AndroidManifest.xml 的 intent-filter 这样没有特定操作的要素。

# 总结

从整体上看，量很大，但我认为是为了给大家一个灵活的定义。
例如，想在 Android 端添加逻辑的时候，只能使用 GameActivity 的部分。
另外，即使不是插件，也可以在项目单体中使用，所以可以使用的范围很广。