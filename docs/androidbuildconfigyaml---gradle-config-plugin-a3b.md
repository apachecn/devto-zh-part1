# 希望安卓根据 Yaml 而不是 BuildConfig 自动生成 APP 常量- Gradle Config Plugin

> 原文：<https://dev.to/chuross/androidbuildconfigyaml---gradle-config-plugin-a3b>

## Weft

### 想做的事

*   想统一管理 APP 中使用的常数值
*   每个 ProductFlavor 都想改变 API 的目的地等设定信息
*   我不想自己开一个管理它们的班

### 不使用 BuildConfig 的理由

*   build.gradle 变得肥大
*   常数的记述多少有些麻烦
    *   因为必须一一记述类型，所以很麻烦
    *   我也不喜欢用字符串填充所有的值

## 梯度配置插件

[https://github.com/tmiyamon/gradle-config](https://github.com/tmiyamon/gradle-config)

## Special

*   可以为每个产品布局定义 Yaml
*   从 Yaml 自动生成用于引用值的类
    *   值类型会根据 Yaml 的记述内容自动解决

## import

### 在 buildscript 中添加插件

```
buildscript {
  repositories {
    maven {
      url "https://plugins.gradle.org/m2/"
    }
  }
  dependencies {
    classpath "gradle.plugin.com.tmiyamon:gradle-config:0.2.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 应用插件

在有 APP 项目的 build.gradle 上追加以下内容
( app/build.config )

`apply plugin: 'com.tmiyamon.config'`

## 使用方法

### 制作配置文件

在 APP 的项目根目录中添加`config`目录

文件名对应以下内容

```
config/default.yml
config/default_secret.yml
config/${productFlavor}.yml
config/${productFlavor}_secret.yml
config/${buildType}.yml
config/${buildType}_secret.yml 
```

Enter fullscreen mode Exit fullscreen mode

根据 ProductFlavor 适当地读入这些文件，生成一个类

`*_seciet.yml`的文件可以放入保密信息。 使用时，为了不置于版本控制之下，请放入`.gitignore`

### 在 Yaml 中填入项目

接下来只需要做 YAML

```
size: 2
server: google.com
section:
  size: 3
  servers: [ {name: yahoo.com}, {name: amazon.com} ] 
```

Enter fullscreen mode Exit fullscreen mode

这样记述的话，构建时会生成名为**Settings** 的类

生成的类可以这样参照值

```
Settings.size   // => 2
Settings.server // => google.com
Settings.section.size // => 3
Settings.section.servers.get(0).name // => yahoo.com
Settings.section.servers.get(1).name // => amazon.com 
```

Enter fullscreen mode Exit fullscreen mode

convenience

## Use

### 使用 kotlin 的扩展，制作组合了常数和自变量的字符串

例如只将基础的 URL 写入 Yaml 中，通过 kotlin 的扩展来实现路径的组合

```
url:
  base: https://hoge.com 
```

Enter fullscreen mode Exit fullscreen mode

接着在 kotlin 端扩展属性

```
fun Settings.Url.getDetail(id: String) = "$base/detail/$id" 
```

Enter fullscreen mode Exit fullscreen mode

这样就可以在 APP 上参照了

```
Settings.url.getDetail("1") // => https://hoge.com/detail/1 
```

Enter fullscreen mode Exit fullscreen mode

## 后记

因为设定信息是和什么东西细致组合，记述很多的部分，所以想变得容易管理

这个插件被认为是满足这些部分要求的好方法