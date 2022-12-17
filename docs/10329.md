# 使用 Gradle 创建可执行的 Kotlin JARs

> 原文：<https://dev.to/preslavrachev/create-executable-kotlin-jars-using-gradle>

*本文原载于[我的博客](http://www.preslav.me/2016/06/13/kotlin-basics-create-an-executable-kotlin-jar-using-gradle/)T3*

Kotlin 非常适合创建小型命令行实用程序，它们可以作为普通的 JAR 文件打包和分发。这个简短的教程将向您展示如何:

*   建立一个支持 Kotlin 的 Gradle 项目
*   添加启动功能
*   将您的构建配置为在执行 JAR 时调用这个函数。

### 设置 Kotlin 依赖关系

```
apply plugin: 'java'
apply plugin: 'kotlin'

//… other stuff, you typically find in a Gradle build file

dependencies {
     // other dependencies …
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}

buildscript {
     ext.kotlin_version = '1.0.2'
     //...
     compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
} 
```

Enter fullscreen mode Exit fullscreen mode

您还会希望您的 IDE(我假设您使用的是 IntelliJ 或 Android Studio)将 Kotlin 源代码所在的目录标记为源目录。因为 Kotlin 和 Java 是最好的朋友，所以保持相同的包结构是非常好的。这是一个很好的实践，尽管要保持 Kotlin 代码与 Java 代码在物理上是分开的。因此，对于 Java 类，通常在`src` - `src/main/java`下有两个文件夹，对于 Kotlin 类，在`src/main/kotlin`下有两个文件夹。测试也一样。同样，在您的`build.gradle`文件中，添加以下内容:

```
sourceSets {
    main.java.srcDirs += 'src/main/kotlin/'
    test.java.srcDirs += 'src/test/kotlin/'
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 IntelliJ，您可以不依赖 ide 来指导您完成所有这些，但是我想向您展示一些基础知识，因为人们可能不总是依赖 IDE 的舒适性。要看到一切都正常工作，请转到您的项目目录并创建一个新的构建:

```
grade clean build 
```

Enter fullscreen mode Exit fullscreen mode

如果一切都设置正确，您应该能够看到一个名为`compileKotlin`的任务，构建已经成功执行了该任务。

### 编写你的第一个 Kotlin 程序

与 Java 不同，Kotlin 对驻留在任何类范围之外的函数更友好。你可以创建一个托管一个`main()`函数的`Main`类，或者你可以创建一个顶级的`main()`，而不需要将它包装在一个类中。也许，您无法在如此简短的示例中找到任何差异，但是我发现创建顶级函数的可能性有助于减少样板代码。

下面是强制 HelloWorld 示例。用一个任意的名字(比如说`Main`)和一个扩展名`.kt`创建一个文件，简单地写:

```
fun main(args : Array<String>) { 
  println("Hello, world!") 
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，添加包是可选的，也可以用分号结束行。不过，为了与我的 Java 代码保持一致，我通常会两者都添加，并希望我的同事也这样做。

### 配置您的 Gradle build 以创建一个可执行的 JAR

我们刚刚添加的`main`函数足以测试设置一个可执行的 JAR，然后应该能够简单地通过执行:
来调用它

```
java -jar <MY_PROJECT_NAME>.jar 
```

Enter fullscreen mode Exit fullscreen mode

如果您只是尝试构建您的项目，然后执行上面的命令，我们会得到下面的消息:

```
no main manifest attribute <PATH_TO_MY_PROJECT_JAR>.jar 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们必须配置`jar`任务，Java Gradle 构建要经过这个任务，并告诉它我们项目的起点在哪里。在 Java 项目中，这将是我们的`main()`函数所在的类的路径:

```
jar {
    manifest {
        attributes 'Main-Class': 'com.myname.myprojectname.Main'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

等一下？我们已经定义了任何类范围的函数 outisde。这是真的，但同时也不完全是真的。实际上，为了保持字节码级别的一致性，并与 JVM 向后兼容，Kotlin 编译器将所有顶级函数添加到各自的类中。在我们的例子中，由 Kotlin 编译器生成的类将与我们的函数所在的文件同名，加上后缀`Kt`。这意味着，例如，如果我们的文件名为`Main.kt`，Kotlin 编译器将生成一个名为`MainKt.class`的类，并将其添加到生成的 JAR 文件中。知道了这一点，我们可以重写上面的 Gradle 配置，如下:

```
jar {
    manifest {
        attributes 'Main-Class': 'com.myname.myprojectname.MainKt'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**你可以指定这个类应该被编译的名字，通过在你的文件上添加一个文件范围的注释，甚至在`package`声明之前:

```
@file:JvmName("MainCls") 
```

Enter fullscreen mode Exit fullscreen mode

这个新名称可以在 JAR 清单配置中使用，如上所示。

即使我们在 JAR 清单配置中正确地指定了我们的主类，如果我们试图使用`jar -jar`来执行我们的主函数，我们仍然会看到一条错误消息:

```
Exception in thread "main" java.lang.NoClassDefFoundError: kotlin/jvm/internal/Intrinsics
    at com.preslavrachev.imdbparser.MainKt.main(Main.kt)
Caused by: java.lang.ClassNotFoundException: kotlin.jvm.internal.Intrinsics
    at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
    at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:331)
    at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
    ... 1 more 
```

Enter fullscreen mode Exit fullscreen mode

有经验的 Java 开发人员会很快识别出这种类型的异常。默认情况下，当 Gradle(以及 Maven)将一些 Java 类文件打包到一个 JAR 文件中时，假设这个 JAR 文件将被一个应用程序引用，其中它的所有依赖项也可以在加载应用程序的类路径中访问。要执行 JAR 而不必指定其自身依赖项的路径，必须告诉构建系统获取这个 JAR 的所有引用依赖项，并将它们作为 JAR 本身的一部分进行复制。在 Java 社区中，这被称为“胖罐子”。在“胖罐子”中，所有的依赖关系都在加载应用程序的类路径中结束，所以代码可以毫无问题地执行。创建 fat JARs 的唯一缺点当然是它们不断增长的文件大小(这也解释了它的名字)，尽管在大多数情况下，这不是一个大问题。为了告诉 Gradle 复制一个 JAR 的所有依赖项，应该简单地修改上面提到的 JAR 任务配置，添加以下代码:

```
jar {
    manifest {
        attributes 'Main-Class': 'com.preslavrachev.imdbparser.MainKt'
    }

    // This line of code recursively collects and copies all of a project's files
    // and adds them to the JAR itself. One can extend this task, to skip certain 
    // files or particular types at will
    from { configurations.compile.collect { it.isDirectory() ? it : zipTree(it) } }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 进一步链接

*   [Gradle–创建一个具有依赖关系的 Jar 文件](http://www.mkyong.com/gradle/gradle-create-a-jar-file-with-dependencies/)
*   [使用 Gradle | stack overflow 创建可运行的 JAR](http://stackoverflow.com/questions/21721119/creating-runnable-jar-with-gradle)