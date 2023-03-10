# 优化 Flink 应用的四种方法

> 原文：<https://dev.to/mushketyk/four-ways-to-optimize-your-flink-applications-363>

[![](img/95e8b3aed7c2cf51c36b3900c0d30261.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BmBXnkU1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cv65nwycvzita8ggz2qf.jpeg)

Flink 是一个复杂的框架，提供了许多调整其执行的方法。在本文中，我将展示四种不同的方法来提高 Flink 应用程序的性能。

如果你对 Flink 不熟悉，可以看看其他类似[这个](https://brewing.codes/2017/09/25/flink-vs-spark/)、[这个](https://dev.to/mushketyk/getting-started-with-batch-processing-using-apache-flink-bnh)、[这个](https://dev.to/mushketyk/getting-started-with-stream-processing-using-apache-flink-530)的入门文章。但是如果你已经熟悉 Apache Flink，这篇文章将帮助你使你的应用程序更快一点。

# 使用 Flink 元组

当您使用像`groupBy`、`join`或`keyBy`这样的操作时，Flink 提供了许多选项来选择数据集中的一个键。您可以使用按键选择器功能:

```
// Join movies and ratings datasets
movies.join(ratings)
        // Use movie id as a key in both cases
        .where(new KeySelector<Movie, String>() {
            @Override
            public String getKey(Movie m) throws Exception {
                return m.getId();
            }
        })
        .equalTo(new KeySelector<Rating, String>() {
            @Override
            public String getKey(Rating r) throws Exception {
                return r.getMovieId();
            }
        }) 
```

或者您可以在 POJO 类型中指定一个字段名称:

```
movies.join(ratings)
    // Use same fields as in the previous example
    .where("id")
    .equalTo("movieId") 
```

但是，如果您正在使用 Flink 元组类型，您可以简单地指定将用作键的字段元组的位置:

```
DataSet<Tuple2<String, String>> movies = ...
DataSet<Tuple3<String, String, Double>> ratings = ...

movies.join(ratings)
    // Specify fields positions in tuples
    .where(0)
    .equalTo(1) 
```

最后一个选项会给你最好的性能，但是可读性呢？这是否意味着您的代码现在看起来像这样:

```
DataSet<Tuple3<Integer, String, Double>> result = movies.join(ratings)
    .where(0)
    .equalTo(0)
    .with(new JoinFunction<Tuple2<Integer,String>, Tuple2<Integer,Double>, Tuple3<Integer, String, Double>>() {
        // What is happening here?
        @Override
        public Tuple3<Integer, String, Double> join(Tuple2<Integer, String> first, Tuple2<Integer, Double> second) throws Exception {
            // Some tuples are joined with some other tuples and some fields are returned???
            return new Tuple3<>(first.f0, first.f1, second.f1);
        }
    }); 
```

在这种情况下，提高可读性的一个常见习语是创建一个从某个`TupleX`类继承的类，并为这些字段实现 getters 和 setters。下面是一个来自 Flink Gelly 库的`Edge`类，它有三个类并扩展了`Tuple3`类:

```
public class Edge<K, V> extends Tuple3<K, K, V> {

    public Edge(K source, K target, V value) {
        this.f0 = source;
        this.f1 = target;
        this.f2 = value;
    }

    // Getters and setters for readability
    public void setSource(K source) {
        this.f0 = source;
    }

    public K getSource() {
        return this.f0;
    }

    // Also has getters and setters for other fields
    ...
} 
```

# 重用 Flink 对象

可以用来提高 Flink 应用程序性能的另一个选项是，当从用户定义的函数返回数据时，使用可变对象。看一下这个例子:

```
stream
    .apply(new WindowFunction<WikipediaEditEvent, Tuple2<String, Long>, String, TimeWindow>() {
        @Override
        public void apply(String userName, TimeWindow timeWindow, Iterable<WikipediaEditEvent> iterable, Collector<Tuple2<String, Long>> collector) throws Exception {
            long changesCount = ...
            // A new Tuple instance is created on every execution
            collector.collect(new Tuple2<>(userName, changesCount));
        }
    } 
```

正如您在每次执行`apply`函数时所看到的，我们创建了一个`Tuple2`类的新实例，这增加了垃圾收集器的压力。解决这个问题的一种方法是反复使用同一个实例:

```
stream
    .apply(new WindowFunction<WikipediaEditEvent, Tuple2<String, Long>, String, TimeWindow>() {
        // Create an instance that we will reuse on every call
        private Tuple2<String, Long> result = new Tuple<>();

        @Override
        public void apply(String userName, TimeWindow timeWindow, Iterable<WikipediaEditEvent> iterable, Collector<Tuple2<String, Long>> collector) throws Exception {
            long changesCount = ...

            // Set fields on an existing object instead of creating a new one
            result.f0 = userName;
            // Auto-boxing!! A new Long value may be created
            result.f1 = changesCount;

            // Reuse the same Tuple2 object
            collector.collect(result);
        }
    } 
```

好一点了。我们在每次调用时都创建一个新的`Tuple2`实例，但是我们仍然间接地创建了一个`Long`类的实例。为了解决这个问题，Flink 有多个所谓的价值阶层:`IntValue`、`LongValue`、`StringValue`、`FloatValue`等。这个类的目的是提供内置类型的可变版本，所以我们可以在我们的用户定义函数中重用它们。下面是我们如何使用它们:

```
stream
    .apply(new WindowFunction<WikipediaEditEvent, Tuple2<String, Long>, String, TimeWindow>() {
        // Create a mutable count instance
        private LongValue count = new IntValue();
        // Assign mutable count to the tuple
        private Tuple2<String, LongValue> result = new Tuple<>("", count);

        @Override
        // Notice that now we have a different return type
        public void apply(String userName, TimeWindow timeWindow, Iterable<WikipediaEditEvent> iterable, Collector<Tuple2<String, LongValue>> collector) throws Exception {
            long changesCount = ...

            // Set fields on an existing object instead of creating a new one
            result.f0 = userName;
            // Update mutable count value
            count.setValue(changesCount);

            // Reuse the same tuple and the same LongValue instance
            collector.collect(result);
        }
    } 
```

这种习语常用于 Flink 图书馆，如 Flink Gelly。

# 使用功能注释

优化 Flink 应用程序的另一种方法是提供一些关于用户定义函数如何处理输入数据的信息。由于 Flink 不能解析和理解代码，所以您可以提供关键信息来帮助构建更有效的执行计划。我们可以使用三种注释:

*   @ ForwardedFields–指定输入值中哪些字段保持不变并用于输出值。
*   @ NotForwardedFields–指定未保留在输出中相同位置的字段。
*   @ read fields–指定用于计算结果值的字段。您应该只指定在计算中使用的字段，而不仅仅是复制到输出中。

让我们看看如何使用`ForwardedFields`注释:

```
// Specify that the first element is copied without any changes
@ForwardedFields("0")
class MyFunction implements MapFunction<Tuple2<Long, Double>, Tuple2<Long, Double>> {
    @Override
    public Tuple2<Long, Double> map(Tuple2<Long, Double> value) {
       // Copy first field without change
        return new Tuple2<>(value.f0, value.f1 + 123);
    }
} 
```

这意味着输入元组中的第一个元素没有被更改，它在相同的位置返回。

如果你不改变一个字段，只是简单地把它移动到不同的位置，你也可以用`ForwardedFields`注释来指定。在下一个例子中，我们交换输入元组中的字段，并警告 Flink:

```
// 1st element goes into the 2nd position, and 2nd element goes into the 1st position
@ForwardedFields("0->1; 1->0")
class SwapArguments implements MapFunction<Tuple2<Long, Double>, Tuple2<Double, Long>> {
    @Override
    public Tuple2<Double, Long> map(Tuple2<Long, Double> value) {
       // Swap elements in a tuple
        return new Tuple2<>(value.f1, value.f0);
    }
} 
```

上面提到的注释只能应用于有一个输入参数的函数，比如`map`或者`flatMap`。如果您有两个输入参数，您可以使用`ForwardedFieldsFirst`和`ForwardedFieldsSecond`注释，分别提供关于第一个和第二个参数的信息。

下面是我们如何在`JoinFunction`接口的实现中使用这些注释:

```
// Two fields from the input tuple are copied to the first and second positions of the output tuple
@ForwardedFieldsFirst("0; 1")
// The third field from the input tuple is copied to the third position of the output tuple
@ForwardedFieldsSecond("2")
class MyJoin implements JoinFunction<Tuple2<Integer,String>, Tuple2<Integer,Double>, Tuple3<Integer, String, Double>>() {
    @Override
    public Tuple3<Integer, String, Double> join(Tuple2<Integer, String> first, Tuple2<Integer, Double> second) throws Exception {
        return new Tuple3<>(first.f0, first.f1, second.f1);
    }
}) 
```

出于类似的目的，Flink 还提供了`NotForwardedFieldsFirst`、`NotForwardedFieldsSecond`、`ReadFieldsFirst`和`ReadFirldsSecond`注释。

# 选择加入类型

如果你给 Flink 另一个提示，你可以使你的连接更快，但是在我们讨论它为什么工作之前，让我们先谈谈 Flink 是如何执行连接的。

当 Flink 处理批量数据时，集群中的每台机器都会存储部分数据。为了执行连接，Apache Flink 需要找到满足连接条件的两个数据集的所有对。为此，Flink 首先必须将来自两个数据集的具有相同键的项目放在集群中的同一台机器上。对此有两种策略:

*   **重新分区-重新分区策略**–在这种情况下，两个数据集都按其关键字进行分区，并通过网络发送。这意味着，如果数据集很大，在网络上复制它们可能需要大量的时间。
*   **广播转发策略**–在这种情况下，一个数据集保持不变，但第二个数据集被复制到集群中包含第一个数据集的每台机器上。

如果要将一个小数据集与一个大得多的数据集连接起来，可以使用广播转发策略，避免对第一个数据集进行代价高昂的分区。这很容易做到:

```
ds1.join(ds2, JoinHint.BROADCAST_HASH_FIRST) 
```

这暗示第一个数据集比第二个小得多。

您还可以使用其他联接提示:

*   **BROADCAST _ HASH _ SECOND**–第二个数据集要小得多
*   **re partition _ HASH _ FIRST**–第一个数据集有点小
*   **re partition _ HASH _ SECOND**–第二个数据集有点小
*   **重新分区 _ 排序 _ 合并**–重新分区两个数据集，并使用排序和合并策略
*   **OPTIMIZER _ choices**–Flink OPTIMIZER 将决定如何连接数据集

你可以在本文的[中阅读更多关于 Flink 如何执行连接的内容。](https://flink.apache.org/news/2015/03/13/peeking-into-Apache-Flinks-Engine-Room.html)

# 更多信息

我希望你喜欢这篇文章，并发现它很有用。

近期会写更多关于 Flink 的文章，敬请期待！你可以在这里阅读我的其他文章[，或者你可以看看我的 Pluralsight 课程，我在那里详细介绍了 Apache Flink:](https://brewing.codes/)[了解 Apache Flink](http://bit.ly/understanding-flink) 。这是本课程的[短片预告](http://bit.ly/understanding-flink-preview)。

帖子[优化你的 Flink 应用的四种方法](https://brewing.codes/2017/10/17/flink-optimize/)首先出现在[酿造代码](https://brewing.codes)上。