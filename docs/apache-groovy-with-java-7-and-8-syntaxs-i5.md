# 带有 Java 7 和 8 语法的 Apache Groovy

> 原文：<https://dev.to/saba1024/apache-groovy-with-java-7-and-8-syntaxs-i5>

自 Apache Groovy 2.6(最新版本是 2017 年 11 月 16 日的 2.6.0-alpha-2)以来，它可以使用 Java8 的 Lambda 语法和 Java7 的 try-with-resources。Groovist 已经使用了这些函数。但是对于还没有使用 Apache Groovy 的 Java 开发人员来说，适应 Java 的语法真的很重要。

安装 Apache Groovy，然后将文件名从`Foo.java`改为`Foo.groovy`并简单地执行`groovy Foo.groovy`。
从 Apache Groovy 2.6 开始，Java 开发者再次等于 Apache Groovy 开发者。

但是这个函数在 Apache Groovy 上还不是标准的。
Apache Groovy 2.6 可选支持，Apache Groovy 3 默认支持。Apache Groovy 2.6 仍处于 alpha 阶段。但是已经出来了。我们可以测试一下！

# 安装 Apache Groovy 2.6

通过 sdkman 安装。
**Apache Groovy 2.6 还不支持默认的 Java8 语法**
它必须通过执行 Groovy 指定一个选项`-Dgroovy.antlr4=true`。

```
[koji:~]$ sdk install groovy 2.6.0-alpha-2 
[koji:~]$ sdk use groovy 2.6.0-alpha-2

# to activate Java7/9 syntax.
[koji:~]$ groovyConsole -Dgroovy.antlr4=true&

# of course you can JAVA_OPTS.
[koji:~]$ echo $JAVA_OPTS

[koji:~]$ export JAVA_OPTS="-Dgroovy.antlr4=true"
[koji:~]$ echo $JAVA_OPTS
-Dgroovy.antlr4=true
[koji:~]$ groovyConsole& 
```

Enter fullscreen mode Exit fullscreen mode

# 试资源

```
def file = new File("/tmp/test.txt")

/*
def fileReader = file.newReader()
println fileReader.ready()
fileReader.close()
println fileReader.ready() // java.io.IOException: Stream closed. of ocurse :)
*/

// try-with-resources ! Since Apache Groovy 2.6 can use!
try(BufferedReader br = file.newReader()) {
    br.eachLine {
        println it
    }
} catch(Exception e) {
    println e
}

// Normal Groovy way!
file.withReader {BufferedReader br ->
    br.eachLine {
        println it
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 使用λ

```
// Lamda syntax is available!
java.util.function.Function<Integer, String> test = (i) -> {"${i} !?"}
assert "123 !?" == test.apply(123)

// Lambda with streams.
List<Integer> list = [1,2,3,4,5]
assert [10, 8, 6, 4, 2] == list.stream().map((a) -> {a * 2}).sorted((a, b) -> { return b - a; }).collect()

// Normal Groovy way!
assert [10, 8, 6, 4, 2] == list.collect{it * 2}.sort{a,b -> b <=> a} 
```

Enter fullscreen mode Exit fullscreen mode

比起 Java，我更喜欢 Groovy 风格。但是这个新的语法解析器对于学习 Apache Groovy 的 Java 开发人员来说确实很有帮助。