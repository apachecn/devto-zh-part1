# TrailDB 上百万行的冒险，Python 和 Go

> 原文：<https://dev.to/rhymes/adventures-in-traildb-with-millions-of-rows-python-and-go>

存储和查询大量数据并不是很多项目或公司必须处理的问题，但在某种程度上，如果一个产品很受欢迎，很多用户觉得它很有用，这种情况可能会发生。

您可能会自然地围绕关系数据库系统设计您的应用程序，并根据您自己的需求和问题使用许多技术来进行水平或垂直(或两者)伸缩。

你用事件数据做什么？这些信息可能是成批收集的，也可能是实时收集的，但是在一天结束时，您会如何处理属于用户的所有这些信息呢？

事件数据可以是来自传感器、你自己的移动应用、排队系统、用户界面、分析、访问日志等等的数据。

我将“事件数据”定义为与特定所有者相关的一系列按时间顺序发生的事件。

这些数据可能会出现在许多不同的地方，例如:

*   关系数据库中的一组表(如 PostgreSQL 或 MySQL)
*   流数据平台(如 Apache Kafka 或 Amazon Kinesis)
*   时间序列数据库(如 InfluxDB)

在本文中，我想探索一种不同的工具，在某些特定的情况下，它可能是适合这项工作的工具:TrailDB。

## 什么是 TrailDB

