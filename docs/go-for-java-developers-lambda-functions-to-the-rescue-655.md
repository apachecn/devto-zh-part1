# 面向 Java 开发人员

> 原文：<https://dev.to/napicella/go-for-java-developers-lambda-functions-to-the-rescue-655>

昨天我不得不在围棋中执行一个任务。

正如你可能已经从这篇[帖子](https://dev.to/napicellatwit/go-for-java-developers-or-is-the-java-concurrency-that-bad-6ff)中知道的，我最近开始用 Go 编程，所以经常在我脑海中出现的第一个问题是:我如何用 Java 编程？

我不确定这是好是坏，但我发现从多个角度看同一件事对我有好处。这不是关于使用 Go 或 Java，使用函数式或命令式风格，而是在混合不同的概念中，我看到了真正的价值。与上一篇文章类似，在这篇文章中，我将记录如何从不同的角度看待问题并找到解决方案。

## 问题

我将简单地介绍一下这个任务，突出这个帖子中有趣的部分。任务的一部分包括在内存中加载一个文件的一部分:一个超级简单的解析器。

文件是这样的:

```
// a comment here 
#HEADER 
Key1=value1 
//another comment 
Key2=value2 
Key3=value3 
Key4=value4 
```

Enter fullscreen mode Exit fullscreen mode

我们对属于一个部分的两个键感兴趣。这个部分以一个标题开始，标题以一个标签开始。
我们得到了部分的名称和两个键的名称。
即 section: "person "，keys : ["name "，" surname"]
所以我们想在文件中找到" person "部分，并将名字和姓氏的值加载到内存中。

关于文件的几个问题:

*   文件头可能不在文件中
*   姓名可能不在文件中
*   在文件中，名字总是在姓氏之前
*   它可能包含注释(//)或其他标记

一种可能的解决方案是简单地逐行读取文件，并且:

*   如果该行以#开头，那么这是一个部分的开始，准备读取这个标题的密钥对
*   读出名字和姓氏
*   忽略注释和其余的键

* * *

所以，让我们暂时戴上 Java 程序员的帽子。

> 所以读取一个文件，一行一行，过滤一些行…

好的，这看起来像是一个由`java.nio.file.Files`、一个`java.util.stream.Stream`和一个自定义`java.util.function.Predicate`过滤数据流的工作。
我说自定义，意思是我们需要在谓词中保持一些状态。
状态就是谓词下次被调用时应该匹配的字符串，也就是说，如果它已经匹配了头，那么接下来应该匹配名字。

> 这基本上意味着两件事:流和 Lambda 函数。

长话短说吧，戈朗没有小溪这种东西。让我在这里做一个小小的转移，这个心理练习的全部目的不是实现一些违背语言本身的东西，而是从中获得最好的东西。所以没有溪流。没有它，我们也活不下去，因为循环救援。

但是兰姆达斯呢？好吧，围棋的功能是一等公民。我们可以将它们赋给变量，创建函数数组，像传递其他参数一样传递它们，所以我想我们有自己的 lambdas:)

* * *

好了，说够了，让我们看看代码…
在看代码之前，让我们回顾一下我们想要做的事情。
所以我们希望有一个 for 循环，在这个循环中，我们从一个源中逐行读取内容，对于每一行，我们检查它是否应该被写到某个地方。
检查该行是否应该被写入的位是一个过滤器，它在内部保持一个状态。

好了，让我们看看这一点:

```
func copy(profileName string, in io.Reader, out io.Writer) error {
    var (
        line       string
        readError  error
        writeError error
    )

    profileFilter := newProfileFilter(profileName)
    reader := bufio.NewReader(in)

    for {
        line, readError = reader.ReadString('\n')

        if profileFilter.match(line) {
            _, writeError = io.WriteString(out, line)
        }

        if readError != nil || writeError != nil {
            break
        }
    }

     if writeError != nil {
        return writeError
     }

     if readError != io.EOF {
        return readError
     }

     return nil
} 
```

Enter fullscreen mode Exit fullscreen mode

这相当于 Java

```
Files.lines(resource())
             .filter(profileFilter)
             .forEach(ProfileWriter::write); 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它分解一下:

*   Files.lines 是`for loop`
*   过滤器是 if 条件`profileFilter.match(line)`
*   forEach 只是 if 条件的主体`io.WriteString(out, line)`

那么我们来看看滤镜:

```
func newProfileFilter(profileName string) *profileFilter {
    var matchers  bool)

    matchers = append(matchers,
        matcher(startsWith).apply("#"+profileName),
        matcher(startsWith).apply("name="),
        matcher(startsWith).apply("surname="),
    )

    return &profileFilter{matchers, profileName}
}

