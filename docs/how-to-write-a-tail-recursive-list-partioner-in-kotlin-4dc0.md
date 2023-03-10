# 如何用 Kotlin 编写一个尾部递归列表分割器

> 原文：<https://dev.to/schreiber_chris/how-to-write-a-tail-recursive-list-partioner-in-kotlin-4dc0>

编写一个列表分区函数是一个经典的编程面试练习。我想给你看一个这个问题的解决方案，用 Kotlin 写的。我的解决方案将利用尾递归，这是一个非常有趣的语言特性，我们在 Java 中已经错过了很久。我将使用测试驱动开发(TDD)一步步进行。

## 先决条件

如果您使用的是 IntelliJ IDEA，那么从 IDE 版本 15 开始，Kotlin 就是现成兼容的。如果您使用的是另一个 IDE，请按照 Kotlin 的教程页面上关于如何启动和运行您的环境的说明进行操作。

## 练习的讲解

问题很简单:我们想写一个方法，允许将一个列表划分成一个给定大小的列表(称为分区大小)。
例如，由大小为 2 的{1，2，3，4，5}个分区组成的列表将为{ {1，2}，{3，4}，{5} }。
当然，list size 应该严格大于 0，否则这就是一个无意义的调用。该方法应该能够处理任何类型的列表，如字符串列表、整数列表...

## 第一步:测试失败

像在 TDD 中一样，我首先要写一个失败的测试。

```
class ListPartitionerKtTest {

    @Test
    fun empty_list_should_return_an_empty_list() {
        assertEquals(emptyList<List<String>>(), emptyList<String>().myPartition())
    }
} 
```

如您所见，我选择将我的分区方法实现为 Kotlin 列表类型的 myPartition 函数扩展。我选择了 myPartition 这个名称，以避免与现有的 List.partition 方法混淆(后者没有相同的目的)。让我们通过这个测试:

```
fun List<String>.myPartition() = emptyList<List<String>>()) 
```

## 被仿制

现在，让我们通过处理多种类型的列表来实现练习的一个约束:

```
@Test fun should_also_handle_integers() {
    assertEquals(emptyList<List<Int>>(), emptyList<Int>().myPartition()) 
```

我们必须改变我们的实现，使它变得通用:

```
fun <T> List<T>.myPartition() = emptyList<List<T>>() 
```

## 声明分区大小

我们的测试通过了，但是我们的 myPartition 函数的签名在规格方面是不正确的:我们必须能够选择分区大小。所以，让我们写一个失败的测试:

```
@Test fun should_accept_different_partition_size() {
    assertEquals(emptyList<List<Int>>(), emptyList<Int>().myPartition(3))
} 
```

显然，代码无法编译，所以让我们向 myPartition 添加一个参数，用默认值来避免修改我们之前的测试:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) = emptyList<List<T>>() 
```

## 检查分区大小

现在我们有了分区大小参数，我们必须检查它是否是一个有效的参数。分区大小必须严格大于 0。

```
@Test(expected = IllegalArgumentException::class) fun partition_size_should_not_be_negative() {
    emptyList<Int>().myPartition(-1)
}
@Test(expected = IllegalArgumentException::class) fun partition_size_should_be_strictly_greater_than_0() {
    emptyList<Int>().myPartition(0)
} 
```

要修复我们的实现，我们有几件事情要做:

*   在参数上添加控件并引发异常
*   因为我们要写一个完整的方法体，我们需要写一个显式的类型返回和一个返回语句

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<T> {
    if (partitionSize < 1) throw IllegalArgumentException("Partition sizz should be strictly greater than 0, ${partitionSize} is invalid")
    return emptyList<List<T>>()
} 
```

我们的测试通过了，所以让我们稍微重构一下，以避免出现一长串的异常初始化:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<List<T>> {
    validatePartitionSize(partitionSize)
    return emptyList<List<T>>()
}

private fun validatePartitionSize(partitionSize: Int) {
    if (partitionSize < 1) throw IllegalArgumentException("Partition sizz should be strictly greater than 0, ${partitionSize} is invalid")
} 
```

一切都还是绿色的，让我们继续前进。

## 对小于分区大小的列表进行分区

我们将编写实际的列表分区。第一步很简单:如果列表小于分区大小，我们只需返回一个包含列表本身的列表:

```
@Test fun given_list_smaller_than_partition_size_should_return_the_a_list_containing_source_list() {
    val list = listOf(1)
    assertEquals(listOf(list), list.myPartition(2))
} 
```

像在 TDD 中一样，让我们编写最简单的代码来通过这个测试:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<List<T>> {
    validatePartitionSize(partitionSize)
    return listOf(this)
} 
```

我们可以添加一个正好具有分区大小的列表:

```
@Test fun given_list_size_equals_to_partition_size_should_return_the_a_list_containing_source_list() {
    val list = listOf(1, 2)
    assertEquals(listOf(list), list.myPartition(2))
} 
```

因为测试通过了，所以没有代码可写！

## 让我们(最后)划分一个列表吧！

下一个测试是显而易见的:我们用默认的分区大小(2)传递一个包含 3 个元素的列表，我们期望得到一个包含两个元素的列表:

*   包含前两个元素的列表
*   包含第三个元素的列表

