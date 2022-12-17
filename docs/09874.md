# 使用 Hazelcast Jet 回放事件

> 原文：<https://dev.to/poetix/event-replaying-with-hazelcast-jet>

(以下文章是从 [OpenCredo 博客](https://opencredo.com/blog)交叉发布的，Dominic 和其他 [OpenCredo](https://opencredo.com) 作者在博客中写了他们感兴趣和感兴趣的技术和技术挑战)

## 简介:并行流处理

Java 8 的 Streams API 设计背后的意图之一是更好地利用现代计算机的多核处理能力。可以在单个线性值流上执行的操作也可以并行运行，方法是将该流分成多个子流，并在每个子流的处理结果可用时对其进行组合。

例如，假设我们想要计算一个大数组`String`值的散列。最简单的方法就是用`Arrays.deepHashCode` :

```
int hash = Arrays.deepHashCode(strings); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们也可以把数组中所有`String`的散列集合成一个`int`的数组，然后取这个数组的散列:

```
int hash = Arrays.hashCode(Arrays.stream(strings)
    .mapToInt(String::hashCode).toArray()); 
```

Enter fullscreen mode Exit fullscreen mode

这样做了之后，我们就可以在默认 Java `ForkJoinPool`中的所有线程上并行散列操作，方法是让流并行:

```
int hash = Arrays.hashCode(Arrays.stream(strings).parallel()
    .mapToInt(String::hashCode).toArray()); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个测试，它使用了 [Contiperf](http://databene.org/contiperf) 性能测试库来收集这三种方法的一些指标:

```
public class PerformanceTest {

    @Rule
    public final ContiPerfRule contiperfRule = new ContiPerfRule();

    private static final String[] strings = IntStream.range(0, 1000000)
            .mapToObj(i -> randomString())
            .toArray(String[]::new);

    private static String randomString() {
        Random random = ThreadLocalRandom.current();
        byte[] bytes = new byte[random.nextInt(100) + 1];
        random.nextBytes(bytes);
        return new String(bytes);
    }

    @Test
    @PerfTest(invocations = 10000, warmUp = 10000)
    public void hashWithDeepHashCode() {
        Arrays.deepHashCode(strings);
    }

    @Test
    @PerfTest(invocations = 10000, warmUp = 10000)
    public void hashWithLinearStream() {
        Arrays.hashCode(Arrays.stream(strings).mapToInt(String::hashCode).toArray());
    }

    @Test
    @PerfTest(invocations = 10000, warmUp = 10000)
    public void hashWithParallelStream() {
        Arrays.hashCode(Arrays.stream(strings).parallel().mapToInt(String::hashCode).toArray());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是三种方法的结果:

```
com.opencredo.eventhose.PerformanceTest.hashWithParallelStream
samples: 8536
max:     19
average: 6.018158388003749
median:  6

com.opencredo.eventhose.PerformanceTest.hashWithDeepHashCode
samples: 9234
max:     29
average: 10.06670998483864
median:  11

com.opencredo.eventhose.PerformanceTest.hashWithLinearStream
samples: 9262
max:     40
average: 12.909090909090908
median:  13 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所料，将一个由`String`组成的数组线性转换成一个由`int`组成的数组，然后对其进行哈希运算，比只使用`Arrays.deepHashCode`要慢 20%左右。但是，当我们并行处理每个单独的`String`的散列时，我们会看到随着更多 CPU 核心投入使用，速度显著加快，每个核心处理原始数组中值的子集。

然而，我们仍然受限于单台机器上可用的 CPU 和内存资源。如果我们可以在一个*机器集群*上并行处理流处理任务，会怎么样？

## Hazelcast 喷射

Hazelcast Jet 在 Hazelcast 分布式数据网格的基础上构建了两层。第一个映射一个 DAG(有向无环图),表示网格上的计算流，为分布式数据流提供一个通用的处理模型。数据由源提供，由接收器消费，并由同时充当接收器和源的处理器转换。这个模型应该是和 Spark、Spring XD 或者 Apache Storm 合作过的人都很熟悉的，这里就不详细讨论了——在 Hazelcast Jet 网站上有[很好的介绍。](http://jet.hazelcast.org/performance/#DAGtoModelComputations)

第二层将使用 Java 8 Streams API 定义的计算映射到 DAG。通过一些小的调整，我们可以像以前一样运行相同的代码，并让它分布在一个 Hazelcast 实例集群上:

```
public class JetTest {

    private static final String[] strings = IntStream.range(0, 1000000)
            .mapToObj(i -> randomString())
            .toArray(String[]::new);

    private static String randomString() {
        Random random = ThreadLocalRandom.current();
        byte[] bytes = new byte[random.nextInt(100) + 1];
        random.nextBytes(bytes);
        return new String(bytes);
    }

    private JetInstance jet1;
    private JetInstance jet2;
    private JetInstance jet3;
    private JetInstance jet4;

    @Before
    public void startJet() {
        jet1 = Jet.newJetInstance();
        jet2 = Jet.newJetInstance();
        jet3 = Jet.newJetInstance();
        jet4 = Jet.newJetInstance();
    }

    @After
    public void stopJet() {
        jet1.shutdown();
        jet2.shutdown();
        jet3.shutdown();
        jet4.shutdown();
    }

    @Test
    public void hashInJet() {
        IStreamList<String> input = jet1.getList("strings");
        Arrays.stream(strings).forEach(input::add);

        int hash = Arrays.hashCode(input.stream()
                .mapToInt(String::hashCode)
                .toArray());

        System.out.println(hash);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

也许毫不奇怪，考虑到需要协调集群内的工作，并将序列化数据和字节码推送到每个执行它的节点，这比在单个进程中在内存中做同样的事情要慢得多。然而，重用一个众所周知的编程模型来定义分布式计算的能力是非常有吸引力的。让我们给它一些比散列字符串更有趣的东西。

## 重演事件

假设我们有一个来自许多来源的事件流，这些事件是交错的，可能是无序的，我们希望将它分解成一个按时间顺序排列的事件历史图，并按事件来源分组。换句话说，给定:

```
At 3pm, Lightbulb 1 was switched on.
At 1pm, Lightbulb 2 was switched off.
At 2pm, Lightbulb 1 was plugged in.
At 11am, Lightbulb 2 was plugged in.
At 10am, Lightbulb 3 was plugged in.
At 12pm, Lightbulb 2 was switched on... 
```

Enter fullscreen mode Exit fullscreen mode

我们想要

```
Lightbulb 1:
    2pm: plugged in
    3pm: switched on

Lightbulb 2:
    11am: plugged in
    12pm: switched on
    1pm: switched off

Lightbulb 3:
    10am: plugged in... 
```

Enter fullscreen mode Exit fullscreen mode

这可以用 Streams API 表示如下:

```
Map<AggregateId, SortedSet<Event>> eventHistories = events.stream()
        .collect(groupingBy(
            Event::getAggregateId,
            toCollection(() -> TreeSet<Event>(eventTimestampComparator))); 
```

Enter fullscreen mode Exit fullscreen mode

给定一个函数，它获取单个灯泡的事件历史，并返回灯泡的当前状态，我们可以将无序的事件流转换成更有用的东西:一个键/值查找表，给出每个灯泡的当前状态:

```
IMap<AggregateId, LightbulbState> eventHistories = events.stream()
        .collect(groupingByToIMap(
            Event::getAggregateId,
            collectingAndThen(
                toCollection(() -> TreeSet<Event>(eventTimestampComparator)),
                eventHistory -> stateModel.getCurrentState(eventHistory)); 
```

Enter fullscreen mode Exit fullscreen mode

请注意签名从`Map`到`IMap`(一个 Hazelcast 分布式键/值映射)的变化，这意味着结果将存储在分布于整个 Hazelcast 网格的映射中。这意味着 Hazelcast 可以将每次计算的结果保存在执行计算的数据网格的分区中，而不必将所有结果序列化并返回给客户端，然后将它们组装到一个本地`Map`中。这在事件源系统中可能非常有用，在这种系统中，我们希望对聚合的当前状态进行快速缓存查找，特别是如果我们希望缓存分布在服务器网格中。

因为 Streams API 将计算定义为终止于收集操作——类似于 map/reduce 处理中的“reduce”阶段——所以似乎将 Hazelcast Jet 与此 API 一起使用最适合流数据源上的批处理计算，而不是连续的流处理操作，例如计算滚动平均值或检测滚动时间窗口内的异常事件模式。然而，在第一印象中，Hazelcast Jet 为一个强大的分布式计算引擎提供了一个熟悉而方便的编程 API，它有可能在某些用例中取代 Spark 或 Storm。