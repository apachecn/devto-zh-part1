# 如何在 Fizzbuzz 面试挑战中永不失败

> 原文：<https://dev.to/waterlink/how-to-never-fail-at-the-fizzbuzz-interview-challenge-62o>

啊，经典的 FizzBuzz 编码挑战。

我决定附上这张照片以引起你的注意。哦，还因为它与挑战的“嗡嗡”部分有关。

“嗡嗡”——闹钟…

前进！你知道吗，你甚至没有一个理由在这些考试中失败？你认为你很了解编码形吗？

让你的想象力自由驰骋:

你正在进行编码面试，面试官刚刚要求你实现 FizzBuzz，并给了你以下问题描述:

> 写一个程序，打印从 1 到 n 的数字序列。
> 
> 每个被三除的数字都应该显示为“嘶嘶”
> 
> 每个被 5 除的数字都应该显示为“嗡嗡声”
> 
> 每一个被 3 和 5 除的数字都应该显示为“FizzBuzz”
> 
> 匿名采访者

小菜一碟！

所以你在写，代码从你体内涌出…

五到十分钟后，你就完成了。你觉得面试官可能会怀疑你已经准备好迎接挑战了。那样的话，他们会对你有一些额外的意想不到的要求。

就像在现实世界编程一样。

继续阅读，我们将探索这些额外的需求和可能的解决方案。现在，我们将在科特林解决 FizzBuzz 挑战。

进行一些自动化测试可能是个好主意，这样我们就知道什么时候完成了。让我们在 JUnit4 中创建一个新的测试套件:

