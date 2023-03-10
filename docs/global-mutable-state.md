# 全局可变状态

> 原文：<https://dev.to/ericnormand/global-mutable-state>

软件中最大的问题之一是全局可变状态。这使得你的代码很难处理，而且一旦你走上这条路，它会变得越来越糟糕。减少程序中全局可变状态的数量是提高代码质量的最好方法之一，不管它是过程性的还是函数性的。

### 定义

全局可变状态有三个词，每个都很重要:

***全局*** 意味着它可以从代码中的任何其他点访问。这将你所有的代码联系在一起。你必须对整个程序进行推理，而不是对一小部分进行推理，因为任何其他部分都可以触及到它。

***可变*** 表示可以改变。您通常会看到，任何可以读取该值的人也可以更改它。代码中相邻的两次读取可能会返回不同的值。或者，更糟糕的是，它们返回的数据结构本身正在改变，甚至在读取之后也是如此。

***状态*** 更难定义。但它基本上意味着值取决于程序的历史。历史有多久远了？嗯，在最坏的情况下(即全局可变状态)，这意味着整个历史。**你必须知道程序是如何执行的**，包括线程是如何交错的。

当你把全局、可变和状态结合起来时，你会得到一个大混乱。当人们说“很难对进行推理[”时，**他们真正的意思是“它有 bug，你看代码看不出来”**。](http://www.lispcast.com/reasoning-about-code)

好的一面是，你可以系统地去除这三个方面。您可以或多或少地分别删除它们。我喜欢说**可以用任何语言** 编写函数式程序，甚至是最程序化的语言。一种方法是尽可能减少全局可变状态的数量。

### 标识全局可变状态

一些迹象:全局范围内的多个变量(在 Clojure 中:名称空间顶层的多个原子)，读写全局变量没有明确的模式(或者在一小段代码中多次读取全局变量)。变量可能在两次读取之间改变了值。

### 打扫卫生

一旦全局可变状态出现，实际上很难摆脱它。如果它不被束缚，它的用法将会传播。全局可变状态非常有用，它实际上可以用于许多不同的目的。过了一段时间，就很难看出使用模式是什么，以及如何去替换它们。但是我们可以依次解决每个淘气的方面。

#### 1)变量需要全局吗？

也许你可以修改代码，让一个对象被传递给
函数，而不是一个全局变量。这意味着您可以在每次运行代码时创建一个新的实例，这至少保证了每次都是从一个已知的值开始，并且在不同的执行中封装了变异。

换句话说，**把全局变量变成局部变量**。最好的方法是在进行变异的函数的本地(或者更小的范围，如果可能的话)。其次是本地对象上的实例变量。

使用全局变量是非常诱人的，因为它们是代码不同部分协同工作的一种简单方式。这里有一个例子:

```
var file;                            // the dreaded global variables
var recordCount;

function readFile() {
  file = openFile("input.txt");      // global mutation here
}

function countRecords() {
  recordCount = 0;
  for(var c in file.lines()) {       // global read
    recordCount++;                   // global mutation here
  }
}

function generateOutput() {
  for(var c in file.lines()) {       
    print(c + "," + recordCount);
  }
}

function processFile() {
  readFile();                        // these lines have to be in this order
  countRecords();
  generateOutput();
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们尝试使用上面的技术使变量不那么全局化。

```
// got rid of the globals
function readFile(state) {                // functions now take the state
  state.file = openFile("input.txt");
}

function countRecords(state) {            // see, the state is now an argument
  var x = 0;                              // use a local here, instead of storing
  for(var c in state.file.lines()) {      //   intermediate values in the global
    x++;
  }
  state.recordCount = x;                  // then assign the state once
}

function generateOutput(state) {          // state as argument, again
  for(var c in state.file.lines()) {
    print(c + "," + state.recordCount);   
  }
}

function processFile() {
  var state = {};                         // the state is now local (still mutable)
  readFile(state);                       
  countRecords(state);                   
  generateOutput(state);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们做的最大的转换是向每个方法传递一个`state`对象。它不再是全球性的。每次运行`processFile`时，我们都会生成一个新的实例。我们从一个已知的初始状态开始，我们知道对这个物体不会有任何竞争。

我们做的另一个转换是更多地依赖局部变量来累积中间值。这可能看起来微不足道，但这意味着我们的状态对象不包含不一致的数据。它要么不包含数据，要么是正确的。

#### 2)需要可变吗？

有没有从变量中读取但不写入变量的函数？它们可以被更改为将当前值作为一个
参数。减少依赖这些特定变量的代码量是一件好事。

换句话说，尽可能只使用函数的参数和返回值。将变量的变异隔离到代码的一小部分。

让我们将这种技术应用到我们刚刚修改的代码中。

```
function readFile() {
  return openFile("input.txt");     // instead of mutating state,
}                                   //    just return the value

function countRecords(file) {       // take just the state you need as arguments
  var x = 0;
  for(var c in file.lines()) {
    x++;
  }
  return x;                         // return the value you calculate
}

function generateOutput(file, recordCount) { // take the two values you need
  for(var c in file.lines()) {               //     as arguments
    print(c + "," + recordCount);
  }
}

function processFile() {
  var file = readFile();     // then use local variables
                             //    (initialized but never mutated)
  var recordCount = countRecords(file);
  generateOutput(file, recordCount);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经将写入可变参数的代码翻译成只返回计算值的代码。然后我们使用局部变量来保存返回值。注意`readFile`现在做的工作是如此之少(它只是一个函数调用),以至于我们可能想要移除它并直接调用`openFile`。这由您决定，但这是我在移除突变时注意到的一件事:函数变得很难读写，而且通常它们是如此的简单，以至于您想要内联它们。

```
function countRecords(file) {
  var x = 0;
  for(var c in file.lines()) {
    x++;
  }
  return x;
}

function generateOutput(file, recordCount) {
  for(var c in file.lines()) {
    print(c + "," + recordCount);
  }
}

function processFile() {
  var file = openFile("input.txt"); // we can just inline this one-liner
  var recordCount = countRecords(file);
  generateOutput(file, recordCount);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 3)需要状态吗？

是否可以修改算法，以便使用它们的自然输入和输出(参数和返回值),而不是写入某个位置？例如，也许你用变量来计数。该函数可以返回总计数，而不是添加到变量中。

程序需要状态。但是我们需要依靠国家来得到正确的答案吗？而我们的状态是否需要依赖于程序的整个历史？

让我们在代码中一步一步地删除状态。

```
function countRecords(file) {
  var x = 0;                    // here's our state
  for(var c in file.lines()) {
    x++;                        // it changes each time through the loop
  }
  return x;
} 
```

Enter fullscreen mode Exit fullscreen mode

变量`x`是 state。它的值取决于循环体执行了多少次。通常，这种计数循环是不必要的，因为标准库已经可以对一个
集合进行计数。

```
function countRecords(file) {
  return file.lines().length();  // we prefer not having to deal with the state
} 
```

Enter fullscreen mode Exit fullscreen mode

哇！现在没有国家了。事实上，它很短，我们可以直接嵌入。在`processFile`叫一次。让我们把它放在那里。

```
function processFile() {
  var file = openFile("input.txt");
  var recordCount = file.lines().length(); // inline the one-liner (optional)
  generateOutput(file, recordCount);
} 
```

Enter fullscreen mode Exit fullscreen mode

那更好。但是我们还有国家。不是特别多，但是让我们继续练习。注意我们是如何依赖`recordCount`的状态传递给`generateOutput`的。谁能保证我们提供的数量与`file`的数量没有区别？一个可能的方向是将`recordCount`计算转移到`generateOutput`中。当`generateOutput`可以自己计算的时候，为什么要相信别人呢？

```
function generateOutput(file) { // eliminate an argument that needed to be kept in sync
  var recordCount = file.lines().length(); // calculate it ourselves
  for(var c in file.lines()) {
    print(c + "," + recordCount);
  }
}

function processFile() {  // now our process is two steps
  var file = openFile("input.txt");
  generateOutput(file);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们不需要那个叫做`file`的小局部变量了。

```
function processFile() {
  generateOutput(openFile("input.txt")); // it can be written as one step
} 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

我把这个简单的例子发挥到了极致。是的，这是一个微不足道的例子。但是我对真实世界代码的经验告诉我，当你移除真实系统中的全局可变状态时，你会看到同样的改进。代码变得更容易推理(因为您是在本地推理)。重构变得更加容易。消除代码变得更加容易。

减少全局可变状态是函数式编程的特征之一。但这也只是*好的*编码。您可以(也应该)在任何编程语言或范式中进行这种重构。如果你对函数式编程感兴趣，我推荐 [PurelyFunctional.tv 时事通讯](http://www.purelyfunctional.tv/newsletter/)。是关于函数式编程的每周邮件，重点是 Clojure。我还将向您发送一些关于学习 Clojure 的重要信息。