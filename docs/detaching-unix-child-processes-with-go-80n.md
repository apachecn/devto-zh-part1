# 用 Go 分离 Unix 子进程

> 原文：<https://dev.to/foresthoffman/detaching-unix-child-processes-with-go-80n>

编辑(2019 . 5 . 13):`midproc`和`midprocrunner`分别更名为`reap`和`reaper`。

我遇到了一个项目的情况，我需要构建两个独立的程序来协同工作。第一个是服务器，第二个是正在进行的面向客户端的进程。该服务器被设计为启动任意数量的正在进行的过程的实例。在编写任何代码让服务器作为子进程运行正在进行的进程之前，我希望子进程独立于服务器。也就是说，如果服务器关闭，子进程仍将运行。它们将请求存储在队列中，并定期尝试重新连接到服务器。一旦连接重新建立，一切都会恢复正常。结果是 Unix 进程并不像我预期的那样工作。

在现实中，当一个进程产生另一个进程时，它被称为“子进程”；原来的被称为“家长”。只要父进程没有退出，子进程就会继续运行。如果出于某种原因，父母倒下了，孩子也倒下了。也就是说，除非孩子被认为“超脱”。当一个子进程不再有父进程时，它就被分离了(我相信从技术上讲，没有父进程的子进程是由一个`init`进程拥有的)。因此，您可以让一个父进程产生一个分离的子进程并立即退出，这将使子进程处于孤立状态。

然而，分离的子进程有一个警告。如果分离的子进程在其原始父进程退出之前被给予退出信号(例如 [SIGKILL](http://man7.org/linux/man-pages/man2/kill.2.html) ),则它将变成“僵尸”。僵尸进程不消耗资源，但是它们仍然在进程 ID 表中被引用。为了处理僵尸，原始父对象必须退出。

现在，如果您试图构建一个运行时间非常重要的 SaaS 产品，关闭服务器来定期清理僵尸进程并不是一个可行的解决方案。

正是在这一点上，当我研究情况时，我决定我的目标是无法实现的。我向一位同事表达了我的沮丧，他给出了一些令人鼓舞的反馈。仍然被难住了，我花了几天时间去做其他需要我立即关注的事情。当我回到这个问题时，我“哦”了一声瞬间。

我意识到我误解了过程是如何相互作用的。在我前面提到的例子中，当分离的子流程的顶级父流程退出时，子流程被分配给`init`流程。当您有嵌套的子进程时，也会发生同样的情况。比如:

```
- Grandparent Process
  - Parent Process
    - Child Process 
```

Enter fullscreen mode Exit fullscreen mode

这就是我感到困惑的地方。我假设当父进程退出时，分离的子进程将被分配给祖父进程。我错了。不管一个分离的子进程是如何嵌套的，当它的父进程退出时，它被分配给`init`进程。这使它成为一个顶级流程。然后，祖父进程和子进程将并行，独立运行。

```
- Grandparent Process
- Child Process 
```

Enter fullscreen mode Exit fullscreen mode

当我最终明白这一点时，我开始着手创建解决方案。之后我发布了 [`reap`](https://github.com/foresthoffman/reap) 和 [`reaper`](https://github.com/foresthoffman/reaper) 围棋套装。

`reap`包可以用来创建中间流程运行程序。或者，在前一示例的上下文中，临时嵌套父进程。

`reaper`包是实现`reap`包的中间流程运行程序。这是一个简单的实现，不会碍事，而且能完成工作。

这里有一个`reaper` :
用法的例子

```
package main

import (
    "bytes"
    "fmt"
    "os/exec"
    "strconv"
)

func main() {

    // prepare a buffer, to which the PID will be written
    var stdout bytes.Buffer

    // prepare the command
    sleepCmd := exec.Command("reaper", "-cmd='sleep'", "-args='30'")
    sleepCmd.Stdout = &stdout

    // run the command
    err := sleepCmd.Run()
    if nil != err {
        panic(err)
    }

    // convert the PID string to a valid integer
    pidInt, err := strconv.ParseInt(stdout.String(), 10, 64)
    if nil != err {
        panic(err)
    }
    pid := int(pidInt)
    fmt.Printf("Created a detached process with an ID of %d!\n", pid)
} 
```

Enter fullscreen mode Exit fullscreen mode

在安装了`reaper`的情况下，编译并运行上述程序，将导致`sleep`命令的分离进程。根据所提供的时间，默认情况下，`30`秒，该进程将在其父进程退出后继续存在。它也很好，可以在完成后自行清理。这是一个简单的例子，但是 runner 可以用来分离任何单独的进程。

对于我的需求来说，这非常好。如果你对使用这个感兴趣，并且想看看一次分离多个命令的能力(例如`sleep 30 && echo "something" | awk '{ print $0 }'` ) [让我知道](https://github.com/foresthoffman/reaper/issues)！

*就这些，尽情享受吧！*

## 学分

封面图片由[延斯·勒列](https://unsplash.com/@madebyjens?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/fork-in-the-road?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄！:D