```
import org.junit.Assert.*
import org.junit.Test

@Suppress("FunctionName")
class FizzBuzzTest {

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要编写第一个测试——最简单的一个，它涵盖了我们可以产生一个没有任何“嘶嘶”或“嗡嗡”声的正常数字序列的事实

```
@Test
fun `simple numbers`() {
    // ARRANGE
    val fizzBuzz = FizzBuzz()

    // ACT
    val sequence = fizzBuzz.generateSequence(size = 2)

    // ASSERT
    assertEquals(listOf("1", "2"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢把我的测试分成三个部分:安排、行动和断言。没有必要像我在这里一样大声写下这些评论。我自己通常不这么做，但是在构建一个好的测试时，我会考虑这三个部分。

另一方面，我强烈建议你将测试分成三个部分。为了清楚起见，也写下这些注释。

如果你已经习惯于编写优秀的测试，请随意忽略我的建议。

前进！在这个测试中，我们需要处理的编译错误很少。首先，我们还没有定义`FizzBuzz`类。其次，我们需要创建`generateSequence`方法。

为了使事情能够编译，我们将使该方法返回一个空列表:

```
class FizzBuzz {

    fun generateSequence(size: Int): List<String> {
        return emptyList()
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行我们的测试。不出所料，它失败了，因为我们期望一个有两个条目的列表`["1", "2"]`，但是我们得到的是一个空列表`[]`。

完成这个过程非常简单。我们将使用 Kotlin 的范围语法:
生成一个从 1 到`size`的序列

```
1..size 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将通过对序列的每一项调用`toString()`方法来生成一个新的序列。为此，我们将使用定义在任何科特林序列上的`map`函数:

```
fun generateSequence(size: Int): List<String> {
    return (1..size).map { it.toString() }
} 
```

Enter fullscreen mode Exit fullscreen mode

哦，对了。

想知道 Kotlin 所有这些简洁的功能是什么吗？也许您想学习如何用 Kotlin 构建一个成熟的应用程序？

我无意中为科特林写了这篇 80 页的动手入门教程。欢迎[下载免费 PDF 终极教程:Kotlin 入门](https://iwillteachyoukotlin.com)。您将学习如何用 Kotlin 构建一个成熟的命令行应用程序。您还将获得免费的 Web 应用程序和 Android 应用程序教程预览！

不管怎样，我们现在应该开始测试了。

他们都通过了。

好吧。我们的下一步是开始处理一些“嘶嘶”数字。为此，我们可以编写一个新的测试。这就像复制粘贴一样简单(哦，不！)之前的测试并对其做了一些修改:

```
@Test
fun `fizz numbers`() {
    // ARRANGE
    val fizzBuzz = FizzBuzz()

    // ACT
    val sequence = fizzBuzz.generateSequence(size = 4)

    // ASSERT
    assertEquals(listOf("1", "2", "Fizz", "4"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们从一到四，我们希望所有的数字都是正常的，除了数字三。它应该在生成的序列中变成“Fizz”。

如果我们运行这个测试，它将会失败，因为到目前为止，我们只生成转换为字符串的简单数字。

为了通过这个测试，我们将把`it.toString()`部分提取到私有方法`generateMethod(number: Int): String`中。我们将添加一个“如果”语句来处理数字被三除的情况:

```
fun generateSequence(size: Int): List<String> {
    return (1..size).map { generateNumber(it) }
}

private fun generateNumber(number: Int): String {
    if (number % 3 == 0) {
        return "Fizz"
    }

    return number.toString()
} 
```

Enter fullscreen mode Exit fullscreen mode

它现在通过了测试套件。

接下来，我们将为“嗡嗡声”需求编写一个测试。为此，我们将生成一个从 1 到 14 的序列。那里会有一些“嘶嘶”声和“嗡嗡”声。

不过，还没有“嘶嘶作响”的声音。

```
@Test
fun `buzz numbers`() {
    // ARRANGE
    val fizzBuzz = FizzBuzz()

    // ACT
    val sequence = fizzBuzz.generateSequence(size = 14)

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "13", "14"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

这个测试失败了，因为我们还没有产生任何“嗡嗡声”。我们将添加另一个“if”语句来处理这种情况:

```
private fun generateNumber(number: Int): String {
    if (number % 3 == 0) {
        return "Fizz"
    }

    if (number % 5 == 0) {
        return "Buzz"
    }

    return number.toString()
} 
```

Enter fullscreen mode Exit fullscreen mode

测试通过了。

最后，我们想写一个涉及一些“fizzbuzzy”数字的测试。在这种情况下，我们希望生成一个从 1 到 31 的序列，这样我们至少包括其中的两个:

```
@Test
fun `fizz buzz numbers`() {
    // ARRANGE
    val fizzBuzz = FizzBuzz()

    // ACT
    val sequence = fizzBuzz.generateSequence(size = 31)

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "13", "14", "FizzBuzz",
            "16", "17", "Fizz", "19", "Buzz",
            "Fizz", "22", "23", "Fizz", "Buzz",
            "26", "Fizz", "28", "29", "FizzBuzz",
            "31"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，这是行不通的。

这是因为第一个“if”语句(代表“fizzes”)捕捉到了这种情况，并产生了“Fizz”。为了解决这个问题，我们必须编写一个新的“if”语句，同时处理“嘶嘶”和“嗡嗡”的情况。

那个案件必须在所有其他案件之前检查。这样，无论是“嘶嘶”，还是“嗡嗡”，都不会在它之前匹配:

```
private fun generateNumber(number: Int): String {
    if (number % 3 == 0 && number % 5 == 0) {
        return "FizzBuzz"
    }

    if (number % 3 == 0) {
        return "Fizz"
    }

    if (number % 5 == 0) {
        return "Buzz"
    }

    return number.toString()
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧。所有测试都通过了。

让我们在测试套件中提取一些副本。值得注意的是，`ARRANGE`部分。这是因为每一次测试都是一样的。我们将把`fizzBuzz`局部变量提升到测试套件类的私有字段:

```
import org.junit.Assert.*
import org.junit.Test

@Suppress("FunctionName")
class FizzBuzzTest {

    private val fizzBuzz = FizzBuzz()

    @Test
    fun `simple numbers`() {
        // ACT
        val sequence = fizzBuzz.generateSequence(size = 2)

        // ASSERT
        assertEquals(listOf("1", "2"), sequence)
    }

    @Test
    fun `fizz numbers`() {
        // ACT
        val sequence = fizzBuzz.generateSequence(size = 4)

        // ASSERT
        assertEquals(listOf("1", "2", "Fizz", "4"), sequence)
    }

    @Test
    fun `buzz numbers`() {
        // ACT
        val sequence = fizzBuzz.generateSequence(size = 14)

        // ASSERT
        assertEquals(listOf(
                "1", "2", "Fizz", "4", "Buzz",
                "Fizz", "7", "8", "Fizz", "Buzz",
                "11", "Fizz", "13", "14"), sequence)
    }

    @Test
    fun `fizz buzz numbers`() {
        // ACT
        val sequence = fizzBuzz.generateSequence(size = 31)

        // ASSERT
        assertEquals(listOf(
                "1", "2", "Fizz", "4", "Buzz",
                "Fizz", "7", "8", "Fizz", "Buzz",
                "11", "Fizz", "13", "14", "FizzBuzz",
                "16", "17", "Fizz", "19", "Buzz",
                "Fizz", "22", "23", "Fizz", "Buzz",
                "26", "Fizz", "28", "29", "FizzBuzz",
                "31"), sequence)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们做测试，他们应该还能通过。作为我们的下一步，我们应该仔细检查生产代码中是否有任何重复。

而且还有一些！

在“fizzbuzz”案例处理程序和相应的“fizz”或“buzz”案例处理程序之间，对“fizz”和“buzz”的检查是重复的。我们能做的最简单的重构就是将它们提取到私有函数中:

```
private fun generateNumber(number: Int): String {
    if (isFizz(number) && isBuzz(number)) {
        return "FizzBuzz"
    }

    if (isFizz(number)) {
        return "Fizz"
    }

    if (isBuzz(number)) {
        return "Buzz"
    }

    return number.toString()
}

private fun isBuzz(number: Int) = number % 5 == 0

private fun isFizz(number: Int) = number % 3 == 0 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，您将要进行更多的重构，例如，将神奇的字符串提取为常量。

但是面试官告诉你重构已经足够了。

你看他们印象深刻。你毕竟提前完成了。但是你觉得面试官有点怀疑。毕竟是经典的练习。

所以他们决定给你一个额外的要求！

> 如果一个数字包含数字“3”，它也应该是“Fizz”
> 
> 如果一个数字包含数字“5”，它也应该是“Buzz”
> 
> 如果一个数字包含两者，它也应该是“FizzBuzz”。
> 
> 匿名采访者

哦，不！

没人告诉你会有更多…

不要烦恼！我们会经历这一切。让我们根据新的要求为“Fizz”部分添加一个新的测试。

我们将生成序列，直到 32。一些以前不“嘶嘶”的数字，会变成这样:

```
@Test
fun `fizz numbers that just contain digit '3'`() {
    // ACT
    val sequence = fizzBuzz.generateSequence(size = 32)

    // 13 becomes Fizz
    // 23 becomes Fizz
    // 31 becomes Fizz
    // 32 becomes Fizz

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "Fizz", "14", "FizzBuzz",
            "16", "17", "Fizz", "19", "Buzz",
            "Fizz", "22", "Fizz", "Fizz", "Buzz",
            "26", "Fizz", "28", "29", "FizzBuzz",
            "Fizz", "Fizz"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，测试失败了。

为了让它通过，我们需要适当地修改函数`isFizz`。您应该在原始条件的顶部加上“它包含数字 3”的检查。在布尔逻辑中(上次我检查过)，这是两个条件之间的“或”运算:

```
private fun isFizz(number: Int) =
        number % 3 == 0 || number.toString().contains('3') 
```

Enter fullscreen mode Exit fullscreen mode

注意`number.toString().contains('3')`部分。测试现在通过了。但是其他一些测试都失败了！

哦不！

我们必须修正这些测试。我们应该将包含数字“3”的数字替换为“Fizz”:

```
@Test
fun `buzz numbers`() {
    // ACT
    val sequence = fizzBuzz.generateSequence(size = 14)

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "Fizz", "14"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
@Test
fun `fizz buzz numbers`() {
    // ACT
    val sequence = fizzBuzz.generateSequence(size = 31)

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "Fizz", "14", "FizzBuzz",
            "16", "17", "Fizz", "19", "Buzz",
            "Fizz", "22", "Fizz", "Fizz", "Buzz",
            "26", "Fizz", "28", "29", "FizzBuzz",
            "Fizz"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

等一下…你不觉得这很奇怪吗？

是的，我想这最后一个案子涵盖了我们所有的边缘案件。这是因为它(有点)包括所有其他测试。所以你应该摆脱那些测试！

哦，不要忘记将私有字段`fizzBuzz`降级回局部变量。

开始了:

```
import org.junit.Assert.*
import org.junit.Test

@Suppress("FunctionName")
class FizzBuzzTest {

    @Test
    fun `generating fizz buzz sequence`() {
        // ARRANGE
        val fizzBuzz = FizzBuzz()

        // ACT
        val sequence = fizzBuzz.generateSequence(size = 32)

        // ASSERT
        assertEquals(listOf(
                "1", "2", "Fizz", "4", "Buzz",
                "Fizz", "7", "8", "Fizz", "Buzz",
                "11", "Fizz", "Fizz", "14", "FizzBuzz",
                "16", "17", "Fizz", "19", "Buzz",
                "Fizz", "22", "Fizz", "Fizz", "Buzz",
                "26", "Fizz", "28", "29", "FizzBuzz",
                "Fizz", "Fizz"), sequence)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们想实现最后两个需求。

为此，让我们改变我们的测试，直到`53`，这样我们会遇到更有吸引力的边缘情况。一些数字将会变成“嗡嗡声”,而另一些数字现在应该会变成“嘶嘶嗡嗡声”。

自己看:

```
@Test
fun `generating fizz buzz sequence`() {
    // ARRANGE
    val fizzBuzz = FizzBuzz()

    // ACT
    val sequence = fizzBuzz.generateSequence(size = 53)

    // 35 became FizzBuzz
    // 51 became FizzBuzz
    // 52 became Buzz
    // 53 became FizzBuzz

    // ASSERT
    assertEquals(listOf(
            "1", "2", "Fizz", "4", "Buzz",
            "Fizz", "7", "8", "Fizz", "Buzz",
            "11", "Fizz", "Fizz", "14", "FizzBuzz",
            "16", "17", "Fizz", "19", "Buzz",
            "Fizz", "22", "Fizz", "Fizz", "Buzz",
            "26", "Fizz", "28", "29", "FizzBuzz",
            "Fizz", "Fizz", "Fizz", "Fizz", "FizzBuzz",
            "Fizz", "Fizz", "Fizz", "Fizz", "Buzz",
            "41", "Fizz", "Fizz", "44", "FizzBuzz",
            "46", "47", "Fizz", "49", "Buzz",
            "FizzBuzz", "Buzz", "FizzBuzz"), sequence)
} 
```

Enter fullscreen mode Exit fullscreen mode

为了通过这一关，我们需要给`isBuzz`私有函数添加类似的条件。注意`number.toString().contains('5')`部分。

```
private fun isBuzz(number: Int) =
        number % 5 == 0 || number.toString().contains('5') 
```

Enter fullscreen mode Exit fullscreen mode

测试现在变回绿色(它们通过了)。

你没注意到`isBuzz`和`isFizz`函数的结构很相似吗？你不认为我们应该能够提取那个结构吗？

我们开始吧！

首先，我们将提取`dividesBy(number, divider)`私有函数:

```
private fun isBuzz(number: Int) =
        dividesBy(number, 5) || number.toString().contains('5')

private fun isFizz(number: Int) =
        dividesBy(number, 3) || number.toString().contains('3')

private fun dividesBy(number: Int, divider: Int) =
        number % divider == 0 
```

Enter fullscreen mode Exit fullscreen mode

其次，我们将提取`containsDigit(number, digit)`私有函数。

```
private fun isBuzz(number: Int) =
        dividesBy(number, 5) || containsDigit(number, 5)

private fun isFizz(number: Int) =
        dividesBy(number, 3) || containsDigit(number, 3)

private fun containsDigit(number: Int, digit: Int) =
        number.toString().contains(digit.toString()) 
```

Enter fullscreen mode Exit fullscreen mode

注意这里的`digit`不是一个字符或字符串。它是一个整数。

这样，我们应该能够从这两个函数中提取出整个结构(`dividesBy`或`containsDigit`)。

开始了:

```
private fun isBuzz(number: Int) =
        dividesOrContainsDigit(number, 5)

private fun isFizz(number: Int) =
        dividesOrContainsDigit(number, 3)

private fun dividesOrContainsDigit(number: Int, digit: Int) =
        dividesBy(number, digit) || containsDigit(number, digit) 
```

Enter fullscreen mode Exit fullscreen mode

你的面试官还没有停止你的重构。所以也许他是出于需求？

谢天谢地！

最后，您可以进行神奇的字符串提取重构！为此，我们将从各自的边缘情况中提取“Fizz”和“Buzz”字符串。来自“fizzbuzzy”的一个也是:

```
companion object {
    private val FIZZ = "Fizz"
    private val BUZZ = "Buzz"
    private val FIZZ_BUZZ = "$FIZZ$BUZZ"
}

private fun generateNumber(number: Int): String {
    if (isFizz(number) && isBuzz(number)) {
        return FIZZ_BUZZ
    }

    if (isFizz(number)) {
        return FIZZ
    }

    if (isBuzz(number)) {
        return BUZZ
    }

    return number.toString()
} 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们为什么要提取这些神奇的字符串呢？

你可能会问自己…

还是只是面试官问你这个问题？

可能是因为你想让在一个地方改变一个字符串变得容易，不是吗？

这就是你想到的地方！如果我需要把“嘶嘶”改成“嘶嘶”呢？

没错。

我必须在测试套件中修复所有这些问题。

烦人！

让我们也替换测试中所有的魔法字符串。我们必须将这些私有常量`FIZZ`、`BUZZ`和`FIZZ_BUZZ`提升到内部访问级别，以便测试可以访问它们:

```
companion object {
    internal val FIZZ = "Fizz"
    internal val BUZZ = "Buzz"
    internal val FIZZ_BUZZ = "$FIZZ$BUZZ"
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将替换测试套件中出现的字符串:

```
import FizzBuzz.Companion.BUZZ
import FizzBuzz.Companion.FIZZ
import FizzBuzz.Companion.FIZZ_BUZZ
import org.junit.Assert.*
import org.junit.Test

@Suppress("FunctionName")
class FizzBuzzTest {

    @Test
    fun `generating fizz buzz sequence`() {
        // ARRANGE
        val fizzBuzz = FizzBuzz()

        // ACT
        val sequence = fizzBuzz.generateSequence(size = 53)

        // ASSERT
        assertEquals(listOf(
                "1", "2", FIZZ, "4", BUZZ,
                FIZZ, "7", "8", FIZZ, BUZZ,
                "11", FIZZ, FIZZ, "14", FIZZ_BUZZ,
                "16", "17", FIZZ, "19", BUZZ,
                FIZZ, "22", FIZZ, FIZZ, BUZZ,
                "26", FIZZ, "28", "29", FIZZ_BUZZ,
                FIZZ, FIZZ, FIZZ, FIZZ, FIZZ_BUZZ,
                FIZZ, FIZZ, FIZZ, FIZZ, BUZZ,
                "41", FIZZ, FIZZ, "44", FIZZ_BUZZ,
                "46", "47", FIZZ, "49", BUZZ,
                FIZZ_BUZZ, BUZZ, FIZZ_BUZZ), sequence)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

此时你所有的测试都应该是绿色的。

你需要做所有这些重构吗？

不一定。

您是否需要知道如何去做，以便为任何需求变更做好准备？

绝对的！

下面是最终的生产代码:

```
class FizzBuzz {

    companion object {
        internal val FIZZ = "Fizz"
        internal val BUZZ = "Buzz"
        internal val FIZZ_BUZZ = "$FIZZ$BUZZ"
    }

    fun generateSequence(size: Int): List<String> {
        return (1..size).map { generateNumber(it) }
    }

    private fun generateNumber(number: Int): String {
        if (isFizz(number) && isBuzz(number)) {
            return FIZZ_BUZZ
        }

        if (isFizz(number)) {
            return FIZZ
        }

        if (isBuzz(number)) {
            return BUZZ
        }

        return number.toString()
    }

    private fun isBuzz(number: Int) =
            dividesOrContainsDigit(number, 5)

    private fun isFizz(number: Int) =
            dividesOrContainsDigit(number, 3)

    private fun dividesOrContainsDigit(number: Int, digit: Int) =
            dividesBy(number, digit) || containsDigit(number, digit)

    private fun containsDigit(number: Int, digit: Int) =
            number.toString().contains(digit.toString())

    private fun dividesBy(number: Int, divider: Int) =
            number % divider == 0

} 
```

Enter fullscreen mode Exit fullscreen mode

我真诚地感谢你阅读，我鼓励你练习你的基础。

在像 http://codingdojo.org/kata/和 T2 这样的网站上有很多不同的挑战。非常感谢创造者和维护者！

喜欢这篇文章吗？–在社交媒体、黑客新闻、Reddit 等网站上与朋友分享。如果你对科特林感兴趣，[在科特林](https://iwillteachyoukotlin.com)上看看我的更多东西。