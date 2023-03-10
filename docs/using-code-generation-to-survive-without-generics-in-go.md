# 在 Go 中使用代码生成在没有泛型的情况下生存

> 原文：<https://dev.to/joncalhoun/using-code-generation-to-survive-without-generics-in-go>

如果你使用 Go(又名 golang)有一段时间了，你可能会遇到这样的情况:这种语言既是静态类型的，又不支持泛型，这被证明是有问题的。

例如，当我创建介绍[队列](https://www.calhoun.io/lets-learn-algorithms-queues-in-go/)和[堆栈](https://www.calhoun.io/lets-learn-algorithms-stacks-what-they-are-and-how-to-implement-them-in-go/)并演示如何实现它们的视频时，很快就清楚了，没有一种很好的方法可以创建任何类型都可以使用的单个队列实现。

当然，有像[容器/列表](https://golang.org/pkg/container/list)这样的东西，但是最终你不得不自己做很多[类型的断言](https://golang.org/ref/spec#Type_assertions)来让它工作。例如，您最终会得到如下代码:

```
l  :=  list.New()  l.PushFront(123)  var  x  interface{}  =  l.Back().Value  if  i,  ok  :=  x.(int);  ok  {  // We are okay to use i as an integer!  fmt.Println(i,  "is an integer!")  }  else  {  // Snap, x wasn't an integer? (â•¯Â°â–¡Â°ï¼‰â•¯ï¸µ â”»â”â”»  } 
```

在使用泛型的语言中，你可以用代码来预先指定列表的底层类型，然后所有添加到列表中的值都必须跟随该类型。它可能看起来像下面这样。

*注意:这是无效代码！这是为了展示泛型可能是什么样子。*

```
// THIS IS NOT VALID CODE, but if it were we would be stating  // that this specific List, demo, could only contain integer  // values, and our compiler could help us verify that we  // do not break that contract.  var  demo  list.List<int> 
```

不幸的是，Go 中没有泛型。

我不想在这里争论它们是否值得增加复杂性，但是我想讨论的是开发人员可以采取的避免像第一个例子中那样编写代码的方法。我想谈谈我们如何在一个没有泛型的地方写出合理的&可维护的代码。

我认为这是一个重要的话题，因为许多来自其他语言的人不知道如何实现这一点，如果没有一些关于“围棋方式”的指导，他们很可能会对围棋产生不好的印象，并错过 golang 提供的所有精彩内容。所以让我们直接开始吧。

> 如果你喜欢这篇文章，请考虑[加入我的邮件列表](https://www.calhoun.io/lets-learn-algorithms-stacks-what-they-are-and-how-to-implement-them-in-go/#subscribe)！这真的有助于激励我继续写这样的文章。
> 
> 如果你加入，我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章或者视频(比如这篇)。没有垃圾邮件。不要出售你的电子邮件。我会像对待自己的收件箱一样对待你的收件箱。
> 
> 作为对您加入的特别感谢，我还将向您发送我即将开设的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。

## 创建特定类型的包装器

我向新手推荐的第一件事就是为他们的用例创建一个特定类型的包装器。例如，如果我们需要创建一个整数队列，您可以很容易地用您自己的`Int`类型在`queue`包中包装`container/list`包。如下所示。

```
package  queue  import  (  "container/list"  "errors"  )  var  (  // Queues will panic with this error when empty and  // the Dequeue method is called.  ErrEmptyQueue  =  errors.New("queue: the queue is empty and the requested operation could not be performed")  // Queues will panic with this error when they encounter a  // value in the underlying list that isn't of the expected  // type. This SHOULD NOT ever happen, and if it does it  // indicates that the underlying `container/list` was  // exported and manipulated by outside code, or that there  // is a bug in this code. Both are bad and shouldn't be  // allowed to happen!  ErrInvalidType  =  errors.New("queue: invalid type encountered - this indicates a bug.")  )  func  NewInt()  *Int  {  return  &Int{list.New()}  }  // Int is an integer queue implementation.  // Behind the scenes it is a linked list FIFO queue  // that uses `container/list` under the hood. The primary  // motivation in creating this type is to allow the compiler  // to verify that we are using the correct types with our  // queue rather than dealing with the interface{} type in  // the rest of our code.  type  Int  struct  {  list  *list.List  }  // Len returns the total length of the queue  func  (q  *Int)  Len()  int  {  return  q.list.Len()  }  // Enqueue adds an item to the back of the queue  func  (q  *Int)  Enqueue(i  int)  {  q.list.PushBack(i)  }  // Dequeue removes and returns the front item in the queue  func  (q  *Int)  Dequeue()  int  {  if  q.list.Len()  ==  0  {  // You could opt to return errors here, but I personally  // prefer to leave length checking up to end users kinda  // like bounds checking in slices.  panic(ErrEmptyQueue)  }  raw  :=  q.list.Remove(q.list.Front())  if  typed,  ok  :=  raw.(int);  ok  {  return  typed  }  // This won't ever happen unless someone has access to  // insert things into the list with an invalid type or  // your code has bug.  panic(ErrInvalidType)  } 
```

虽然乍一看这似乎很多，但是如果你去掉注释，这不到 40 行代码。对于它增加的价值来说，还不算太坏。

### 使用类型不可知的实现

在继续之前，我想提一下，在队列示例中，我们使用了一个包含[容器/列表的结构。引擎盖下的清单](https://golang.org/pkg/container/list/#List)。这是一个双向链表的实现，它支持创建队列和堆栈所需的所有方法，但是它使用`interface{}`类型，这意味着它没有为我们内置的类型安全。它将接受我们提供的任何值，并将其放入队列中。

这显然不是我们想要的，但是通过在我们的`Int`类型上使用一些类型化的方法，我们可以使用`container/list.List`类型来为我们处理大部分繁重的工作，我们剩下要做的就是公开适当的方法并做一些类型检查。

虽然拥有一个在接口上工作的底层实现有时非常有用，但这不是必需的。例如，我可以在这里编写自己的队列实现，而不是使用`container/list`。这可能需要更多的代码，但这并非不可能。

一般来说，如果有类似于`container/list`的东西可以满足我的需求，我会用它来支持我的特定类型实现。这在使用 [sort](https://golang.org/pkg/sort/) 包时尤其常见，我发现自己一直在编写这样的代码:

```
package  sort  import  (  "sort"  "calhoun.io/animals"  )  func  Dogs(dogs  []animals.Dog)  {  sort.Slice(dogs,  func(i,  j  int)  bool  {  return  dogs[i].Age  <  dogs[j].Age  })  } 
```

我没有编写排序实现，而是依赖于`sort`包来处理繁重的工作，并且我简单地提供了一个`Dogs()`函数来简化剩余的代码。

### 如果还没有可以使用的实现呢？

想象一下,`container/list`包不存在，您想要创建一个整数堆栈实现。你是如何进行的？

此时，您有两个选择:

1.  编写特定于整数的实现，或者
2.  编写一个通用实现(如`container/list`)，然后用一个整数特定的实现包装它，就像我们在上面的队列示例中所做的那样。

哪条路是正确的？良好的...*看情况*。

**如果你只需要一个单独的实现**(比如你只需要一个整数栈)，那么**选项(1)最有意义**。

简而言之，第二种选择更难编写、阅读和维护，并且如果只使用一次，将会产生更多的代码，因此与其浪费时间而没有实际好处，不如不要这样做。坚持选项(1)，直到真正需要转向选项(2)。

然后* *当你需要多个实现时(比如一个整数栈，一个字符串栈，一个狗栈)，重构创建一个类型不可知的版本来支持这三个。重构到类型不可知的版本几乎总是比第一个版本更容易。作为一个额外的收获，你可能已经学到了很多关于代码是如何被使用的，并且解决了一些问题。我称之为胜利。

现在你可能会问自己，“*这难道不意味着我必须写很多代码吗？*

是的，是的，它是。嗯，如果全部手动操作，它会的。但是有办法让这变得更容易，比如创造一个发电机。

### 创建发电机

一旦你有了一个类型不可知的实现，有时考虑创建一个利用它的生成器是一个好主意。这将允许您在保持头脑清醒的同时，立即推出特定类型的实现。

您不必维护相同代码的 3 个以上版本，而是将自己限制在一个主版本(我们从中生成的模板)，当您对该文件进行任何更改时，您可以简单地重新生成所有代码。

现在，这确实有一个明显的缺陷——如果您希望能够在主版本更改时重新生成生成的文件，您就不能修改它们。因此，这并不总是一个合适的方法，但是我经常发现它足够有效。

让我们看一个例子来说明这是如何工作的。我们将使用本文前面的队列示例来实现这一点，但是我们将研究如何为任何类型创建生成器，而不是特定于整数。

> 我们将假设以下所有步骤从回购和分支[github.com/joncalhoun/queue/tree/manual](https://github.com/joncalhoun/queue/tree/manual)开始，并以此为基础。我会在每一步之后发布不同分支的链接。

#### 第一步——拔出共享棋子

我喜欢采取的第一步是拉出共享的片段。也就是说，不需要为每个包装器单独生成的任何代码都需要提取出来，这样就不会重复。

我假设我们从代码开始

在队列示例中，这只是我们将在所有队列实现中共享的错误消息。代码如下所示(为了简洁没有注释)。

```
package  queue  import  (  "errors"  )  var  (  ErrEmptyQueue  =  errors.New("queue: the queue is empty and the requested operation could not be performed")  ErrInvalidType  =  errors.New("queue: invalid type encountered - this indicates a bug.")  ) 
```

一旦我知道我计划使用什么代码作为共享代码库，我就把它移到自己的文件中。让我们称之为`shared.go`。

如果您已经编写了两个或更多的实现，那么您可能已经完成了这一步，所以这通常不需要太多的工作。

这一步在 Github 上显示在这里——[https://github.com/joncalhoun/queue/tree/step1](https://github.com/joncalhoun/queue/tree/step1)

#### 步骤 2——创建一个`gen`目录并创建一个模板

这一步肯定是可定制的，但是在这一点上，我发现创建一个包含我的所有生成代码的子目录很有用。也就是说，它将包含我们所有的模板和用于处理这些模板和创建我们的 go 代码的代码。

我给我的取名`gen`，但是你想要什么都可以。有些人喜欢用类似于`cmd/gen`的东西来表明这是一个命令。

```
mkdir gen 
```

然后我们开始写一个生成器。

我通常创建的第一个版本将执行的代码模板写出到 stdout，并使用一个快速内联模板文件。模板文件是我们用来创建`queue.Int`的非共享代码，但是我用`{{.Name}}`替换了所有出现的`Int`，用`{{.Type}}`替换了相关的`int`类型。

*注意:并非所有的`int`事件都应该被替换。Eg `Len()`仍然返回一个 int！*

这一步的代码如下所示，没有注释。或者，你可以在 Github 这里得到更大的版本-[https://github.com/joncalhoun/queue/tree/step2](https://github.com/joncalhoun/queue/tree/step2)

```
package  main  import  (  "flag"  "os"  "text/template"  )  type  data  struct  {  Type  string  Name  string  }  func  main()  {  var  d  data  flag.StringVar(&d.Type,  "type",  "",  "The subtype used for the queue being generated")  flag.StringVar(&d.Name,  "name",  "",  "The name used for the queue being generated. This should start with a capital letter so that it is exported.")  flag.Parse()  t  :=  template.Must(template.New("queue").Parse(queueTemplate))  t.Execute(os.Stdout,  d)  }  var  queueTemplate  =  `
package queue

import (
  "container/list"
)

func New{{.Name}}() *{{.Name}} {
  return &{{.Name}}{list.New()}
}

type {{.Name}} struct {
  list *list.List
}

func (q *{{.Name}}) Len() int {
  return q.list.Len()
}

func (q *{{.Name}}) Enqueue(i {{.Type}}) {
  q.list.PushBack(i)
}

func (q *{{.Name}}) Dequeue() {{.Type}} {
  if q.list.Len() == 0 {
    panic(ErrEmptyQueue)
  }
  raw := q.list.Remove(q.list.Front())
  if typed, ok := raw.({{.Type}}); ok {
    return typed
  }
  panic(ErrInvalidType)
}
` 
```

有了这个，我可以通过从`queue`目录运行下面的*来轻松地创建新的队列实现！*

```
go run gen/main.go -name=String -type=string > string.go 
```

如果你仔细观察，会发现有一些缺陷(比如我们生成的模板顶部的换行符),但是没有任何明显的缺陷使它不可用。这意味着我们已经准备好进入最后一步——清理那些小瑕疵！

#### 步骤 3 - gofmt 和 goimports

如何清理代码取决于你，但我个人喜欢运行`gofmt`和`goimports`来确保事情得到清理。这让我不必担心模板文件的格式是否完美(比如顶部的换行符)，运行`goimports`让我不必提供导入标志。相反，我依靠`goimports`来为我处理。

如何做到这一点取决于您，但是您需要将一些命令链接起来，以便每个后续的命令都可以从上一个命令中读取输入，并以下一个命令可以使用的方式写入其输出。使用标准库最简单的方法是使用 [io。Pipe()](https://golang.org/pkg/io/#Pipe) 和做类似于在此建议的[栈溢出文章](http://stackoverflow.com/questions/10781516/how-to-pipe-several-commands-in-go)。

或者你可以使用我刚刚发布的一个包，这样其他人就不必重写所有这些代码-[https://github.com/joncalhoun/pipe](https://github.com/joncalhoun/pipe)-这就是我在这里要使用的。我还会忽略几乎所有潜在的错误，因为我只在坐在电脑前有意生成代码时运行它，我可以直观地检查错误。

在我们的代码中找到第 2 步中的代码行`t.Execute(os.Stdout, d)`,并用以下代码替换它:

```
rc,  wc,  _  :=  pipe.Commands(  exec.Command("gofmt"),  exec.Command("goimports"),  )  t.Execute(wc,  d)  wc.Close()  io.Copy(os.Stdout,  rc) 
```

`pipe.Commands()`接受一组命令并返回一个`io.ReadCloser`、一个`io.WriteCloser`和一个`chan error`(这里我忽略了)。我将执行模板的输出写到`wc`(write closer)，然后关闭它，这样下一个命令就知道它正在读取的输入已经完成。最后，我将阅读器`rc`的输出复制到`os.Stdout`，以便在我的屏幕上打印出来。

有了这个设置，向我们的管道添加新命令就变得很简单了，所以在你认为合适的时候添加吧。

这部分的最终代码在 Github 上这里-[https://github.com/joncalhoun/queue/tree/step3](https://github.com/joncalhoun/queue/tree/step3)

#### 把所有的东西放在一起

将这三个步骤放在一起，我们现在可以生成代码并创建新的队列实现来使用。下面是我运行的几个命令，给你几个在最终的 Github repo 中的示例输出文件，可以在这里找到-[https://github.com/joncalhoun/queue](https://github.com/joncalhoun/queue)

```
go run gen/main.go -name=String -type=string > string.go
go run gen/main.go -name=Int -type=int > int.go
go run gen/main.go -name=IntSlice -type="[]int" > int_slice.go
# Getting a little meta
go run gen/main.go -name=List -type="*list.List" > container_list.go 
```

*如果你想查看这些队列的使用情况，你可以进入 master-[https://github . com/joncalhoun/queue/blob/master/demo/demo . go](https://github.com/joncalhoun/queue/blob/master/demo/demo.go)T3】中的 demo 目录*

最棒的是，我们现在可以使用所有的队列实现，并且可以放心，我们不可能传入错误的数据类型。编译器现在支持我们了！

我们的代码中唯一没有被编译器进行类型检查的区域是生成的代码，因为我们只允许通过类型安全的方法访问底层类型，所以我们甚至不必太担心这一点。

补充说明:如果你愿意，你也可以为每个文件生成测试，但是我在这里没有这么做。

好了，代码生成到此为止。让我们简单谈谈另一个选项——接口。

## 使用接口而不是类型

另一种方法是使用接口而不是特定的类型。我不打算在这里做太多的详细介绍，因为它已经在其他地方讨论过了，而且这篇文章已经很长了，但是如果你想要一个这样的例子，我强烈建议你查看一下 [sort](https://golang.org/pkg/sort) 包。

与其要求您编写自己的排序功能，您唯一需要做的事情就是实现[排序。接口](https://golang.org/pkg/sort/#Interface)。之后，您可以使用排序包中几乎所有的函数，而不管您的底层类型是什么。

就像我对特定类型包装器所做的那样，这有点难以分解，但一般来说，确定接口是否正确的最好方法是问自己“我真的关心底层类型是什么吗，或者有一些方法我可以用来实现相同的目标吗？”

如果这个问题的答案是“是”，那么您可以编写类似于`sort`包的代码，它只关心一个接口是否被实现。

这并不总是显而易见的，但是通过一点实践，你会发现自己更频繁地选择和利用接口，**但是这里的关键是实践**。不练习，你不可能变得更好或学会一项技能，所以一定要时不时地尝试一下。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/using-code-generation-to-survive-without-generics-in-go/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章或者视频(比如这篇)。没有垃圾邮件。不要出售你的电子邮件。我会像对待自己的收件箱一样对待你的收件箱。

作为对您加入的特别感谢，我还将向您发送我的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。