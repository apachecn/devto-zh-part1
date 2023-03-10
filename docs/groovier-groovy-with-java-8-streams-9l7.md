# 带有 Java 8 流的 Groovier Groovy

> 原文：<https://dev.to/rvprasad/groovier-groovy-with-java-8-streams-9l7>

考虑编写 [Groovy](http://www.groovy-lang.org/) 代码，从给定的集合中检索元素的转换版本，使元素的转换版本满足给定的谓词。

下面是一段仅使用 Groovy 特性的纯 Groovy 代码片段。

```
(0..N).collect { e -> transform(e) }
      .findResult([]) { e -> predicate(e) ? e : null } 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个使用 Java 8 流的 Groovy+Java 代码片段。

```
IntStream.range(0, N)
         .mapToObj { e -> transform(e) }
         .filter { e -> predicate(e) }
         .findAny()
         .orElse([]) 
```

Enter fullscreen mode Exit fullscreen mode

第一个代码片段在执行 *findResult* (分阶段执行)之前转换( *collect* )源集合中的每个元素，而第二个代码片段执行源集合中每个元素的整个函数管道。因此，Groovy+Java 代码片段效率更高，因为一旦找到元素的转换版本，它就停止转换元素。

就并行性而言，下面是使用[gpar](http://gpars.org/)并行化的 Groovy 专用代码片段。

```
GParsPool.withPool {
    (0..N).collectParallel { e -> transform(e) }
          .findResult([]) { e -> predicate(e) ? e : null }
} 
```

Enter fullscreen mode Exit fullscreen mode

当 *collect* 被并行化时(如 *collectParallel* ，findResult 没有被并行化，因为它不被 GPars 支持。此外，即使 *findResult* 被并行化，它也将仅在 *collectParallel* 完成之后执行，因此，上面观察到的由于分阶段执行导致的低效率仍将持续。

相比之下，这里是并行版本的 Groovy+Java 代码片段。

```
IntStream.range(0, N)
         .parallel().
         .mapToObj { e -> transform(e) }
         .filter { e -> predicate(e) }
         .findAny()
         .orElse([]) 
```

Enter fullscreen mode Exit fullscreen mode

与并行化的 Groovy 专用代码片段不同，管道中的每个函数在其输入可用时并行执行。因此，这将比并行化的纯 Groovy 代码片段更有效。

几年前，Groovy 开发了 Java groovier。有了 Java 8 流，我觉得 Java 可以让 Groovy groovier。

也就是说，我渴望看到 Groovy 将如何发展以使 Java 更 groovier。