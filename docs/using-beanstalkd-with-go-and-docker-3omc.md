# 将 Beanstalkd 与 Go 和 Docker 一起使用

> 原文：<https://dev.to/asaf_g6/using-beanstalkd-with-go-and-docker-3omc>

## 为什么是 Beanstalkd？

有很多工作队列，选择使用哪一个通常是一个艰难的选择。我猜你们中的很多人都去过那里，花了几个小时想出在你的下一个项目或功能中使用哪种技术。列出每个系统的优缺点，把重点放在你真正需要的系统上。

[Beanstalkd](http://kr.github.io/beanstalkd/) 为排队问题提供了一个轻量级的快速解决方案——它是事务性的、持久的并且非常简单。另一方面，它有自己的协议(所以很难替换)，它是“在客户端级别”扩展的，这意味着不支持冗余，并且它没有任何安全性。哦，它是开源的——这总是好的。
注意，你可以使用类似[ghost tunnel](https://github.com/square/ghostunnel)的东西作为安全层。

因此，如果简单性和速度对你有吸引力，并且你在一个封闭的网络中运行(所以你不需要安全), Beanstalkd 可能就是适合你的！

## 带它兜风

我将使用 docker 来运行服务器，所以如果你没有 Docker，你可以到这里。你也可以在本地安装 Beanstalkd，这里有[的指示](http://kr.github.io/beanstalkd/)。

用 docker 运行 Beanstalkd:

```
$ docker run -d -p 11300:11300 schickling/beanstalkd 
```

Enter fullscreen mode Exit fullscreen mode

验证它正在运行:

```
docker ps
CONTAINER ID        IMAGE                   COMMAND                 CREATED             STATUS              PORTS                      NAMES
1b67ae4804ca        schickling/beanstalkd   "beanstalkd -p 11300"   3 seconds ago       Up 1 second         0.0.0.0:11300->11300/tcp   zealous_noether 
```

Enter fullscreen mode Exit fullscreen mode

太好了！它在跑。Beanstalkd 最酷的一点是它的协议是明文的。
所以我们可以用 telnet 踢轮胎。

如果你愿意，可以在这里阅读协议文档[。](https://github.com/kr/beanstalkd/blob/master/doc/protocol.txt) 

```
$ telnet 127.0.0.1 11300
Trying 127.0.0.1...
Connected to 127.0.0.1.
Escape character is '^]'. 
```

Enter fullscreen mode Exit fullscreen mode

**stats** 命令打印出系统的统计信息:

```
stats
\-\-\-
OK 899
current-jobs-urgent: 0
current-jobs-ready: 0
current-jobs-reserved: 0
current-jobs-delayed: 0
current-jobs-buried: 0
cmd-put: 0
cmd-peek: 0
cmd-peek-ready: 0
cmd-peek-delayed: 0
cmd-peek-buried: 0
cmd-reserve: 0
cmd-reserve-with-timeout: 0
cmd-delete: 0
cmd-release: 0
cmd-use: 0
cmd-watch: 0
cmd-ignore: 0
cmd-bury: 0
cmd-kick: 0
cmd-touch: 0
cmd-stats: 1
cmd-stats-job: 0
cmd-stats-tube: 0
cmd-list-tubes: 0
cmd-list-tube-used: 0
cmd-list-tubes-watched: 0
cmd-pause-tube: 0
job-timeouts: 0
total-jobs: 0
max-job-size: 65535
current-tubes: 1
current-connections: 1
current-producers: 0
current-workers: 0
current-waiting: 0
total-connections: 1
pid: 1
version: 1.10
rusage-utime: 0.020000
rusage-stime: 0.003000
uptime: 491
binlog-oldest-index: 0
binlog-current-index: 0
binlog-records-migrated: 0
binlog-records-written: 0
binlog-max-size: 10485760
id: fd7af7e8d3eb1a86
hostname: 1b67ae4804ca 
```

Enter fullscreen mode Exit fullscreen mode

## 队列流动

有制作人，有工人，有管。

试管被命名为作业队列，您可以从两端使用。
总的来说——生产商把工作放到管子里，工人处理工作，并把它们标记为完成。听起来像工作队列，对吗？

一个岗位有四种状态:

1.  就绪-任务已准备好由工人消耗
2.  保留-其中一名员工已保留该职务，并且正在处理中
3.  延迟-作业正在等待准备就绪
4.  掩埋-任务存储在一个 FIFO 列表中，等待直到一个工人发出一个 kick 命令。更多信息请见下文。

### 监制

制片人挑选了一个试管，并在其中放入了一个任务。当这种情况发生时，生产者可以决定几个选项:

*   优先级-整数，数字越小意味着越紧急。
*   delay -整数，在作业准备好运行之前等待的秒数。
*   运行时间-整数，允许工作线程运行作业的秒数。如果工作没有在这段时间内完成，它将被释放，另一个工人可以接受它。

### 职工

工人决定观察哪些试管，然后发出保留命令。这将返回一个新的保留作业，保留意味着只有该特定工人将获得该作业。

作业执行后，工人发出删除命令，将该作业从队列中删除。如果工人不能完成作业，它也可以释放它，实际上是将作业放回队列中。员工还可以“隐藏”职务，这将使职务处于“隐藏”状态。“被埋”的作业将保持被埋状态，直到其中一个工人发出“踢”命令，使作业再次“准备好”。

## Go 客户端

Beanstalkd 的协议非常简单，这使得编写自己的客户端非常有吸引力。这主意不错，但是等等...我们不应该重新发明轮子..对吗？嗯，有一堆[客户端库](https://github.com/kr/beanstalkd/wiki/client-libraries)可以用于各种编程语言。既然我已经选择使用 [Go](https://golang.org/) ，我将选择其中一个 Go 客户端库。[豆茎](https://github.com/iwanbk/gobeanstalk)看起来不错，我们就买那个吧。

## 示例 App

在你的 Go 应用程序中，你可以使用 Beanstalkd 来发送电子邮件，处理一些数据，调整猫图片的大小或者任何你的应用程序需要的东西。因为我们的目的是学习，所以我们将实现一个保存评论的应用程序。生产者将把评论推到队列中，而工人将把评论保存到磁盘上。

注意，为了简单起见，我将我们所有的文件都放在主包中，您可能希望对代码库进行不同的划分。有一篇关于如何命名和打包你的围棋程序元素的博文。

让我们从定义数据结构和接口开始。第一个是评论。

**common . go**T2】

```
package main

import "time"

type Comment struct {
    UserName string
    Date time.Time
    Text string
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要能够通过 Beanstalkd 传递我们的评论，Beanstalkd 使用字符串作为作业主体。
我们将通过一个接口来实现这一点，这样我们的协议就可以有多个实现(尽管我们只有一个):

**common . go**T2】

```
type CommentProtocol interface {
    Decode(encodedComment []byte) (*Comment, error)
    Encode(comment *Comment) ([]byte, error)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的 CommentProtocol 接口的实现将使用 JSON:

**JSON _ protocol . go**

```
package main

import "encoding/json"

type JsonCommentProtocol struct {

}

func (protocol *JsonCommentProtocol) Decode(encodedComment []byte) (*Comment, error) {
    unCodedComment := Comment{}
    err := json.Unmarshal(encodedComment, &unCodedComment)
    if err != nil {
        return nil, err
    }
    return &unCodedComment, nil
}

func (protocol *JsonCommentProtocol) Encode(comment *Comment) ([]byte, error) {
    encodedComment, err := json.Marshal(comment)
    if err != nil {
        return nil, err
    }
    return encodedComment, nil
}

func MakeJsonCommentProtocol() *JsonCommentProtocol {
    return &JsonCommentProtocol{}
} 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要定义在员工端获得数据后如何处理这些数据。我们将为此使用一个接口:

**common . go**T2】

```
type CommentProcessor interface {
    DoProcess(comment *Comment) error
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的虚拟实现:

**comment_processor.go**

```
package main

import (
    "os"
    "fmt"
    "time"
)

type DiskCommentProcessor struct {
    dir string
}

func (processor *DiskCommentProcessor) DoProcess(comment *Comment) error {
    filePath := fmt.Sprintf("%s/%s_%s.txt", processor.dir, comment.Date.Format(time.RFC3339), comment.UserName)
    commentFile, err := os.Create(filePath)
    if err != nil {
        return err
    }
    defer commentFile.Close()
    commentFile.Write(comment.Text)
    return nil
}

func MakeNewCommentProcessor(dir string) *DiskCommentProcessor {
    return &DiskCommentProcessor{dir: dir}
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，它没有做太多，但对我们来说已经足够了。

现在我们已经定义了我们的注释数据结构，如何将它作为文本传递以及如何处理它，我们可以开始使用 Beanstalkd 了。当然，我们需要先*得到*库。

```
$ go get github.com/iwanbk/gobeanstalk 
```

Enter fullscreen mode Exit fullscreen mode

因为我们想保持干燥，我们将实现一个结构来处理生产者和工人都将拥有的公共函数。

**豆茎 _common.go**

```
package main

import (
    "github.com/iwanbk/gobeanstalk"
    "fmt"
    "os"
)

type PapaBeanstalk struct {
    ServerAddress string
    serverConnection *gobeanstalk.Conn
}

func (papa *PapaBeanstalk) Connect() {
    beanstalkConnection, err := gobeanstalk.Dial(papa.ServerAddress)
    if err != nil {
        // do retries or whatever you need
        fmt.Println(err)
        os.Exit(1)
    }
    fmt.Println("connected!")
    papa.serverConnection = beanstalkConnection
}

func (papa *PapaBeanstalk) Close() {
    if papa.serverConnection != nil {
        papa.serverConnection.Quit()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们实现生产者:

**producer . go**T2】

```
package main

import (
    "time"
    "fmt"
)

type Producer struct {
    PapaBeanstalk
    protocol CommentProtocol
}

func (producer *Producer) PutComment(comment *Comment) error {
    body, err := producer.protocol.Encode(comment)
    if err != nil {
        return err
    }
    priority := uint32(10)
    delay := 0 * time.Second
    time_to_run := 20 * time.Second
    jobId, err := producer.serverConnection.Put(body, priority, delay, time_to_run)
    if err != nil {
        return err
    }
    fmt.Println("inserted Job id: ", jobId)
    return nil
}

func MakeNewProducer(serverAdress string, protocol CommentProtocol) *Producer {
    producer := Producer{protocol: protocol}
    producer.ServerAddress = serverAdress
    return &producer
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们将 PapaBeanstalk 嵌入到生产者结构中，使 Connect()和 Close()函数对其可用。如果你是新手，并且更习惯于使用继承，这对你来说似乎有点奇怪。但事实是，围棋没有继承，这也一样有效。

你大概注意到了，我们没有使用任何试管！Beanstalkd 默认使用“默认”管道，所以我们同时也很好。

我们下一步将实现的工人:

**worker.go**

```
package main

import (
    "fmt"
    "time"
    "github.com/iwanbk/gobeanstalk"
)

type CommentWorker struct {
    PapaBeanstalk
    protocol CommentProtocol
    processor CommentProcessor
}

func (worker *CommentWorker) ProcessJob() {
    fmt.Println("reserving job")
    job, err := worker.serverConnection.Reserve()
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println("got job Id: ", job.ID)
    comment, err := worker.protocol.Decode(job.Body)
    if err != nil {
        worker.handleError(job, err)
        return
    }
    processError := worker.processor.DoProcess(comment)
    if processError != nil {
        worker.handleError(job, err)
        return
    }
    fmt.Println("processed job id: ", job.ID)
    worker.serverConnection.Delete(job.ID)
}

func (worker *CommentWorker) handleError(job *gobeanstalk.Job, err error) {
    fmt.Println(err)
    priority := uint32(5)
    delay := 0 * time.Second
    worker.serverConnection.Release(job.ID ,priority, delay) // hey I can't handle this
}

func MakeNewWorker(serverAddress string, protocol CommentProtocol, processor CommentProcessor) *CommentWorker {
    worker := CommentWorker{protocol: protocol, processor: processor}
    worker.ServerAddress = serverAddress
    return &worker
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用的是 reserve 命令，它将一直阻塞，直到有作业返回。然后在我们得到一份工作后，我们试着解码并处理它。如果出错，我们将作业释放回队列，如果成功，我们将删除作业。

发送释放命令时，您可以决定是否要更改作业的优先级，或给它一个延迟。

如果您想要或需要轮询，而不是阻塞直到任务到来，您可以使用“reserve-with-timeout”命令，该命令在超时后返回一个错误。

现在我们既有了工人又有了生产者，我们只需要主函数来使它工作。

```
package main

import (
    "time"
    "os"
    "fmt"
)

func ProducerMain() {
    var comments = []Comment{
        {UserName: "some_user", Text:"i love your cat", Date: time.Now()},
        {UserName: "some_other_user", Text:"i prefer dogs", Date: time.Now()},
        {UserName: "another_user", Text:"please close this thread", Date: time.Now()},
        {UserName: "admin", Text:"thread closed - not relevant", Date: time.Now()},
    }
    protocol := MakeJsonCommentProtocol()
    producer := MakeNewProducer("localhost:11300", protocol)
    producer.Connect()
    defer producer.Close()
    for _, comment := range comments {
        producer.PutComment(&comment)
    }
}

func WorkerMain() {
    protocol := MakeJsonCommentProtocol()
    commentsDir := "./comments"
    os.Mkdir(commentsDir, 0777)
    processor := MakeNewCommentProcessor(commentsDir)
    worker := MakeNewWorker("localhost:11300", protocol, processor)
    worker.Connect()
    defer worker.Close()
    for {
        worker.ProcessJob()
    }

}

func printUsage() {
    fmt.Println("Usage: example-app worker/producer")
    os.Exit(1)
}

func main() {
    if len(os.Args) < 2 {
        printUsage()
    }
    if os.Args[1] == "worker" {
        WorkerMain()
    } else if os.Args[1] == "producer" {
        ProducerMain()
    } else {
        printUsage()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是什么？

*   带有一些虚拟数据的生产者主函数
*   workerman 函数具有无限循环
*   使我们能够在生产者和工人之间进行选择的主要功能

让我们构建我们新的令人惊叹的应用程序:

```
$ go build 
```

Enter fullscreen mode Exit fullscreen mode

该命令将构建一个目录名为**的可执行文件，例如 app**

我们可以为生产者和工人使用相同的可执行文件。

打开一个终端窗口，用“生产者”参数运行它:

```
$ ./example-app producer 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似这样的内容:

```
connected!
inserted Job id:  1
inserted Job id:  2
inserted Job id:  3
inserted Job id:  4
connection closed 
```

Enter fullscreen mode Exit fullscreen mode

在另一个终端窗口中使用“worker”参数运行它:

```
$ ./example-app worker 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似这样的内容:

```
connected!
reserving job
got job Id:  1
processed job id:  1
reserving job
got job Id:  2
processed job id:  2
reserving job
got job Id:  3
processed job id:  3
reserving job
got job Id:  4
processed job id:  4
reserving job 
```

Enter fullscreen mode Exit fullscreen mode

它正在运行一个无限循环，所以键入 ctrl-C 来停止它。

太好了！我们现在有了一个非常简单的 Beanstalkd 应用程序。

但是等等...那些管子呢？让我们修改我们的应用程序以使用管道。

我们将向 worker 结构添加一个管变量:

**worker.go**

```
type CommentWorker struct {
    PapaBeanstalk
    protocol CommentProtocol
    processor CommentProcessor
    tube string
} 
```

Enter fullscreen mode Exit fullscreen mode

并将它添加到使我们的 Worker 构造的函数中:

**worker.go**

```
func MakeNewWorker(serverAddress string, protocol CommentProtocol, processor CommentProcessor,
                                tubeToListenOn string) *CommentWorker {
    worker := CommentWorker{protocol: protocol, processor: processor, tube: tubeToListenOn}
    worker.ServerAddress = serverAddress
    return &worker
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将实现手表命令并使用它:

**worker.go**

```
 func (worker *CommentWorker) watch() error {
    watching, err := worker.serverConnection.Watch(worker.tube)
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
    fmt.Println("watching ", watching, " tubes")
    return nil
}

func (worker *CommentWorker) Connect() {
    worker.PapaBeanstalk.Connect()
    worker.watch()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当工作人员连接到服务器时，我们都会自动监视已配置的试管。工人仍然在默认的管道上监听，这对我们来说是好的。如果您希望 worker 不在默认管道上侦听，您需要使用 ignore 命令。

新更新的工人现在看起来是这样的:

**worker.go**

```
package main

import (
    "fmt"
    "time"
    "github.com/iwanbk/gobeanstalk"
    "os"
)

type CommentWorker struct {
    PapaBeanstalk
    protocol CommentProtocol
    processor CommentProcessor
    tube string
}

func (worker *CommentWorker) ProcessJob() {
    fmt.Println("reserving job")
    job, err := worker.serverConnection.Reserve()
    if err != nil {
        fmt.Println(err)
        return
    }
    fmt.Println("got job Id: ", job.ID)
    comment, err := worker.protocol.Decode(job.Body)
    if err != nil {
        worker.handleError(job, err)
        return
    }
    processError := worker.processor.DoProcess(comment)
    if processError != nil {
        worker.handleError(job, err)
        return
    }
    fmt.Println("processed job id: ", job.ID)
    worker.serverConnection.Delete(job.ID)
}

func (worker *CommentWorker) watch() error {
    watching, err := worker.serverConnection.Watch(worker.tube)
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
    fmt.Println("watching ", watching, " tubes")
    return nil
}

func (worker *CommentWorker) Connect() {
    worker.PapaBeanstalk.Connect()
    worker.watch()
}

func (worker *CommentWorker) handleError(job *gobeanstalk.Job, err error) {
    fmt.Println(err)
    priority := uint32(5)
    delay := 0 * time.Second
    worker.serverConnection.Release(job.ID ,priority, delay) // hey I can't handle this
}

func MakeNewWorker(serverAddress string, protocol CommentProtocol, processor CommentProcessor,
                                tubeToListenOn string) *CommentWorker {
    worker := CommentWorker{protocol: protocol, processor: processor, tube: tubeToListenOn}
    worker.ServerAddress = serverAddress
    return &worker
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将功能添加到生产者:

**生产者. go**

```
func (producer *Producer) UseTube(tubeName string) error {
    return producer.serverConnection.Use(tubeName)
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一剩下的就是修改主函数:

**main.go**

```
package main

import (
    "time"
    "os"
    "fmt"
)

func ProducerMain(tubes []string) {
    var comments = []Comment{
        {UserName: "some_user", Text:"i love your cat", Date: time.Now()},
        {UserName: "some_other_user", Text:"i prefer dogs", Date: time.Now()},
        {UserName: "another_user", Text:"please close this thread", Date: time.Now()},
        {UserName: "admin", Text:"thread closed - not relevant", Date: time.Now()},
    }
    protocol := MakeJsonCommentProtocol()
    producer := MakeNewProducer("localhost:11300", protocol)
    producer.Connect()
    defer producer.Close()

    for _, tube := range tubes {
        producer.UseTube(tube)
        for _, comment := range comments {
            producer.PutComment(&comment)
        }
    }

}

func WorkerMain(commentsDir string, tube string) {
    protocol := MakeJsonCommentProtocol()
    os.Mkdir(commentsDir, 0777)
    processor := MakeNewCommentProcessor(commentsDir)
    worker := MakeNewWorker("localhost:11300", protocol, processor, tube)
    worker.Connect()
    defer worker.Close()
    for {
        worker.ProcessJob()
    }

}

func printUsage() {
    fmt.Println("Usage: example-app worker/producer")
    os.Exit(1)
}

func main() {
    if len(os.Args) < 2 {
        printUsage()
    }
    if os.Args[1] == "worker1" {
        WorkerMain("first_comments", "first")
    } else if os.Args[1] == "worker2" {
        WorkerMain("second_comments", "second")
    } else if os.Args[1] == "producer" {
        ProducerMain([]string{"first", "second"})
    } else {
        printUsage()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里添加了一些东西。

*   生产商主要接受要使用的试管列表
*   ProducerMain 遍历管道，并将虚拟作业放入每个管道中
*   WorkerMain 接受目录路径和管道名，并使用它们创建 worker

重建应用程序

```
$ go build 
```

Enter fullscreen mode Exit fullscreen mode

打开一个终端窗口，用“producer”参数运行它:

```
$ ./example-app producer 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到插入了 8 个作业。

在另一个终端窗口中，用“worker1”参数运行它:

```
$ ./example-app worker1 
```

Enter fullscreen mode Exit fullscreen mode

在另一个终端窗口中使用“worker2”参数运行它:

```
$ ./example-app worker2 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到作业已被处理。如果成功的话，相同数量的注释文件应该出现在“first_comments”和“second_comments”文件夹中。

## 本教程没有涵盖的内容

Beanstalkd 不仅仅是基本的 put-reserve-delete 流。

以下是您应该研究的更高级用法的命令和主题:

*   超时-“最后期限很快”，“超时”
*   触摸命令
*   埋葬命令
*   忽略命令
*   踢腿命令
*   列出命令
*   统计命令
*   查看命令
*   暂停管

## 总结

到目前为止，您应该对在 Go 中使用 Beanstalkd 有了基本的了解。

示例应用在 [github](https://github.com/asafg6/go-beanstalkd-example) 上，可以随意使用。

如果你喜欢这个教程，请评论、分享或告诉你的朋友。

感谢阅读。**