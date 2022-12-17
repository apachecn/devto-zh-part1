# 通过阅读测试来避免代码爬行

> 原文：<https://dev.to/0x4445565a/avoiding-the-code-crawl-with-tests>

## TL；速度三角形定位法(dead reckoning)

1.  测试驱动的开发非常棒
2.  阅读测试可以补充/替代文档
3.  去看看试卷。

## 一个和时间一样古老的故事

作为一名开发人员，你可能依赖于库、包、模块等。当您这样做时，文档对于理解和有效地使用代码是至关重要的。但是，经常会出现文档不完整或不存在的情况。

您的自然本能是叹息，并开始挖掘代码，希望找到任何注释或明显的代码片段来帮助您实现目标。

根据库的维护情况，这可能是一场快速的斗争，你会发现光荣的`use_me_and_only_me_init()`，但更有可能的是，你已经找到了一个函数和数据块的集合，它们被拼凑在一起，松散地做你想要它们做的事情。

必须有一个更好的方法来做到这一点，根据测试的存在，有！

## 什么是测试？

对于那些刚接触测试或测试驱动开发的人来说，测试是一段段代码对一段段代码的测试。这是通过分解功能、向它扔数据、然后检查输出来完成的。

如果我有一个二加二的测试，它应该总是断言四，如果没有，那么你不能推送你的代码，直到它被修复或者在发布队列中被处理。这可以防止新代码破坏旧的特性/逻辑。

关于 TDD 的伟大之处在于，如果做得好，所有的东西都会被测试，这意味着开发人员会意外地创建他们的代码的例子，而这些代码正是应该被使用的。

在文档中，开发人员倾向于记录并给出他们认为重要的东西的例子，结果很多特性可能没有记录下来(这可以通过使用像 [Doxygen](https://en.wikipedia.org/wiki/Doxygen) 这样的东西来减轻，但那是另一篇文章)。有了测试用例，一切都必须被覆盖。

## 现实生活中的例子呢？

我几乎在我开发的每一种语言/平台上都这样做过，但在这种情况下，我只是浏览了一下 [Awesome-Go](https://github.com/avelino/awesome-go) repo，直到我发现一个项目有薄弱的文档。

我碰到一个 [FTP 客户端](https://github.com/jlaffaye/ftp)要作为一个包使用。你注意到的第一件事是文档仅限于简单的安装说明和 [GoDocs](https://godoc.org/github.com/jlaffaye/ftp) 。

在 [ftp.go](https://github.com/jlaffaye/ftp/blob/master/ftp.go) 中浏览 ftp 包，你可以很快看到除了偶尔的注释之外，没有明显的操作列表。这是开发人员在挖掘库代码时经常看到的情况。

```
// Connect is an alias to Dial, for backward compatibility
func Connect(addr string) (*ServerConn, error) {
  //...
}

// Dial is like DialTimeout with no timeout
func Dial(addr string) (*ServerConn, error) {
  //...
}

// DialTimeout initializes the connection to the specified ftp server address.
//
// It is generally followed by a call to Login() as most FTP commands require
// an authenticated user.
func DialTimeout(addr string, timeout time.Duration) (*ServerConn, error) {
  //...
} 
```

在这一点上，我们可以通过并开始在墙上油漆，看看我们是否可以得到一些工作，或者我们可以看看测试。

浏览`*_test.go`文件，你可以很快看到 [client_test.go](https://github.com/jlaffaye/ftp/blob/master/client_test.go) 是我们想要查看的文件。现在，我知道外面的人可能不会写 Go，但这种想法是普遍的，所以对我来说是赤裸裸的。

浏览这个文件，你可以看到一个又一个函数被命名为`TestConnPASV()`、`TestConnEPSV()`和`testConn()`。我们几乎立刻就能看出哪个最有可能引起我们的兴趣，`testConn()`。

```
// Author's Note: Modified to be simpler.
func testConn(t *testing.T, passive bool) {

    c, err := DialTimeout("localhost:21", 5*time.Second)
    if err != nil {
        t.Fatal(err)
    }

    err = c.Login("anonymous", "anonymous")
    if err != nil {
        t.Fatal(err)
    }

    err = c.ChangeDir("incoming")
    if err != nil {
        t.Error(err)
    }
        //...
} 
```

如何启动 FTP 连接、运行命令以及使用 ChangeDir()和 List()之类的帮助方法变得非常清楚。如果你特别注意，你还可以看到如何实现错误登录的错误检查以及 IPv6。

```
// Author's Note: Modified to be simpler.
func TestWrongLogin(t *testing.T) {
    c, err := DialTimeout("localhost:21", 5*time.Second)
    if err != nil {
        t.Fatal(err)
    }
    defer c.Quit()

    err = c.Login("zoo2Shia", "fei5Yix9")
    if err == nil {
        t.Fatal("expected error, got nil")
    }
} 
```

在 45 秒之后，我们现在已经准备好获取那些代码段，并对它们进行一些修改，以适应我们的用例。

由于薄弱的文档，这可能会占用大量宝贵的开发时间，带来许多挫折。但是由于编写良好的测试，我们学会了如何快速准确地实现这个包。

## 外卖

关于使用测试作为文档，有一些事情不得不说。这并不总是行得通的，有时开发人员会写出糟糕或不完整的测试。但是这不应该阻止你尝试这种类型的开发。

下次当你陷入未记录的代码时，看看测试吧！