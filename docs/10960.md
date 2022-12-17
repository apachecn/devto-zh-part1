# PySpark 和潜在狄利克雷分配

> 原文：<https://dev.to/seanlane/pyspark-and-latent-dirichlet-allocation-m9p>

在过去的这个学期(2016 年春季)，我有机会参加了两门课程:从概率角度看统计机器学习(有点拗口)和大数据科学& Capstone。在前者中，我们有机会研究近年来蓬勃发展的各种统计机器学习算法和过程的广度。这包括许多不同的主题，从高斯混合模型到潜在的狄利克雷分配。在后一种情况下，我们班分成几个小组，与许多大公司或组织中的一家合作一个顶点项目。这只是一门 3 个学分的课程，所以与学生整个学期唯一关注的传统顶点课程相比，这是一个不太密集的项目，但尽管如此，这仍然是一次很棒的经历。大数据科学课程教给我们一些大数据科学和常规数据分析的基础知识(ETL、MapReduce、Hadoop、Weka 等。)然后把我们释放到那边的蓝色荒野中，看看我们可以通过我们的各种项目完成什么。

对于大数据课程，我的团队实际上被分配了两个项目:

1.  尝试使用社交媒体追踪疾病和疫情
2.  为 Apache PySpark 创建一个模块，对`pyspark.ml`模型进行敏感性分析

这两个项目都涉及到 Apache PySpark 的使用，因此我对它有了基本的了解。在统计机器学习课程的最后一个项目中，我考虑了如何将两者的经验结合起来，并考虑使用 PySpark 的 LDA 功能，以便对我的大数据小组已经收集的一些社交媒体数据进行建模。我的一个想法是，如果我们可以对社交媒体内容进行聚类，那么我们就可以找到进一步的模式，或者过滤掉不良数据。也就是说，当我的班级试图自己实现 LDA 模型时，需要相当长的时间来处理，但我觉得在一个计算机集群上使用 PySpark 将允许我利用我们收集的大量社交媒体数据。我看到了一些在 Spark 中使用 LDA 的教程和例子，但是我发现它们都是用 Scala 编写的。从 Spark 到 PySpark 并不是很难，但是我觉得 PySpark 的一个版本会对一些人有用。

## 潜在狄利克雷分配的概要说明

