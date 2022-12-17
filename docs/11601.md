# 在 Gradle 上进行多项目

> 原文：<https://dev.to/shiena/gradle-1fho>

Gradle 不仅可以处理单一项目，还可以将多个项目一起处理，称为多项目。
多项目有几种布局，所以以以下 3 个项目为例进行写。

*   `root`用于汇总项目的项目
*   `share`集中了通用功能的项目
*   `application`在有 APP 应用的项目中依赖于`share`项目。

`build.gradle`的写法详细[Gradle User Guide/7.3 .多项目的 Java 内部版本](http://gradle.monochromeroad.com/docs/userguide/tutorial_java_projects.html#sec:examples)等，在此省略。

## 分层结构的布局

`root`在下属配置了`share`和`application`，布局看起来很容易理解。
Gradle 的构建文件`build.gradle`和`settings.gradle`配置在`root`的正下方。

```
root/
  share/
  application/
  build.gradle
  settings.gradle 
```

在`settings.gradle`中，在`include`后面写上项目名称。

```
include 'share', 'application' 
```

## 平面布局

为了便于在 Eclipse 等中处理，将所有项目配置在同一层的布局。
`build.gradle`和`settings.gradle`保持不变，`share`和`application`与`root`处于同一层次。
但是，在该布局中，除`root`以外无法构建。

```
root/
  build.gradle
  settings.gradle
share/
application/ 
```

`settings.gradle`只是将`include`变更为`includeFlat`。

```
includeFlat 'share', 'application' 
```

### 使用 master 的平面布局

**※ Remember**

在平面布局的特别模式中，只要将`root`目录的名称设为`master`，就可以从其他项目中建立。
在这种情况下，如果在同一阶层的`master`目录中有`settings.gradle`，则可能会被作为多项目处理。

参考:[登录用户指南/第 53 章构建的生命周期/53.4 .初始化](http://gradle.monochromeroad.com/docs/userguide/build_lifecycle.html#sec:initialization)

```
master/
  build.gradle
  settings.gradle
share/
application/ 
```

## 另一种平面布局

即使是平面布局，也可以在各个项目中构建。
从最初的平面布局开始，再将`settings.gradle`配置在与各项目相同的阶层。

```
root/
  build.gradle
share/
application/
settings.gradle 
```

在`settings.gradle`中设置根项目的名称和目录。
Gradle 在当前目录中没有`settings.gradle`时，会依次查找上位目录，因此可以从任何项目建立。

```
rootProject.name = 'root'
rootProject.projectDir = new File(settingsDir, 'root')

includeFlat 'share', 'application' 
```