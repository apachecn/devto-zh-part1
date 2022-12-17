# PawLIB 有 3 件很酷的事

> 原文：<https://dev.to/codemouse92/3-cool-things-to-do-with-pawlib>

*(最初发布于[indeliblebluepen.com](https://www.indeliblebluepen.com/3-cool-things-to-do-with-pawlib/))。)*

我可能应该在这篇文章上加上一个巨大的免责声明:我帮助编写了来自 MousePaw Media 的新 C++编程库 [PawLIB](https://mousepawmedia.com/projects/libraries/pawlib) 。它包含了我曾经写过的一些我最喜欢的代码。除了想要构建高性能的数据结构，我们真的想要*让 C++再次变得有趣*。

与其试图描述是什么让图书馆变得很酷(我已经在官方新闻稿中尝试过了)，我宁愿演示一些我最喜欢的小技巧。

## 1:三连条件句

我们大多数人都非常擅长将编程逻辑归结为是非语句，但*并不总是*。似乎总有那么一个讨厌的例外，逻辑是三种方式而不是两种方式——T2。

围绕这一点有许多技巧:异常、枚举、将“null”视为一个值(请不要做最后一个！)，但是这些解决方法通常让你觉得很笨拙。是的，你可以把逻辑重写为一个两步的过程，但是我们大多数人喜欢简短的代码。枚举通常很好，但现在我们要为三路停止添加额外的代码。

PawLIB 1.0 引入了一种新的原子数据类型，称为“ **trilean** ,它包含三种不同的状态:true、false 和 maybe。

让我们实现一个简单的例子。想象一个有转折的数字猜谜游戏:三个数字是“毒药”，如果你猜中其中任何一个，游戏就结束了。否则，你有无限的尝试。

显然，我可以用一个`break`语句代替 trilean 轻松实现这一点，但这是一个很好的例子。

我们从设置变量开始。

```
tril status = maybe; // the outcome and loop control variable
int attempts = 0; // the number of guesses
int number = 42; // the number to guess
int poisoned[3] = {11, 48, 57};
int guess = 0; // the player's guess</pre> 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁起见，我对答案和中毒数字的值进行了硬编码，但是在真实的游戏中，您可能希望随机生成这些值。

`status`变量是 trilean。默认情况下，它被设置为`maybe`，这意味着在我们的情况下，结果仍然是不确定的。`true`将为一胜，`false`为一死。

我们可以在 while 循环中使用 trilean 的“可能”状态，所以我们不需要`break`。

```
// while the status is 'maybe', the player can still guess
while(~status)
{
  std::cin >> guess; // get the guess
  ++attempts; // increment the number of guesses

  // If the player guessed right
  if(guess == number)
    status = true; // report success, thereby exiting loop

  // Else if the player guessed a poison number...
  else if(guess == poisoned[0] || guess == poisoned[1] || guess == poisoned[2])
    status = false; // report death, thereby exiting loop

  // Else if the player below target...
  else if(guess < number)
    std::cout << "Your guess is too low." << std::endl;

  // Else if the player above target...
  else if(guess > number)
    std::cout << "Your guess is too high." << std::endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

剩下的很简单:我们只需检查“status”变量的真或假状态。

```
if(status)
  std::cout << "You won in " << attempts << " attempts." << std::endl;
else if(!status)
{
  std::cout << "You guessed a poison number and died." << std::endl;
  std::cout << "The answer was " << number << std::endl;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是完整的代码。

Trilean 与 boolean 及其常量完全兼容，因此我们可以通过`==`操作符轻松地进行比较。

## 2:内存转储

使用 C++时，迟早会遇到指针和内存问题。当然，你可以打印出指针和任何原子数据类型，但如果不做大量额外的工作，就无法打印出原始内存。如果你想查看原始内存，你通常需要一个调试器或者类似的工具。

IOChannel 是 iostream 的一个功能丰富的包装器。它的众多特性之一是允许你智能地输出任何东西...包括原始内存转储。

假设我们有一个类，比如下面这个。

```
class SomeMagicThing
{
  public:
    SomeMagicThing(int n1, int n2, bool b1)
    :foo(n1), bar(n2), baz(b1)
    {}

    ~SomeMagicThing(){}
  private:
    int foo;
    int bar;
    bool baz;
}; 
```

Enter fullscreen mode Exit fullscreen mode

假设我们的代码中有该对象的一个实例。

```
SomeMagicThing* thingy = new SomeMagicThing(123, 456, true); 
```

Enter fullscreen mode Exit fullscreen mode

现在，假设我们遇到了某种麻烦，需要直接查看内存以进行调试。只给定我们的指针，我们可以使用 IOChannel 打印出内存。

```
ioc << ptr_memory << read_size(sizeof(SomeMagicThing)) << thingy << io_end; 
```

Enter fullscreen mode Exit fullscreen mode

像变魔术一样，把‘东西’的完整记忆打印出来。让我们来分析一下...

`ptr_memory`告诉 IOChannel 打印出指针处的内存。为了防止内存访问错误，我们默认只读取一个字节，所以需要指定一个`read_size()`。我们可以对我们的类型使用`sizeof()`操作符来实现这一点。

当我们运行这个时，我们看到...

> 7b000000c801000001000000

然而，这仍然不像我们希望的那样易读，所以让我们使用 IOChannel 的格式化工具来使它更漂亮。

就我个人而言，我喜欢十六进制中的大写字母，所以我将打开它(`num_upper`)。我还要按字和字节拆分(`mem_allsep`)。为了好玩，我们也将这个文本输出为粗体(`ta_bold`)和蓝色(`fg_blue`)。

```
ioc << fg_blue << ta_bold << num_upper << mem_allsep
 << ptr_memory << read_size(sizeof(SomeMagicThing)) << thingy << io_end; 
```

Enter fullscreen mode Exit fullscreen mode

运行它，我们看到:

> **7b 00 00 00 c8 01 00 00 | 01 00 00 00**

(显然，终端中会有颜色。)

为了方便起见，我可能还想在同一行打印出地址。在中途重置 IOChannel 的一些标志有点棘手，所以我们将在我们编写的流语句之上创建另一个流语句。

```
ioc << "The memory at " << fg_green << ta_bold << ptr_address << thingy << io_send;
ioc << " is: " << io_send; 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行这个时，我们得到...

> 在 **0x1236D80** 的内存为:**7b 00 00 00 c8 01 00 00 | 01 00 00 00**

(还是那句话，终端版有颜色。)

这看起来似乎是一件毫无意义的事情，但是请考虑一下:通过一些恰当放置的语句，我们现在可以在不调试符号的情况下查看编译过的*代码中的特定内存！在某些情况下，它也比典型的调试器更容易使用。我已经在多次调试中成功地使用了这种模式。*

这是完整的代码。

## 3:即时基准测试

这听起来熟悉吗？你坐在电脑前，盯着代码，思考哪种方法更有效。研究一无所获，试图对这两种方法进行 big-O 分析令人望而生畏。

除了作为一个成熟的测试框架，PawLIB Goldilocks 还有一个完整的服务比较基准。使用它就像编写两个测试一样简单。

假设我想比较冒泡排序和选择排序。显然我们已经知道哪个更快，但这是一个很好的例子。在我的示例代码中，我为每一个都编写了函数(在这里我就不再赘述了——完整的代码见本节末尾的要点)。

Goldilocks 测试几乎不需要样板文件，其结构也有很好的文档记录。我花了整整两分钟为每一个问题打一份试卷。

```
class TestBubbleSort : public Test
{
  public:
    TestBubbleSort(){}

    testdoc_t get_title()
    {
      return "Bubble Sort";
    }

    testdoc_t get_docs()
    {
      return "Runs a bubble sort.";
    }

    bool run()
    {
      int arr[10] = {42, 57, 96, 21, 66, 17, 10, 97, 43, 86};
      bubblesort(arr, 10);
    }

    ~TestBubbleSort(){}
};

class TestSelectionSort : public Test
{
  public:
    TestSelectionSort(){}

    testdoc_t get_title()
    {
      return "Selection Sort";
    }

    testdoc_t get_docs()
    {
      return "Runs a selection sort.";
    }

    bool run()
    {
      int arr[10] = {42, 57, 96, 21, 66, 17, 10, 97, 43, 86};
      selectionsort(arr, 10);
    }

    ~TestSelectionSort(){}
}; 
```

Enter fullscreen mode Exit fullscreen mode

是的，这就是测试的全部内容。Goldilocks 测试有一些额外的安装和拆卸功能，但是我真的不需要它们。

提醒一句——由于各自的`run()`函数是基准函数，我们需要确保它们之间唯一的区别是我们测量的。

Goldilocks 需要在代码中启动，我们的测试用它注册，然后我们可以调用我们的比较函数。当我们进行比较时，我们希望指定每次测试重复 1000 次，基准测试人员总是运行三次测试。

```
int main()
{
  TestManager* testmanager = new TestManager();

  testmanager->register_test("BubbleSort", new TestBubbleSort);
  testmanager->register_test("SelectionSort", new TestSelectionSort);

  testmanager->run_compare("BubbleSort", "SelectionSort", 1000);

  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！当我们运行代码时，比较基准运行并给出完整的统计数据。这最终使得 Golidlocks 基准如此有用:统计数据帮助您剔除“受污染”的结果，例如在测量期间 CPU 可能在哪里做了其他事情。

(警告:在运行基准测试时，您应该*总是*编译为“release”。调试符号可能会影响结果。)

这里有很多信息。三种传递类型——妈妈、爸爸和熊宝宝——与基准测试中的缓存预热和缓存未命中的影响有关。“原始”数字是从完整的测量集合中得出的，而“调整”数字是在从集合中去除异常值后计算出来的。

简而言之，当测量总是以相同方式运行的代码时，我们希望寻找较低的 RSD(相对标准偏差；RSD 越低，测量越精确。如果有任何 RSD 以红色突出显示，我们应该扔掉结果。但是，如果 RSD 较低，或者至少两次测试结果相似，那么我们就要看结果了。

(关于基准统计的完整指南可以在[这里](https://docs.mousepawmedia.com/pawlib/general/console.html#understanding-benchmark-results)找到。

在运行这个案例时，我们被告知选择排序快了大约 300-400 个 CPU 周期(显然是每次执行)，这正是我们所期望的。

正如所承诺的，[这里是完整的代码](https://gist.github.com/CodeMouse92/839657990cc73818c4b3a1a4f9c1eba0)。

## 总结

这只是冰山一角。PawLIB 不仅是我曾经参与的最喜欢的项目之一，而且现在是我的编程工具库中的常规工具。

可以从 [GitHub](https://mousepawmedia.com/projects/libraries/pawlib) 下载 PawLIB。关于建造和使用它的说明可以在[官方文件](https://docs.mousepawmedia.com/pawlib/)中找到。

在该库的后续版本中会有更多很酷的特性。如果你发现任何错误，有疑问，或者想看到新的特性，欢迎你加入 MousePaw Media [开发社区](https://mousepawmedia.com/developers)。