我在完成自己的分析时主要参考的文章可以在这里找到:[用 LDA 进行主题建模:MLlib meets GraphX](https://databricks.com/blog/2015/03/25/topic-modeling-with-lda-mllib-meets-graphx.html) 。在那里，Joseph Bradley 恰当地描述了什么是主题建模，LDA 如何覆盖它，以及它可以用来做什么。我将尝试简要总结他的评论，并推荐您参考 Databrick 的博客和其他资源以获得更深入的报道。主题建模试图获取“文档”，无论它们是实际的文档、句子、推文等等，并推断文档的主题。LDA 试图通过将主题解释为在所有文档(语料库)中所有可能的单词(词汇)上的看不见的或潜在的分布来做到这一点。这最初是为文本分析开发的，但现在正被用于许多不同的领域。

## py spark 中的例子

这个例子将遵循 Databrick 的博客文章中给出的 LDA 例子，但是扩展到您可能正在使用的任何语料库应该是相当简单的。在本例中，我们将从 3 个新闻组中获取文章，使用`pyspark.mllib`的 LDA 功能对它们进行处理，看看我们能否通过识别 3 个不同的主题来验证该过程。

步骤是收集你的语料库。正如我前面提到的，我们将使用 3 个新闻组中的讨论。完整的集合可以在这里找到: [20 个新闻组](http://kdd.ics.uci.edu/databases/20newsgroups/20newsgroups.html)。在这个例子中，我选择了 3 个看起来截然不同的主题:

*   comp.os.ms-windows.misc
*   体育运动棒球
*   talk .宗教. misc

我提取了讨论的集合，然后将所有的讨论放入一个目录中，形成我的语料库。然后我们可以将 PySpark 脚本指向这个目录，将文档放入其中。在这个例子中使用的全部代码可以在这篇文章的底部找到。

第一段代码将初始化我们的 SparkContext:

```
from collections import defaultdict
from pyspark import SparkContext
from pyspark.mllib.linalg import Vector, Vectors
from pyspark.mllib.clustering import LDA, LDAModel
from pyspark.sql import SQLContext
import re

num_of_stop_words = 50 # Number of most common words to remove, trying to eliminate stop words num_topics = 3 # Number of topics we are looking for num_words_per_topic = 10 # Number of words to display for each topic max_iterations = 35 # Max number of times to iterate before finishing 
# Initialize sc = SparkContext('local', 'PySPARK LDA Example')
sql_context = SQLContext(sc) 
```

然后，我们将引入数据并对其进行标记，以形成我们的全球词汇表:

```
data = sc.wholeTextFiles('newsgroup/files/*').map(lambda x: x[1])

tokens = data \
    .map( lambda document: document.strip().lower()) \
    .map( lambda document: re.split("[\s;,#]", document)) \
    .map( lambda word: [x for x in word if x.isalpha()]) \
    .map( lambda word: [x for x in word if len(x) > 3] ) 
```

这里，我们通过执行以下操作来处理语料库:

1.  将每个文件作为单独的文档加载
2.  去除任何前导或尾随空白
3.  如果适用，将所有字符转换成小写
4.  将每个文档拆分成单词，用空格、分号、逗号和八叉线分隔
5.  只保留全部是字母字符的单词
6.  仅保留大于 3 个字符的单词

这样，我们就可以将每个文档表示为一个单词列表，这些单词有望比“the”、“and”以及其他我们怀疑与我们希望找到的主题无关的小单词更有洞察力。下一步是生成我们的全球词汇表:

```
# Get our vocabulary
# 1\. Flat map the tokens -> Put all the words in one giant list instead of a list per document
# 2\. Map each word to a tuple containing the word, and the number 1, signifying a count of 1 for that word
# 3\. Reduce the tuples by key, i.e.: Merge all the tuples together by the word, summing up the counts
# 4\. Reverse the tuple so that the count is first...
# 5\. ...which will allow us to sort by the word count 
termCounts = tokens \
    .flatMap(lambda document: document) \
    .map(lambda word: (word, 1)) \
    .reduceByKey( lambda x,y: x + y) \
    .map(lambda tuple: (tuple[1], tuple[0])) \
    .sortByKey(False) 
```

上述代码执行以下步骤:

1.  扁平化语料库，将所有单词聚合成一个巨大的单词列表
2.  用数字`1`映射每个单词，表示我们计算这个单词一次
3.  通过找到任何给定单词的所有实例，并将它们各自的计数相加，减少每个单词的计数
4.  反转每个元组，使单词计数在每个单词之前…
5.  …这样我们就可以根据每个单词的数量进行排序。

我们现在有了一个元组的排序列表，按照每个单词在语料库中出现的次数降序排列。然后，我们可以用它来删除最常见的单词，这些单词很可能是常见的单词(如“the”、“and”、“from”)，它们很可能与任何给定的主题都没有区别，而且在所有主题中出现的可能性也是一样的。然后，我们通过设置决定删除`k`数量的单词来确定要删除哪些单词，找到列表中深度为`k`的单词的计数，然后删除词汇表中出现该数量或更多的单词。之后，我们将索引每个单词，给每个单词一个唯一的 id，然后将它们收集到一个映射中:

```
# Identify a threshold to remove the top words, in an effort to remove stop words threshold_value = termCounts.take(num_of_stop_words)[num_of_stop_words - 1][0]

# Only keep words with a count less than the threshold identified above, 
# and then index each one and collect them into a map vocabulary = termCounts \
    .filter(lambda x : x[0] < threshold_value) \
    .map(lambda x: x[1]) \
    .zipWithIndex() \
    .collectAsMap() 
```

这给我们留下了一个词汇表，它由单词元组及其字数组成，最常用的单词被删除了。下一步是将每个文档表示为字数矢量。这意味着，不是每个文档都由单词序列组成，我们将拥有一个大小为全局词汇的列表，每个单元格的值是单词的计数，其 id 是该单元格的索引:

```
# Convert the given document into a vector of word counts def document_vector(document):
    id = document[1]
    counts = defaultdict(int)
    for token in document[0]:
        if token in vocabulary:
            token_id = vocabulary[token]
            counts[token_id] += 1
    counts = sorted(counts.items())
    keys = [x[0] for x in counts]
    values = [x[1] for x in counts]
    return (id, Vectors.sparse(len(vocabulary), keys, values))

# Process all of the documents into word vectors using the 
# `document_vector` function defined previously documents = tokens.zipWithIndex().map(document_vector).map(list) 
```

在实际开始运行模型之前，要做的最后一件事是反转我们的词汇表，以便我们可以根据 id 查找每个单词。这将让我们看到哪些单词与哪些主题密切相关:

```
# Get an inverted vocabulary, so we can look up the word by it's index value inv_voc = {value: key for (key, value) in vocabulary.items()} 
```

现在，我们打开一个输出文件，用期望的主题数量和最大迭代次数在语料库上训练我们的模型:

```
# Open an output file with open("output.txt", 'w') as f:
    lda_model = LDA.train(documents, k=num_topics, maxIterations=max_iterations)

    topic_indices = lda_model.describeTopics(maxTermsPerTopic=num_words_per_topic)

    # Print topics, showing the top-weighted 10 terms for each topic
    for i in range(len(topic_indices)):
        f.write("Topic #{0}\n".format(i + 1))
        for j in range(len(topic_indices[i][0])):
            f.write("{0}\t{1}\n".format(inv_voc[topic_indices[i][0][j]] \
                .encode('utf-8'), topic_indices[i][1][j]))

    f.write("{0} topics distributed over {1} documents and {2} unique words\n" \
        .format(num_topics, documents.count(), len(vocabulary))) 
```

显然，您可以获取输出，并做您想做的事情，但是这里我们将得到一个名为`output.txt`的输出文件，它将列出我们希望看到的三个主题中的每一个。您可以使用`num_topics`来查看模型的反应，但是因为我们知道我们围绕三个不同的主题进行讨论，所以我们认为有三个主题将通过围绕分别与这些主题相关的单词进行聚类来反映这一点。

接下来是收集“未标记的”数据(这可以称为标记的)，并使用 LDA 对您新发现的语料库进行主题建模。我仍在学习如何去做，但希望这对任何想开始使用 PySpark LDA 的人有所帮助。

* * *

## 附录:这是完整的脚本

```
from collections import defaultdict
from pyspark import SparkContext
from pyspark.mllib.linalg import Vector, Vectors
from pyspark.mllib.clustering import LDA, LDAModel
from pyspark.sql import SQLContext
import re

num_of_stop_words = 50 # Number of most common words to remove, trying to eliminate stop words num_topics = 3 # Number of topics we are looking for num_words_per_topic = 10 # Number of words to display for each topic max_iterations = 35 # Max number of times to iterate before finishing 
# Initialize sc = SparkContext('local', 'PySPARK LDA Example')
sql_context = SQLContext(sc)

# Process the corpus:
# 1\. Load each file as an individual document
# 2\. Strip any leading or trailing whitespace
# 3\. Convert all characters into lowercase where applicable
# 4\. Split each document into words, separated by whitespace, semi-colons, commas, and octothorpes
# 5\. Only keep the words that are all alphabetical characters
# 6\. Only keep words larger than 3 characters 
data = sc.wholeTextFiles('newsgroup/files/*').map(lambda x: x[1])

tokens = data \
    .map( lambda document: document.strip().lower()) \
    .map( lambda document: re.split("[\s;,#]", document)) \
    .map( lambda word: [x for x in word if x.isalpha()]) \
    .map( lambda word: [x for x in word if len(x) > 3] )

# Get our vocabulary
# 1\. Flat map the tokens -> Put all the words in one giant list instead of a list per document
# 2\. Map each word to a tuple containing the word, and the number 1, signifying a count of 1 for that word
# 3\. Reduce the tuples by key, i.e.: Merge all the tuples together by the word, summing up the counts
# 4\. Reverse the tuple so that the count is first...
# 5\. ...which will allow us to sort by the word count 
termCounts = tokens \
    .flatMap(lambda document: document) \
    .map(lambda word: (word, 1)) \
    .reduceByKey( lambda x,y: x + y) \
    .map(lambda tuple: (tuple[1], tuple[0])) \
    .sortByKey(False)

# Identify a threshold to remove the top words, in an effort to remove stop words threshold_value = termCounts.take(num_of_stop_words)[num_of_stop_words - 1][0]

# Only keep words with a count less than the threshold identified above, 
# and then index each one and collect them into a map vocabulary = termCounts \
    .filter(lambda x : x[0] < threshold_value) \
    .map(lambda x: x[1]) \
    .zipWithIndex() \
    .collectAsMap()

# Convert the given document into a vector of word counts def document_vector(document):
    id = document[1]
    counts = defaultdict(int)
    for token in document[0]:
        if token in vocabulary:
            token_id = vocabulary[token]
            counts[token_id] += 1
    counts = sorted(counts.items())
    keys = [x[0] for x in counts]
    values = [x[1] for x in counts]
    return (id, Vectors.sparse(len(vocabulary), keys, values))

# Process all of the documents into word vectors using the 
# `document_vector` function defined previously documents = tokens.zipWithIndex().map(document_vector).map(list)

# Get an inverted vocabulary, so we can look up the word by it's index value inv_voc = {value: key for (key, value) in vocabulary.items()}

# Open an output file with open("output.txt", 'w') as f:
    lda_model = LDA.train(documents, k=num_topics, maxIterations=max_iterations)
    topic_indices = lda_model.describeTopics(maxTermsPerTopic=num_words_per_topic)

    # Print topics, showing the top-weighted 10 terms for each topic
    for i in range(len(topic_indices)):
        f.write("Topic #{0}\n".format(i + 1))
        for j in range(len(topic_indices[i][0])):
            f.write("{0}\t{1}\n".format(inv_voc[topic_indices[i][0][j]] \
                .encode('utf-8'), topic_indices[i][1][j]))

    f.write("{0} topics distributed over {1} documents and {2} unique words\n" \
        .format(num_topics, documents.count(), len(vocabulary))) 
```