这里我要引用[出处](http://traildb.io/):

> TrailDB 是一个用 C 实现的库，它允许你以极快的速度查询一系列事件。

TrailDB 由 AdRoll 开发，用于处理 Pb 级的数据量。

不是每个人都必须处理这样的扩展挑战，但我认为 TrailDB 即使对于较小的数据集也有很多有趣的东西。

## trail db 是如何工作的

TrailDB 有两种主要模式:构造和查询。

在构建阶段，所有要摄取的事件数据都被传递到库，库在内部将数据分成一组轨迹。每个踪迹是一系列按时间顺序排列的事件，由您选择的唯一标识符索引。

所有这些摄取的数据都经过处理、排序和压缩，以最小化占用的空间。结果是一个只读文件准备好被查询。

## 如何创建 TrailDB

在我的实验中，我导出了一个包含超过 1.39 亿个事件的表，这些事件被分成两个 CSV 文件(不要在您最喜欢的编辑器中打开它们，即使是 Sublime :-D 也不行)。

第一个文件占用 29 GB，第二个文件占用 30 GB。

TrailDB 有各种编程语言的绑定:C、D、R、Haskell、Rust、Python 和 Go。

在所有这些语言中，我更熟悉的是 Python，所以这是我的起点。即使 TrailDB 自己的[教程](http://traildb.io/docs/tutorial/#part-ii-analyze-a-large-traildb-of-wikipedia-edits)警告不要对海量数据使用 Python。

来看看是怎么做到的:

```
COLUMNS = OrderedDict(zip(
    ['YOUR', 'LIST', 'OF', 'COLUMNS', 'IN', 'THE', 'CSV'],
     range(0, NUMBER_OF_COLUMNS)
))

def create_trail_db(input_path, output_path, columns):
    traildb = TrailDBConstructor(output_path, COLUMNS.keys())

    with open(input_path) as input_file:
        reader = csv.reader(input_file, encoding='utf-8', escapechar='\\')

        for line in reader:
            event_uuid = line[COLUMNS['event_uuid']]
            event_time = line[COLUMNS['event_time']]

            # I should probably use a regular expression here but I am lazy
            # and this is just an experiment
            try:
                time = datetime.strptime(event_time, '%Y-%m-%d %H:%M:%S.%f')
            except ValueError:
                time = datetime.strptime(event_time, '%Y-%m-%d %H:%M:%S')

            utf8_encoded_values = [value.encode('utf-8') for value in line]
            traildb.add(event_uuid, time, utf8_encoded_values)

    traildb.finalize() 
```

Enter fullscreen mode Exit fullscreen mode

基本上，你打开一个构造函数，迭代你的文件，传递一个 uuid 和一个时间戳给 traildb，然后调用 finalize。非常非常简单。正如你注意到的，我必须使用 Python 2，因为 TrailDB 不支持 Python 3。

一些注释和统计数据:

*   输入 CSV 文件在磁盘上是 29 GB
*   输出的`.tdb`文件为 4.4 GB
*   在我配有 16 GB 内存和固态硬盘的 Macbook Pro 上，制作过程花了 3 小时 5 分钟
*   在那 3 个小时里，我正常使用电脑做其他事情

因为我不想为数据集的后半部分再等 3 个小时，所以我决定在 Go 中重写 Python 脚本。这是我第一次尝试在教程之外编写 Go 代码，所以这可能不是最好的实现，但是 TrailDB 库在 Go 中也很简单:

```
// I skipped all the error checking for brevity
func create_trail_db(input_path string, output_path string, columns []string) {
    traildb, err := tdb.NewTrailDBConstructor(output_path, columns...)
    input_file, err := os.Open(input_path)
    defer input_file.Close()

    dialect := csv.Dialect{
        Delimiter:   ',',
        Quoting:     csv.NoDoubleQuote,
        DoubleQuote: csv.NoDoubleQuote,
    }
    reader := csv.NewDialectReader(input_file, dialect)
    for {
        record, err := reader.Read()

        event_uuid := record[indexOf("event_uuid", columns)]
        event_time := record[indexOf("event_time", columns)]

        timestamp, err := strptime.Parse(event_time, "%Y-%m-%d %H:%M:%S.%f")
        if err != nil {
            timestamp, _ = strptime.Parse(event_time, "%Y-%m-%d %H:%M:%S")
        }

        traildb.Add(event_uuid, timestamp.Unix(), record)
    }

    traildb.Finalize()
    traildb.Close()
} 
```

Enter fullscreen mode Exit fullscreen mode

解析第二个文件花了 1 小时 30 分钟，比 Python 解析的前一个文件多了 49.3 万行。

关于整个创作过程的几点说明:

*   压缩效果非常好，59 GB 原始源材料总共压缩了 8.8 GB
*   围棋花了一半的时间做更多的事情
*   我敢肯定，很多时间都花在解析 CSV 中各行的时间戳上，所以时间戳已经以秒为单位，这样可能会更快

## 如何查询 TrailDB

所以现在我们有两个文件，比如说`a.tdb`和`b.tdb`，每个文件占用大约 4.4 GB，放在一个文件夹中准备进行分析。我们如何做到这一点？

原来有很多方法，从“请不要看这个愚蠢的代码”的方法到那些可以在分治架构中扩展的方法。

### 查询条件

如果我们想为我们的冒险查询一些东西，我们需要决定寻找什么。在 SQL 中，这个实验的条件如下:

```
SELECT *
FROM ab_tests
WHERE ab_test_id = '1234'
AND action_type IN ('clicked_button_red', 'clicked_button_green') 
```

Enter fullscreen mode Exit fullscreen mode

也可能是别的什么，这只是一个伪例子。

### 设置哑参考点

由于我需要一个不切实际的参考点，以便能够在实际使用 TrailDB 时发出“哇”的一声，所以我决定用 Python 编写一个哑脚本，遍历文件中所有数千万行，并手动检测结果。自己看:

```
tdb = TrailDB(tdb_filepath)

...

def action_data(tdb, action_types, action_types):
    for uuid, trail in tdb.trails():
        for event in trail:
            if event.ab_test_id in ab_test_ids and event.action_type in action_types:
                yield event

count = 0
for event in action_data(tdb, ab_test_ids, action_types):
    print event
    count += 1 
```

Enter fullscreen mode Exit fullscreen mode

```
db, err := tdb.Open(tdb_filepath)

func action_data(db *tdb.TrailDB, ab_test_ids map[string]bool, action_types map[string]bool) int {
    trail, err := tdb.NewCursor(db)

    count := 0
    for i := uint64(0); i < db.NumTrails; i++ {
        err := tdb.GetTrail(trail, i)

        for {
            evt := trail.NextEvent()
            evtMap := evt.ToMap()
            if (ab_test_ids[evtMap["ab_test_id"]]) && (action_types[evtMap["action_type"]]) {
                evt.Print()
                count += 1
            }
        }
    }

    return count
} 
```

Enter fullscreen mode Exit fullscreen mode

以下是计时:

```
 $ time python query_db_naive.py a.db
4105.14s user 49.11s system 93% cpu 1:14:19.35 total

$ time go run query_db_naive.go a.db
538.53s user 8.66s system 100% cpu 9:05.35 total 
```

Enter fullscreen mode Exit fullscreen mode

Python 用了 1 小时 14 分，Go 用了 9 分 5 秒。他们都找到了我要找的 216 个事件。

第二个文件我跳过了 Python，因为我不想在等待中死去。Go 用了不到 12 分钟就找到了第二个文件中的 220 个事件(我提醒你这个文件比第一个文件大)。

### 使用 TrailDB 过滤器 API 查询

TrailDB 允许开发者为查询创建[过滤器](http://traildb.io/docs/technical_overview/#return-a-subset-of-events-with-event-filters)。

例如，您可以使用它们在单独的 TrailDB 文件中快速提取与一组条件匹配的行，或者只是，您知道，在时间结束之前找到一些东西并做一些事情，就像上面的例子一样。

我使用过滤器重写了两个脚本:

```
 query = [
    [('ab_test_id', value) for value in ab_test_ids],
    [('action_type', value) for value in action_types]
]
count = 0
for uuid, trail in tdb.trails(event_filter=query):
    for event in trail:
        print event
        count += 1 
```

Enter fullscreen mode Exit fullscreen mode

`tdb.trails()`接受过滤器的 Python 列表，只返回匹配的行。每个过滤器都是一个列表，查询中的所有列表都在`AND`中，单个列表中的每一项都在`OR`中。

让我们在围棋中看到同样的东西:

```
 query := [][]tdb.FilterTerm{
  {
    tdb.FilterTerm{Field: "ab_test_id", Value: "2767"},
  },
  {
    tdb.FilterTerm{Field: "action_type", Value: "clicked_button_red"},
    tdb.FilterTerm{Field: "action_type", Value: "clicked_button_green"},
  },
}
filter := db.NewEventFilter(query)
db.SetFilter(filter) 
```

Enter fullscreen mode Exit fullscreen mode

以下是第一个文件的结果:

```
 $ time python query_db_filter.py a.tdb
14.36s user 0.63s system 97% cpu 15.362 total

$ time go run query_db_filter.go a.tdb
5.75s user 0.53s system 97% cpu 6.459 total

# precompiled go binary
$ time ./query_db_filter a.tdb
5.42s user 0.39s system 97% cpu 5.945 total 
```

Enter fullscreen mode Exit fullscreen mode

第二个文件的结果:

```
 $ time python query_db_filter.py b.tdb
14.13s user 0.61s system 96% cpu 15.257 total

$ time go run query_db_filter.go b.tdb
5.82s user 0.79s system 92% cpu 7.194 total

# precompiled go binary
$ time ./query_db_filter b.tdb
5.60s user 0.43s system 97% cpu 6.192 total 
```

Enter fullscreen mode Exit fullscreen mode

我们已经知道我们的基线是无意义的，但我们仍然可以得出一些结论:

*   Go 的例子比 Python 快 2，2.6 倍
*   TrailDB 过滤器真的很快，尽管创建复杂的条件可能需要一点准备
*   事情可能会更快，分割路径，并行查找项目，然后连接结果。就像在“地图缩小”算法中一样。

### 使用 TrailDB 命令行工具查询

TrailDB 附带了一个用 C 编写的命令行工具，可以用来创建数据库、过滤、创建索引、合并等等。

所以我自然想看看查询有多快:

```
 $ time tdb dump --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i a.tdb
6.47s user 0.45s system 96% cpu 7.176 total

$ time tdb dump --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i b.tdb
5.66s user 0.46s system 96% cpu 6.359 total 
```

Enter fullscreen mode Exit fullscreen mode

速度绝对与我们的 Go 参考实现不相上下。

### 使用 TrailDB 命令行工具和准备好的索引进行查询

命令行工具有一个简洁的特性，允许预先构建一个与我们的过滤器相匹配的索引。索引保存在磁盘上 traildb 文件的旁边。

首先我们创建索引:

```
 $ time tdb index --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i a.tdb
Index created successfully at a.tdb.index in 286 seconds.
346.05s user 12.63s system 125% cpu 4:45.99 total

$ du a.tdb.index
645M  a.tdb.index 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们再次运行上一段中的相同查询:

```
 $ time tdb dump --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i a.tdb
0.06s user 0.01s system 39% cpu 0.185 total 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它非常快。我们从 6.47 秒到 0.06 秒

但是有一个问题:我找不到使用编程语言索引的方法。它只能由不理想的命令行工具使用。

## 如何合并多个 TrailDBs

我们现在知道如何创建一个 traildb，如何从中提取数据，但我们仍然不知道如何将两个 traildb 文件合并成一个。

我创建了另一对要合并的脚本:

```
 output_tdb = TrailDBConstructor(output_path, columns)

for tdb_filepath in tdb_filepaths:
    tdb = TrailDB(tdb_filepath)
    output_tdb.append(tdb) 
```

Enter fullscreen mode Exit fullscreen mode

```
 output_tdb, err := tdb.NewTrailDBConstructor(output_path, columns...)

for i := 0; i < len(tdb_filepaths); i++ {
  tdb_filepath := tdb_filepaths[i]

  db, err := tdb.Open(tdb_filepath)

  err = output_tdb.Append(db)

  db.Close()
}

output_tdb.Finalize()
output_tdb.Close() 
```

Enter fullscreen mode Exit fullscreen mode

然后我运行了它们:

```
 $ time python merge_dbs.py final.tdb a.tdb b.tdb
1044.97s user 1260.73s system 27% cpu 2:22:08.29 total

$ time go run merge_dbs.go final.tdb a.tdb b.tdb
960.93s user 1149.51s system 22% cpu 2:33:54.73 total 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查一下事件是否都在那里:

```
 >>> from traildb import TrailDB
>>> tdb = TrailDB('final.tdb')
>>> tdb.num_events
139017085L 
```

Enter fullscreen mode Exit fullscreen mode

注意:Go 可能比 Python 慢 11 分钟，因为我当时在 S3 桶上上传千兆字节，同时时间机器的备份也开始了...这些测试肯定是不科学的:-D

合并是一个缓慢的操作，我认为库必须为每个 TrailDB 解压缩数据，并在最后一个中重新压缩它。

在我的 16 GB 内存的笔记本电脑上，操作非常密集。这个过程像疯了一样交换，但是我没有在专用机器上运行这些脚本。

合并可能只是为了合并小文件。

## 查询 1.39 亿个事件

现在我们已经创建了包含 1.39 亿个事件的“最终”文件，我很想知道 Python 和 Go 的速度有多快:

```
 $ time python query_db_filter.py final.tdb
29.56s user 2.65s system 80% cpu 40.223 total

$ time go run query_db_filter.go final.tdb
11.63s user 1.02s system 95% cpu 13.303 total 
```

Enter fullscreen mode Exit fullscreen mode

## 额外内容:如何在亚马逊 S3 上查询 TrailDB 文件

TrailDB 有一个[实验特性](http://tech.adroll.com/blog/data/2016/11/29/traildb-mmap-s3.html)，在一个单独的版本中，它可以使用 Linux[userfaultfd()](https://lwn.net/Articles/615086/)syscall 来利用页面错误从网络(远程 S3 文件)获取数据块，将这些数据块映射到本地内存中，并将它们缓存在磁盘上。

它提供了一个服务器进程，将页面错误转换为网络调用，以获取 S3 上的部分文件。我不知道这是可以做到的，当我读到这篇文章时，我惊讶得目瞪口呆。

多酷啊。我必须弄清楚它是否有效(现在你知道我为什么在进行之前的实验时上传东西到 S3 了)。

我设置了一台 EC2 机(t1.micro + 8GB SSD gp2 卷)，在上面安装了 traildb，开始播放。

我的实验配置是:

*   16MB 的块大小(进程在网络上从文件中获取的数据量)
*   磁盘上 1GB 的最大占用空间(本地缓存的最大大小)

```
 time tdb dump --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i s3://something/a.tdb
real    2m27.941s
user    0m6.348s
sys     0m3.400s 
```

Enter fullscreen mode Exit fullscreen mode

由于明显的原因比本地版本慢，但是看看当你有一些本地缓存数据时会发生什么:

```
 time tdb dump --filter='ab_test_id=2767 & action_type=clicked_button_red action_type=clicked_button_green' -i s3://something/a.tdb
real    0m52.742s
user    0m6.496s
sys     0m3.680s 
```

Enter fullscreen mode Exit fullscreen mode

不错吧？

这个特性有用的原因是，在同样的情况下，我们可能有比磁盘存储更大的 TrailDB 文件，所以我们需要一种方法来查询数据，而不需要本地可用的文件。

## 奖励内容:用 reel 查询 TrailDB，一种实验性语言

TrailDB 提供了一个多线程工具 reel，可用于计算一系列事件的指标，例如跳出率或漏斗分析。

它对 TrailDB 文件进行操作，而不对它们进行任何修改。这只是另一种提取信息的方式。

一个卷轴查询看起来像这样:

```
 var Events uint

if $ab_test_id $ab_test_id='2767':
   if $action_type $action_type='clicked_button_red' or if $action_type $action_type='clicked_button_green':
      inc Events 1 
```

Enter fullscreen mode Exit fullscreen mode

这是 2 线程和 4 线程的结果:

```
 $ time ./reel query.rl -P -T 2 a.tdb
[thread 0] 0% trails evaluated
[thread 0] 44% trails evaluated
[thread 0] 88% trails evaluated
[thread 0] 100% trails evaluated
Events
216

7.39s user 0.42s system 178% cpu 4.371 total 
```

Enter fullscreen mode Exit fullscreen mode

```
 $ time ./reel query.rl -P -T 4 a.tdb
[thread 0] 0% trails evaluated
[thread 0] 44% trails evaluated
[thread 0] 88% trails evaluated
[thread 0] 100% trails evaluated
Events
216

9.98s user 0.54s system 295% cpu 3.560 total 
```

Enter fullscreen mode Exit fullscreen mode

所以，既然它或多或少和我们的 Go 和 Python 脚本一样快，为什么还要学习另一个工具呢？因为 reel 可以用来处理不同事件、[之间的](https://github.com/traildb/reel#control-flow)[时间流逝，它有控制流](https://github.com/traildb/reel#handling-time)和[它可以分组结果](https://github.com/traildb/reel#grouping-by-splitting-contexts)。

还有另一个非实验性的工具， [trck](https://github.com/traildb/trck) ，这是一个带有状态机的完整的查询引擎，但我无法让它在 OSX 上工作，所以我将方便地把测试作为练习留给读者；-)

## 考虑事项和结论

如果使用得当，TrailDB 真的很快，我认为最终的文件是只读的更好。成为公司数据工作流的一部分需要一些工作，但我认为这是一个值得探索的工具，它可以通过分析大量数据来控制成本，因为“大数据”和基本上没有人再删除任何东西的事实，所有公司都开始拥有这些数据。

我肯定会推荐使用 Go over Python 来操作 TrailDB。

正如我在开始时所说，TrailDB 是由 AdRoll 创建的，ad roll 使用它来处理 Pb 级的数据量。我认为这是一个值得了解的工具(或者至少知道它的存在)，尽管目前我觉得它还有点粗糙。此外，该社区似乎非常小，如果我没有弄错的话，开源项目由 AdRoll 的开发人员维护，这可能会使该项目受到他们公司的业务需求和时间表的驱动(最新版本是 0.6，于 2017 年 5 月发布)。

无论如何，如果你有兴趣了解更多，这里有几个链接:

*   [宣布 TrailDB](http://tech.adroll.com/blog/data/2016/05/24/traildb-open-sourced.html)
*   [trail db 简介](http://slides.com/villetuulos/intro-to-traildb#/)