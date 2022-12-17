# 使用 Apache Flink 开始批处理

> 原文：<https://dev.to/mushketyk/getting-started-with-batch-processing-using-apache-flink-bnh>

如果您最近一直在关注软件开发新闻，您可能听说过名为 Apache Flink 的新项目。我已经写了一些关于它的内容[这里](https://brewing.codes/2017/09/25/flink-vs-spark/)和[这里](https://brewing.codes/2017/01/16/apache-flink-a-new-landmark/)，但是如果你不熟悉它，Apache Flink 是新一代大数据处理工具，它可以处理有限的数据集(这也称为批处理)或潜在的无限数据流(流处理)。在新功能方面，许多人认为 Apache Flink 是一个游戏改变者，甚至可以在未来取代 Apache Spark。

在本文中，我将向您介绍如何使用 Apache Flink 实现简单的批处理算法。我们将从设置开发环境开始，然后我们将了解如何加载数据、处理数据集以及将数据写回外部系统。

# 为什么要批量处理？

您可能听说过流处理是“现在的新热点”, Apache Flink 是流处理工具。这就提出了一个问题，为什么我们需要学习如何实现批处理应用程序。

诚然，流处理变得越来越普遍；许多任务仍然需要批处理。此外，如果您刚刚开始使用 Apache Flink，在我看来，最好从批处理开始，因为它更简单，并且在某种程度上类似于使用数据库。一旦您了解了批处理，您就可以了解 Apache Flink 真正大放异彩的流处理！

# 如何效仿榜样

如果您想自己实现一些 Apache Flink 应用程序，首先您需要创建一个 Flink 项目。在这篇文章中，我们打算用 Java 写应用，但是你也可以用 Scala、Python 或者 r 写 Flink 应用。

要创建 Flink Java 项目，请执行以下命令:

```
mvn archetype:generate \
      -DarchetypeGroupId=org.apache.flink \
      -DarchetypeArtifactId=flink-quickstart-java \
      -DarchetypeVersion=1.3.2 
```

在您输入组 id、工件 id 和项目版本之后，该命令将创建以下项目结构:

```
.
â”œâ”€â”€ pom.xml
â””â”€â”€ src
    â””â”€â”€ main
        â”œâ”€â”€ java
        â”‚Â Â  â””â”€â”€ flinkProject
        â”‚Â Â  â”œâ”€â”€ BatchJob.java
        â”‚Â Â  â”œâ”€â”€ SocketTextStreamWordCount.java
        â”‚Â Â  â”œâ”€â”€ StreamingJob.java
        â”‚Â Â  â””â”€â”€ WordCount.java
        â””â”€â”€ resources
            â””â”€â”€ log4j.properties 
```

这里最重要的是大量的`pom.xml`,它指定了所有必要的依赖项。自动创建的 Java 类是一些简单的 Flink 应用程序的例子，您可以看看，但是我们不需要它们。

要开始开发您的第一个 Flink 应用程序，使用`main`方法创建一个类，如下所示:

```
public class FlinkProgram {

    public static void main(String[] args) throws Exception {
        // Create Flink execution environment
        final ExecutionEnvironment env = ExecutionEnvironment.getExecutionEnvironment();

        // We will write our code here

        // Start Flink application
        env.execute();
    }
} 
```

这个`main`方法没有什么特别的。我们所要做的就是添加一些样板代码。

首先，我们需要创建一个 Flink 执行环境，如果您在本地机器上或在 Flink 集群中运行它，它的行为会有所不同:

*   在本地机器上，它将创建一个具有多个本地节点的完整的 Flink 集群。这是测试您的应用程序在现实环境中如何工作的好方法
*   在 Flink 集群上，它不会创建任何东西，而是使用现有的集群资源

或者，您可以创建一个收集环境，如下所示:

```
ExecutionEnvironment env = ExecutionEnvironment.createCollectionsEnvironment(); 
```

这将创建一个 Flink 执行环境，它不是在本地集群上运行 Flink 应用程序，而是在单个 Java 进程中使用内存中的集合来模拟所有操作。您的应用程序将运行得更快，但是这种环境与具有多个节点的本地集群有一些细微的区别。

# 我们从哪里开始？

在我们做任何事情之前，我们需要将数据读入 Apache Flink。我们可以从许多系统中读取数据，包括本地文件系统、S3、HDFS、HBase、Cassandra 等。无论我们从哪里读取数据集，Apache Flink 都允许我们使用`DataSet`类以统一的方式处理数据:

```
DataSet<Integer> numbers = ... 
```

数据集中的所有项应该具有相同的类型。单个 generics 参数指定存储在数据集中的数据类型。

要从文件中读取数据，我们可以使用`readTextFile`方法，该方法将逐行读取文件中的行，并返回类型为`String`的数据集:

```
DataSet<String> lines = env.readTextFile("path/to/file.txt"); 
```

如果像这样指定文件路径，Flink 将尝试读取本地文件。如果您想从 HDFS 读取文件，您需要指定`hdfs://`协议:

```
env.readCsvFile("hdfs:///path/to/file.txt") 
```

Flink 也支持 CSV 文件，但在这种情况下，它不会返回字符串的数据集。它将尝试解析每一行并返回一个由`Tuple`实例组成的数据集:

```
DataSet<Tuple2<Long, String>> lines = env.readCsvFile("data.csv")
                .types(Long.class, String.class); 
```

`Tuple2`是一个存储不可变的两个字段对的类，但是还有其他的类，比如`Tuple0`、`Tuple1`、`Tuple3`，直到`Tuple25`，它们存储 0 到 25 个字段。稍后我们将看到如何使用这些类。

`types`方法指定了 CSV 文件中列的类型和数量，因此 Flink 可以读取并解析它们。

我们还可以创建非常适合小型实验和单元测试的小型数据集:

```
// Create from a list
DataSet<String> letters = env.fromCollection(Arrays.asList("a", "b", "c"));
// Create from an array
DataSet<Integer> numbers = env.fromElements(1, 2, 3, 4, 5); 
```

你可能会问，我们可以在数据集中存储什么数据？并非每种 Java 类型都可以在数据集中使用，有四种不同的类型可供使用:

*   内置的 Java 类型和 POJO 类
*   Flink 元组和 Scala case 类
*   值——这些是 Java 原语类型的特殊可变包装器，可以用来提高性能(我将在下一篇文章中对此进行介绍)
*   Hadoop 可写接口的实现

# 用 Apache Flink 处理数据

现在到数据处理部分！你如何实现一个算法来处理你的数据？为此，您可以使用许多类似 Java 8 streams 操作的操作，例如:

*   **map**–使用用户定义的函数转换数据集中的项目。每个输入元素都被转换成一个输出元素
*   **filter**–根据用户定义的函数过滤数据集中的项目
*   **平面映射**–类似于**映射**操作符，但是允许返回零个、一个或多个元素
*   **group by**–按关键字对元素进行分组。类似于 SQL 中的`GROUP BY`操作符
*   **项目**–在元组数据集中选择指定的字段，类似于 SQL 中的`SELECT`操作符
*   **reduce**–使用用户定义的函数将数据集中的元素组合成一个值

请记住，Java streams 和这些操作的最大区别是，Java 8 在内存中处理数据，并可以访问本地数据，而 Flink 在分布式环境中处理集群上的数据。

让我们看一个使用这些操作的简单例子。下面的例子非常简单。它创建了一个数字数据集，对每个数字进行平方，并过滤掉所有奇数。

```
// Create a dataset of numbers
DataSet<Integer> numbers = env.fromElements(1, 2, 3, 4, 5, 6, 7);

// Square every number
DataSet<Integer> result = numbers.map(new MapFunction<Integer, Integer>() {
    @Override
    public Integer map(Integer integer) throws Exception {
        return integer * integer;
    }
})
// Leave only even numbers
.filter(new FilterFunction<Integer>() {
    @Override
    public boolean filter(Integer integer) throws Exception {
        return integer % 2 == 0;
    }
}); 
```

如果你有 Java 8 的经验，你可能会奇怪为什么我在这里不使用 lambdas。我们可以在这里使用 lambdas，但它会导致一些复杂的情况，正如我在这里写的。

# 保存回数据

在我们处理完数据后，保存我们努力工作的结果是有意义的。Flink 可以将数据存储到许多第三方系统中，如 HDFS、S3、卡珊德拉等。

例如，要将数据写入文件，我们需要使用来自`DataSet`类的`writeAsText`方法:

```
DataSet<Integer> ds = ...

ds.writeAsText("path/to/file"); 
```

出于调试/测试目的，Flink 可以将数据写入标准输出或标准输出:

```
DataSet<Integer> ds = ...

// Output dataset to the standard output
ds.print();

// Output dataset to the standard err
ds.printToErr() 
```

# 更复杂的例子

为了实现一些有意义的算法，我们需要首先下载一个[组镜头电影数据集](https://grouplens.org/datasets/movielens/)。它包含几个 CSV 文件，其中包含有关电影和电影分级的信息。我们将使用该数据集中的`movies.csv`文件，该文件包含所有电影的列表，如下所示:

```
movieId,title,genres
1,Toy Story (1995),Adventure|Animation|Children|Comedy|Fantasy
2,Jumanji (1995),Adventure|Children|Fantasy
3,Grumpier Old Men (1995),Comedy|Romance
4,Waiting to Exhale (1995),Comedy|Drama|Romance
5,Father of the Bride Part II (1995),Comedy
6,Heat (1995),Action|Crime|Thriller
7,Sabrina (1995),Comedy|Romance
8,Tom and Huck (1995),Adventure|Children
9,Sudden Death (1995),Action
10,GoldenEye (1995),Action|Adventure|Thriller 
```

它有三列:

*   **movie Id**–该数据集中一部电影的唯一电影 id
*   **片名**——电影的片名
*   **流派**–每部电影的流派列表，以“|”分隔

我们现在可以在 Apache Flink 中加载这个 CSV 文件，并执行一些有意义的处理。在这里，我们将从本地文件系统加载一个文件，而在现实环境中，您将读取一个大得多的数据集，并且它可能驻留在一个分布式系统中，例如 S3 或 HDFS。

在这个演示中，让我们找到所有“动作”类型的电影。下面是实现这一点的代码片段:

```
// Load dataset of movies
DataSet<Tuple3<Long, String, String>> lines = env.readCsvFile("movies.csv")
            .ignoreFirstLine()
            .parseQuotedStrings('"')
            .ignoreInvalidLines()
            .types(Long.class, String.class, String.class);

DataSet<Movie> movies = lines.map(new MapFunction<Tuple3<Long,String,String>, Movie>() {
    @Override
    public Movie map(Tuple3<Long, String, String> csvLine) throws Exception {
        String movieName = csvLine.f1;
        String[] genres = csvLine.f2.split("\\|");
        return new Movie(movieName, new HashSet<>(Arrays.asList(genres)));
    }
});
DataSet<Movie> filteredMovies = movies.filter(new FilterFunction<Movie>() {
    @Override
    public boolean filter(Movie movie) throws Exception {
        return movie.getGenres().contains("Action");
    }
});

filteredMovies.writeAsText("output.txt"); 
```

我们来分解一下。首先，我们使用`readCsvFile`方法读取一个 CSV 文件:

```
DataSet<Tuple3<Long, String, String>> lines = env.readCsvFile("movies.csv")
    // ignore CSV header
    .ignoreFirstLine()
    // Set strings quotes character
    .parseQuotedStrings('"')
    // Ignore invalid lines in the CSV file
    .ignoreInvalidLines()
    // Specify types of columns in the CSV file
    .types(Long.class, String.class, String.class); 
```

使用 helper 方法，我们指定如何解析 CSV 文件中的字符串，并且我们需要跳过第一行。在最后一行，我们指定 CSV 文件中每一列的类型，Flink 将为我们解析数据。

现在，当我们在 Flink 集群中加载数据集时，我们可以进行一些数据处理。首先，我们使用`map`方法解析每部电影的类型列表:

```
DataSet<Movie> movies = lines.map(new MapFunction<Tuple3<Long,String,String>, Movie>() {
    @Override
    public Movie map(Tuple3<Long, String, String> csvLine) throws Exception {
        String movieName = csvLine.f1;
         String[] genres = csvLine.f2.split("\\|");
         return new Movie(movieName, new HashSet<>(Arrays.asList(genres)));
    }
}); 
```

为了转换每部电影，我们需要实现`MapFunction`，它将接收每条 CSV 记录作为一个`Tuple3`实例，并将它转换成`Movie` POJO 类:

```
class Movie {
    private String name;
    private Set<String> genres;

    public Movie(String name, Set<String> genres) {
        this.name = name;
        this.genres = genres;
    }

    public String getName() {
        return name;
    }

    public Set<String> getGenres() {
        return genres;
    }
} 
```

如果您回忆一下 CSV 文件的结构，第二列包含一部电影的名称，第三列包含一个流派列表。因此，我们分别使用字段`f1`和`f2`来访问这些列。

现在，当我们有一个电影数据集时，我们可以实现我们算法的核心部分，并过滤所有动作电影:

```
DataSet<Movie> filteredMovies = movies.filter(new FilterFunction<Movie>() {
    @Override
    public boolean filter(Movie movie) throws Exception {
        return movie.getGenres().contains("Action");
    }
}); 
```

这将只返回流派集中包含“动作”的电影。

现在最后一步非常简单。我们将结果数据存储到一个文件中:

```
filteredMovies.writeAsText("output.txt"); 
```

这只是将结果数据存储到一个本地文本文件中，但是与`readTextFile`方法一样，我们可以通过指定类似`hdfs://`的协议将该文件写入 HDFS 或 S3。

# 更多信息

这是一篇介绍性文章，还有更多关于 Apache Flink 的内容。近期会写更多关于 Flink 的文章，敬请期待！你可以在这里阅读我的其他[文章，或者你可以看看我的 Pluralsight 课程，我在那里详细介绍了 Apache Flink:](https://brewing.codes/tag/flink/)[了解 Apache Flink](http://bit.ly/understanding-flink) 。这是本课程的[短片预告](http://bit.ly/understanding-flink-preview)。

使用 Apache Flink 开始批处理的帖子[首先出现在](https://brewing.codes/2017/10/01/start-flink-batch/)[酿造代码](https://brewing.codes)上。