```
@Test fun given_1_2_3_and_partition_size_2_should_return_1_2_and_3() {
    val list = listOf(1, 2, 3)
    assertEquals(listOf(listOf(1, 2), listOf(3)), list.myPartition(2))
} 
```

同样，让我们实现最少的代码来修复这个测试:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<List<T>> {
    validatePartitionSize(partitionSize)
    if (this.size <= partitionSize)
        return listOf(this)
    else
        return listOf(this.take(partitionSize), takeLast(this.size - partitionSize))
} 
```

测试通过了，但是这看起来很难看，所以让我们使用 Kotlin 的 when 操作符来重构它:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<List<T>> {
    validatePartitionSize(partitionSize)
    return when {
        this.size <= partitionSize -> listOf(this)
        else -> listOf(this.take(partitionSize), takeLast(this.size - partitionSize))
    }
} 
```

我们可以添加一个应该已经通过的测试:大小为 4 的列表，分区大小为 2:

```
@Test fun given_1_2_3_4_and_partition_size_2_should_return_1_2_and_3_4() {
    val list = listOf(1, 2, 3, 4)
    assertEquals(listOf(listOf(1, 2), listOf(3, 4)), list.myPartition(2))
} 
```

都是绿色的，所以我们什么都不改:)

## 对一个列表进行多次分区

我们之前的测试只涉及一个分区，现在我们将事情变得复杂一点，并转向一个真正的分区实现:

```
@Test fun given_1_2_3_4_5_and_partition_size_2_should_return_1_2_and_3_4_and_5() {
    val list = listOf(1, 2, 3, 4, 5)
    assertEquals(listOf(listOf(1, 2), listOf(3, 4), listOf(5)), list.myPartition(2))
} 
```

通过测试的最简单方法是在我们的实现中添加一点递归:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2) : List<List<T>> {
    validatePartitionSize(partitionSize)
    return when {
        this.size <= partitionSize -> listOf(this)
        else -> listOf(this.take(partitionSize))
         + takeLast(this.size - partitionSize).myPartition(partitionSize)
    }
} 
```

将 myPartition 递归地应用到列表的最后一个元素，并将其添加到结果列表中，这样我们的测试就可以通过了。我们现在有了一个工作列表分区方法！

## 递归的危害

当然，任何玩过递归调用的程序员都会发现我们实现的弱点:对于大列表，我们肯定会面临 StackOverflowException 错误。
让我们添加一个测试来测试我们方法的局限性:

```
@Test fun should_handle_big_lists() {
    val list = getListOfSize(1000)
    list.myPartition(2)
}

private fun getListOfSize(size: Int): List<Int> {
    val range = 1..size
    var list = emptyList<Int>()
    for (i in range) list = list + i
    return list
} 
```

显然，我们的大列表不够大，我们的测试正在通过。让我们使用一个真正的大列表:

```
 @Test fun should_handle_really_big_lists() {
        val list = getListOfSize(100000)
        list.myPartition(2)
    } 
```

我们终于面对堆栈溢出错误了，太好了！为了修复这个测试，我们将使用尾部递归优化。这将允许编译器重用最后一个堆栈元素，而不是创建一个新的元素，因此堆栈溢出错误将会消失。为了实现尾部递归，我们的代码必须遵守以下约束:

*   函数的最后一次调用(返回语句)应该是递归调用
*   我们必须通过在函数定义中使用 *tailrec* 关键字来指示 Kotlin 编译器应该使用尾部递归优化。尾部递归通常使用私有方法来完成，该方法将获取我们调用的当前参数(这里是列表和分区大小)以及所有先前步骤的结果，通常称为累加器。让我们为我们的分区方法实现这个:

```
fun <T> List<T>.myPartition(partitionSize: Int = 2): List<List<T>> {
    validatePartitionSize(partitionSize)
    return myRecursivePartition(this, partitionSize, emptyList())
}

private tailrec fun <T> myRecursivePartition(list : List<T>, partitionSize: Int = 2,
     accumulator : List<List<T>>): List<List<T>> {
    return when {
        list.isEmpty() -> accumulator
        list.size <= partitionSize -> accumulator + listOf(list)
        else -> myRecursivePartition(list.takeLast(list.size - partitionSize),
                partitionSize,
                accumulator + listOf(list.take(partitionSize)))
    }
} 
```

我们的 myPartition 函数现在只是用正确的参数调用递归函数:

*   当前列表
*   分区大小
*   累加器的初始状态:一个空列表

递归方法有 3 个不同的返回条件:

*   如果当前列表是空的，这意味着我们已经完成了划分，结果是累加器，它是逐步计算出来的
*   如果当前列表的大小小于或等于分区的大小，我们返回累加器加上列表。这也是一个终端操作
*   最后一种情况是递归发生的地方:我们用剩余的元素调用相同的方法，并将当前分区添加到累加器中

我们对一个非常大的列表的测试现在通过了！尾部递归是 Scala 等函数式语言支持的非常有用的工具，但许多 Java 程序员并不经常使用它，因为 Oracle 的语言目前不支持这种优化。
多亏了 Kotlin，我们现在可以使用这个工具了:)
我希望这篇文章对你有用。如果你认为这个实现可以改进，或者你需要更多关于这个例子的信息，请不要犹豫给我发送反馈。
你可以在[我的 Github](https://github.com/ChristopheSchreiber/KotlinListPartitioner) 上找到代码。