func startsWith(line string, toMatch string) bool {
    return strings.HasPrefix(
        strings.ToLower(strings.TrimSpace(line)),
        strings.ToLower(toMatch),
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 profileFilter 的构造函数，负责检测节的头和键。它基本上是一个函数或 lambdas 的数组。每当过滤器匹配文件中的一行时，我们从数组中弹出一个元素，因此在下一次迭代中，过滤器将匹配不同的字符串。这段代码中我真正喜欢的是我们构造 lambdas 的方式:

```
matcher(startsWith).apply("name=") 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们正在构建一个匹配器，它匹配以" name="
开头的行。

```
type matcher func(line string, toMatch string) bool 
```

Enter fullscreen mode Exit fullscreen mode

这是一个函数的类型别名。所以这只是一个函数，它得到一行和一个字符串作为输入来匹配并返回一个布尔值。就是这样。
语法`matcher(startsWith)`只是一个造型，我们将函数 startsWith 造型为一个匹配器。当然我们可以这样做，因为 matcher 是一个函数！

> 当我写这个的时候，我想...好吧，围棋中的功能非常酷

应用方法是什么？

```
func (f matcher) apply(toMatch string) func(line string) bool {
    return func(line string) bool {
        return f(line, toMatch)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是一种讨好功能的方式。长话短说，我们有一个两个参数的函数，我们真的想要一个有一个参数的函数，而另一个参数已经设置好了。

所以在这里，我们应用我们想要匹配的字符串，比如" name= "，然后我们得到一个函数，它只有一行作为参数。
我们将 apply 方法附加到匹配器上，所以最终结果是通过调用:

```
matcher(startsWith).apply("name=") 
```

Enter fullscreen mode Exit fullscreen mode

我们得到一个函数，它返回一个布尔值(java 语言中的一个谓词)，如果行以 name 开头，这个布尔值将为真。

为了完整起见，这里是代码的其余部分。

```
type profileFilter struct {
    matchers    []func(line string) bool
    profileName string
}

func (p *profileFilter) match(text string) bool {
    if len(p.matchers) == 0 {
        return false
    }

    shouldFilter := p.matchers[0](text)

    if shouldFilter {
        p.matchers = p.matchers[1:len(p.matchers)]
    }

    return shouldFilter
} 
```

Enter fullscreen mode Exit fullscreen mode

## Java 版本

在写这篇文章的时候，我决定为了好玩，我可以用 Java 写同样的程序。
下面是一个快速实现:

```
 public static void main(String[] args) throws IOException, URISyntaxException{
        Matcher startsWith = (line, toMatch) -> startsWith(line, toMatch);

        ProfileFilter profileFilter =
            new ProfileFilter(
                startsWith.apply("#some-profile"),
                startsWith.apply("key2="),
                startsWith.apply("key3=")
            );

        Files.lines(resource())
             .filter(profileFilter)
             .forEach(System.out::println);
    }

    static class ProfileFilter implements Predicate<String> {
        private LinkedList<Predicate<String>> predicates;

        ProfileFilter(Predicate<String>...predicates) {
            this.predicates = new LinkedList<>(Arrays.asList(predicates));
        }

        @Override
        public boolean test(String s) {
            if (predicates.size() == 0) {
                return false;
            }

            boolean shouldFilter = predicates.getFirst().test(s);

            if (shouldFilter) {
                predicates.removeFirst();
            }

            return shouldFilter;
        }
    }

    interface Matcher extends BiPredicate<String, String> {

        default Predicate<String> apply(String applied) {
            return s -> this.test(s, applied);
        }
    }

    static Boolean startsWith(String line, String toMatch) {
        return line.toLowerCase().trim().startsWith(toMatch.toLowerCase());
    } 
```

Enter fullscreen mode Exit fullscreen mode

这两个版本显然非常相似，除了在 java 版本中，我使用 stream 而不是 for 循环，并且我在控制台上打印这些行，而不是将其写入 Writer。

> 这里有一个旁注:流确实很酷，但是它们不能与异常相处。例如，写入文件是那些抛出异常的操作之一，所以在实际用例中，我想我要么使用 **for 循环**而不是流，要么使用 **RxJava Observable** 。

## 结论

好吧，这比我预期的要长，考虑到我只是想说 Golang 可能没有 XD 那么糟糕。玩笑归玩笑，希望你喜欢。

谢谢！

### 资源

文章代码: [github](https://github.com/napicella/Go-for-java-programmers/blob/master/lambda-to-the-rescue/src/go/profile-filter.go)
我在 Golang 中发现了两个关于函数的有趣讲座。
看着它们完全值得:
[闭包是 go 的泛型](https://www.youtube.com/watch?v=5IKcPMJXkKs&index=1&list=PLZunOCSgrxwwlUSA_-UDRlptkEZEwvV3i)
[没有专长第一类函数](https://www.youtube.com/watch?v=5buaPyJ0XeQ&index=2&list=PLZunOCSgrxwwlUSA_-UDRlptkEZEwvV3i)