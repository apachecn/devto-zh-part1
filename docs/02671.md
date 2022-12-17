# kot pref:kot Lin Android 的简易共享参考库

> 原文：<https://dev.to/chibatching/kotpref-easy-sharedpreferences-library-for-kotlin-android-c68>

使用 SharedPreferences 需要一些小的样板代码。如果我们存储更多数据，或者从自动备份中包含/排除特定数据，我们应该考虑拆分 XML 文件。
[Kotpref](https://github.com/chibatching/Kotpref) 是让你简单轻松实现的库。

要安装，只需在 build.gradle.
中添加应用依赖即可

```
compile "com.chibatching.kotpref:kotpref:2.2.0" 
```

Enter fullscreen mode Exit fullscreen mode

并在应用类调用`onCreate`中的`Kotpref.init(context)`函数。

```
fun onCreate() {
    super.onCreate()
    Kotpref.init(this)
    ....
} 
```

Enter fullscreen mode Exit fullscreen mode

Kotpref 需要定义数据模型以保存到 SharedPreferences 中。可以通过如下扩展`KotprefModel`来创建数据模型。

```
object Profile : KotprefModel() {
    var name: String by stringPref()
    var age: Int by intPref(defuault = 20)
} 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以用普通的属性访问来读写了！！

```
Profile.name = "chibatching" // write
println(Profile.name) // read -> chibatching

println(Profile.age) // read -> 20
Profile.age = 32 // write
println(Profile.age) // read -> 32 
```

Enter fullscreen mode Exit fullscreen mode

通过 Kotpref 保存的 SharedPreferences XML 由类名分隔。在上例中，文件名为`Profile.xml`，内容如下。

```
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <string name="name">chibatching</string>
    <int name="age" value="32" />
</map> 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以包含/排除每个 XML 文件(KotprefModel 对象)作为[自动备份](https://developer.android.com/guide/topics/data/autobackup.html)目标。

Kotpref 核心模块只支持基本 SharedPreferences 值类型(String、Int、Long、Float、Boolean、String set)。虽然有子模块支持 kotlin enum 和 gson 序列化对象。

如果用 kotlin 开发 Android 应用就试试吧！

Kotpref 和我欢迎你的贡献！请随时给你的反馈！
[https://github.com/chibatching/Kotpref](https://github.com/chibatching/Kotpref)