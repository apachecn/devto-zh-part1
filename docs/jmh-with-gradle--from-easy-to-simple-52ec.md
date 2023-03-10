# JMH 与格拉德——从简单到简单

> 原文：<https://dev.to/o_a_e/jmh-with-gradle--from-easy-to-simple-52ec>

[JMH](http://openjdk.java.net/projects/code-tools/jmh/) 是*一个 Java 工具，用于构建、运行和分析用 Java 和其他语言编写的针对 JVM* 的纳米/微米/毫/宏基准。

Gradle 是我目前选择的构建工具。

这篇文章讲述了如何在 gradle 中设置 JMH。

从简单的方法开始。只要包含并使用这个插件:[https://github.com/melix/jmh-gradle-plugin](https://github.com/melix/jmh-gradle-plugin)。

我用了很长一段时间，对于较小的项目来说，它开箱即用。在中型电脑上，我会遇到一些问题，但是可以通过使用它的配置来解决这些问题(比如 **zip64 = true** )。然而，对于一个具有复杂依赖设置的大型项目，我遇到了一些额外的类路径问题。

所以我后退一步，看看这个项目在做什么，也看了一眼 http://gvsmirnov . ru/blog/tech/2014/03/10/keeping-your-benchmarks-separate . html,然后想…它并不复杂…甚至可以更简单…例如，我不明白为什么我需要那个 shadow jar 的东西！

#### 循序渐进

常见的做法是将 JMH 性能指标评测分离出来，例如，不是将它们放在 **src/test/java** 中，而是放在 **src/jmh/java** 中。在 gradle 中添加另一个源集:

```
sourceSets { 
  jmh 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你的基准测试使用了测试中的类，那么就这样做:

```
sourceSets { 
    jmh { 
        compileClasspath += sourceSets.test.runtimeClasspath 
        runtimeClasspath += sourceSets.test.runtimeClasspath 
        } 
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要为名为 **jmh** 的新源集设置依赖关系。所以你的基准所依赖的一切，通常是项目本身(及其所有的依赖)和 JMH 罐子:

```
dependencies { 
    jmhCompile project 
    jmhCompile 'org.openjdk.jmh:jmh-core:1.18' 
    jmhAnnotationProcessor 'org.openjdk.jmh:jmh-generator-annprocess:1.18' 
} 
```

Enter fullscreen mode Exit fullscreen mode

注意: **jmhCompile** 被绑定到 **jmh** 源集定义。您可以自由选择源集名称。如果你称之为**基准**，在依赖部分使用**基准编译**！

还有人在用 Eclipse 吗？这一块有助于正确设置:

```
eclipse { 
    classpath { 
        plusConfigurations.add(configurations.jmhCompile) 
        defaultOutputDir = file('build/classes-jmh-ide') 
    } 
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一部分是关于基准的执行。最简单的版本是这样的:

```
task jmh(type: JavaExec, description: 'Executing JMH benchmarks') { 
    classpath = sourceSets.jmh.runtimeClasspath 
    main = 'org.openjdk.jmh.Main' 
} 
```

Enter fullscreen mode Exit fullscreen mode

但几乎可以肯定的是，你想增强这个版本，使一些 JMH 的参数，参数的梯度任务。例如，选择基准的正则表达式+将结果写入 JSON 文件:

```
task jmh(type: JavaExec, description: 'Executing JMH benchmarks') { 
    classpath = sourceSets.jmh.runtimeClasspath 
    main = 'org.openjdk.jmh.Main' 

    def include = project.properties.get('include', ''); 
    def format = project.properties.get('format', 'json'); 
    def resultFile = file("build/reports/jmh/result.${format}") 
    resultFile.parentFile.mkdirs() 

    args include 
    args '-rf', format 
    args '-rff', resultFile 
} 
```

Enter fullscreen mode Exit fullscreen mode

瞧，现在你可以用
运行你的基准测试了

```
gw jmh2 -Pinclude=“.\*MyBenchmark.\*” 
```

Enter fullscreen mode Exit fullscreen mode

注意:*可能还有更多你想要传递的参数…看下面的完整版本，我发现更多有用的。*

#### 完整版

把所有东西放在一起，加入一些糖和香料(jmhHelp 任务，更多参数，等等..)留给我们的是: