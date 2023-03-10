# 对编程有一个基本的了解需要很长时间吗？

> 原文：<https://dev.to/adamkdean/does-it-take-long-to-get-a-basic-understanding-of-programming-o5d>

Reddit 用户[HONEY _ 大腿](http://www.reddit.com/user/HONEY_THIGHS)今天问了一个非常[好的问题](http://www.reddit.com/r/learnprogramming/comments/1c4jx6/as_someone_interested_in_programming_but_with/)，我想回答得更实际一点。

> 对编程有一个基本的了解需要很长时间吗？

编程语言基本上只是一套规则。你写一些“代码”来遵守这些规则，或者一个[编译器](http://en.wikipedia.org/wiki/Compiler)或者一个[解释器](http://en.wikipedia.org/wiki/Interpreter_(computing))会读取你的代码并应用这些规则来得到一个结果。有许多不同的编程语言，每一种都是为不同的目的而设计的，但基本上它们都以相同的方式工作。

所以让我们做一点编程。我们要创造一种叫做蜂蜜的语言。蜂蜜将是一种非常简单的语言。我们要在大脑中运行它，用我们的大脑作为处理器。

蜂蜜需要一些东西。

首先，它需要能够向用户打印输出。为此，我们将制作一个名为`print`的东西。`print`将告诉计算机(在这种情况下是我们的大脑)打印输出，在这种情况下，也是我们的大脑。让我们来看看您的第一行代码:

```
print "Hello world" 
```

Enter fullscreen mode Exit fullscreen mode

这是最基本的编程。我们首先有我们的`print`指令，在它之后我们有我们的消息`"Hello World"`。这只是告诉计算机将 Hello world 打印给用户。简单，但不是很有用。

你可能已经注意到我们的信息被双引号包围了。在编程领域，我们经常用双引号将一组文本组合在一起。这是一种简单的说法，我的句子从这里开始，在那里结束。一串文本称为一个`string`。把它想象成组成一个单词的一串字母。字面意思是字符串上的字母:

```
----H-e-l-l-o-----w-o-r-l-d---- 
```

Enter fullscreen mode Exit fullscreen mode

好吧，现在蜂蜜能够输出信息，但这有什么用呢？我们需要做一些更有用的事情。为了做到这一点，我们需要一些叫做变量的东西。变量基本上是我们给值(字符串、数字等)起的名字，这样我们以后就可以再次找到它们。

你可能在学校学过代数。如果是这样，你可能还记得`x + 1 = 2, what is x?`之类的简单问题。嗯，`x`将保存`1`的值，因为 1 + 1 等于 2。变量的工作方式很像这样。

以这一小段代码为例，你能猜出它是做什么的吗？

```
message = "Hello world"
print message 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码会输出:`Hello world`

首先，我们给一个名为 message 的*变量*分配一些文本。然后我们打印消息。我们的代码所做的是获取我们的*字符串*，即文本记忆，并以名称`message`将其存储在某处。然后，当我们试图打印我们的`message`时，我们可以找到我们的原始字符串，因为我们有它存储的名字。

这个怎么样？

```
name = "Adam"
print name 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码会输出:`Adam`。

注意我们是如何改变变量的名字的，但是它仍然是一样的？你可以随意称呼你的变量，只要它们不违反编程语言的规则。亲爱的，在我们的语言中，我们只允许你使用字母表中的字母。

我们也可以在变量中存储数字。存储数字时，不使用双引号。双引号告诉计算机你存储的是一个字符串，数字就是数字。让我们尝试另一段代码:

```
age = 23
print "Your age is "
print age 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码会输出:`Your age is 23`

注意我们是如何使用两条`print`指令的；一个用于字符串，一个用于年龄。我们可以很容易地把这些放在一起。让我们把它们加在一起，看看会得出什么结果:

```
age = 23
print "Your age is " + age 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码会输出:`Your age is 23`

如你所见，我们已经把号码加到了文本的末尾，并打印了完整的信息。通常在编程语言中，当你在文本中添加一个数字时，这个数字就成为了文本的一部分。但是如果你把两个数相加会怎么样呢？让我们试一试。

```
a = 7
b = 5
print a + b 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码会输出:`12`

啊哈！当你把数字加在一起时，它们仍然是数字。所以`a`被加到`b`上，结果`12`被打印出来。太神奇了。我们的编程语言现在稍微有用一些，但是还没有完成。我们还需要一个组件才能开始工作。

我们需要能够从用户身上获取数据。我们称这个指令为`input`。它是这样工作的:

```
print "What is your name?"
name = input
print "Your name is " + name 
```

Enter fullscreen mode Exit fullscreen mode

> 输入:`adam`
> T3】输出:`Your name is adam`

通过请求一个名为`name`的变量来存储我们的指令`input`，我们将让计算机向用户请求一些数据。把它想象成一台自动取款机向你索要密码:

```
print "Enter PIN:"
pin = input 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是一种非常简单的工作方式，但是很有意义。我们现在能够编写一个被认为有用的小程序。

在我们制作程序之前，我们要添加一个小东西，叫做注释。它们非常重要。注释让你在源代码中给程序员写消息，但是它们会被编译器忽略。通常你会用一两个字符作为你评论的前缀，在我们的例子中，我们将使用' # '字符。

```
# This is a comment, and it's totally ignored by the computer
print "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们有意见。现在让我们写程序吧！

```
# Language: Honey
# Author: OP & reader
# Description: USD to GBP conversion tool

# $1.00 USD = £0.65 GBP
# let's store the ratio for later
ratio = 0.65

# get the amount of dollars from the user   
print "Enter USD $: "
usd = input

# now let's convert the dollars into her majesty's pounds   
gbp = usd * ratio;

# and now let's output the result
print "$" + usd + " equals £" + gbp 
```

Enter fullscreen mode Exit fullscreen mode

非常好。现在我们来测试一下。假设我们有 100 美元，我们想把它兑换成英镑:

> 输入:`100`
> T3】输出:`$100 equals £65`

让我们尝试一个更复杂的数字:

> 输入:`770.05`
> T3】输出:`$770.05 equals £500`

现在你知道了。这是对编程的基本理解。

我希望这有助于您更好地理解编程这个奇妙而又令人困惑的世界。如果您有任何问题，请随时留下评论或与我联系。

<sub>延伸阅读:

[Wikipedia 上的计算机编程](http://en.wikipedia.org/wiki/Computer_programming)

[Wikipedia 上的 Python 编程语言](http://en.wikipedia.org/wiki/Python_(programming_language))

[Wikibooks 上的 Python 2.6 非程序员教程](http://en.wikibooks.org/wiki/Non-Programmer's_Tutorial_for_Python_2.6)</sub>