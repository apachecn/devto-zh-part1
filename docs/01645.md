# 亲爱的 LINQ，我爱你，但我要回到我的根

> 原文：<https://dev.to/anotherdevblog/linq-i-love-you-but-im-going-back-to-my-roots-19al>

我今天遇到了一个有趣的情况。我使用存储过程从数据库中获取一组对象，但是我只需要返回的一些对象。我必须以列表的形式返回对象。

听起来像是 LINQ where 子句的一个好工作，对吗？

```
public List<object> DoWork() => return GetObjects().Where(ShouldKeep).ToList();
private bool ShouldKeep(object o)
{
  // Do a bit of work here...
} 
```

啊，不错。短。可读。太美了。不幸的是，这不是一个好主意。

## 当 LINQ 不合适

听着，我爱 LINQ。当 Java 开发者问我是什么让我如此热爱 C#时，LINQ 是我的首选。也就是说，我认为我们最大的罪恶之一。NET 开发者犯的错误是过度使用 LINQ。我今天的用例给了我几个不使用我最喜欢的特性的理由。他们中的任何一个人都可能足以取消 LINQ 作为解决这个问题的有效工具的资格。以下是一些例子:

### 三个臭皮匠胜过一个诸葛亮。但有两个系列不是。

由于我们的数据存储库的实现方式，那个`GetObjects()`方法没有返回一个`IEnumerable<object>`。它不是一次一个元素的`yield return`。它不使用延迟执行。它返回一个列表。一个在我的堆上占用内存的列表。这个列表可能会变得很大。

不幸的是，我也需要返回一个列表。只是一个(潜在的)更小的。困难就在这里:那个`.Where(ShouldKeep).ToList()`创建了一个*第二列表*，它的大小与我从数据库调用中获取的潜在大列表的大小相同。您会正确地指出，在这种情况下，第一个列表只是一个中间列表。当该方法返回最终结果列表时，它将超出范围并被标记为垃圾收集。这是事实。但是当我亲爱的 LINQ 正在构建最终结果列表时，你不能逃避这样一个事实，即*将会有两个潜在的大列表给你的进程带来内存压力。*

### LINQ 最适合原子作战

好吧，我撒谎了。我的`ShouldKeep()`方法不只是将元素作为它的参数。决定任何给定的元素是否应该在最终列表中返回要复杂和微妙得多(并且非常有状态)。它不仅取决于元素本身的性质，还取决于列表中其他元素的特性、其他列表中的元素，甚至(吞咽)潜在的另一个数据库调用。

```
private bool ShouldKeep(List<object> list, object o, int index, DataProvider database, CustomConfiguration cfg, ...)
{
  // Do a LOT of work here...
} 
```

所有这些因素都带来了它们自己的计时器、日志和业务逻辑的权重。我承认，一些开发人员可能会将它分解成一堆独立的谓词，并使用一些鲜为人知的 LINQ 重载，这些重载返回带有元素的索引，并执行各种奇妙的巫术，但这不是我现在想玩的游戏。这使得代码更难阅读，甚至更难在其中设置断点。LINQ 擅长处理谓词的原子操作，但是如果你想太花哨，你可能会牺牲一些大的东西。

## IEnumerable 牛逼。但只有一次。

下一点与我的特定用例没有严格的关系，因为我已经提到过我调用的数据库方法返回一个列表，但是既然我已经在我的 soapbox 上，我也可以这样做。不要把`IEnumerable<T>`当成一个实际的集合。这里有一个例子:

```
public static void Main()
{
  Console.WriteLine("Starting");
  var myNumbers = GetNumbersFromDatabase();
  if (myNumbers.Any())
  {
    Console.WriteLine("Got back " + myNumbers.Count() + " results. Here they are:");
    foreach (var number in myNumbers)
    {
    Console.WriteLine(number);
    }
  }
  else
  {
    Console.WriteLine("Didn't get any results");
  }
  Console.WriteLine("Done");
} 
```

在粗略的代码评审过程中，乍一看，这可能没什么问题。也许你甚至知道`GetNumbersFromDatabase()`点击 3 个不同的表，所以它有点贵，但没关系，你只点击一次，所以它很好，对不对？

当然，一切都好。很好，直到它进入生产阶段，您的性能慢如蜗牛。你的用户开始抱怨他们看到，“得到 0 个结果。它们在这里:“而不是”没有得到任何结果。”因此，在花了几个小时寻找 bug 之后，您最终将日志记录添加到了`GetNumbersFromDatabase()`方法中:

```
private static IEnumerable<int> GetNumbersFromDatabase()
{
  Console.WriteLine("In GetNumbersFromDatabase. Let's hit the database!");
  for (var i = 1; i <= 3; i++)
  {
    Console.WriteLine("Querying table " + i);
    yield return i;
  }
} 
```

您会在控制台中看到以下内容:

```
Starting
In GetNumbersFromDatabase. Let's hit the database!
Querying table 1
In GetNumbersFromDatabase. Let's hit the database!
Querying table 1
Querying table 2
Querying table 3
Got back 3 results. Here they are:
In GetNumbersFromDatabase. Let's hit the database!
Querying table 1
1
Querying table 2
2
Querying table 3
3
Done 
```

每当你在任何实现`IEnumerable<T>`的东西上调用 LINQ 扩展方法，它将重新评估集合的提供者。如果底层对象是一个数组或一个列表，这通常没问题，因为它只对内存中已经存在的对象起作用。但是如果实现做了一些奇特的事情，比如 yield——一次返回一个元素，那么你每次都会做该方法做的所有工作。

修复？如果你需要知道关于一堆对象的*任何事情*(或者对其做任何事情),而不是仅仅遍历一次，那么把元素放在一个集合中。将`.ToArray()`添加到对`GetNumbersFromDatabase()`的调用的末尾，可以立即修复它。

另一个提示:总的来说，我喜欢关键字`var`。但是在这种情况下，`var`隐藏了数据库调用返回的对象是迭代器而不是集合的事实。所以在这种情况下，也许要重新考虑你对 var 的使用。

## 回到用例

那么我为我的`GetObjects()`案子做了什么呢？最终我想做这样的事情:

```
List<object> listFromDB = GetObjects();
foreach (var element in listFromDB)
{
  if (!ShouldKeep(element, ...))
  {
    listFromDB.Remove(element);
  }
}
return listFromDB; 
```

但是唉，如果我试着去运行，那我就会得到《美好的旧》的集合被修改了；枚举操作可能不执行“运行时出现异常。这几乎就像 CLR 在*恳求*你复制这个集合(这是不使用 LINQ 的首要原因)。

### 甩开 LINQ *和*走向前方

那么我们该如何解决这个问题呢？我们已经抛弃了 LINQ。让我们抛弃我们另一个 C#最爱，`foreach`。如果我们通过索引访问元素，跟踪迭代状态的责任就落在了 *us* 身上，而不是运行时，所以。当我们试图改变一些事情时，网络不会抱怨和对我们大喊大叫:

```
List<object> listFromDB = GetObjects();
for (var i = 0; i < listFromDB.Count; i++)
{
  if (!ShouldKeep(element, ...))
  {
    listFromDB.RemoveAt(i);
  }
}
return listFromDB; 
```

让我们以一个玩具为例，过滤掉 3 到 7 之间的数字:

```
public static void Main()
{
  var list = Enumerable.Range(1, 10).ToList();
  for (var i = 0; i < list.Count; i++)
  {
    if (list[i] >= 3 && list[i] <= 7)
    {
      list.RemoveAt(i);
    }
  }
  Console.WriteLine(string.Join(",", list));
} 
```

### 收藏移除陷阱

让我们看看输出:

```
1,2,4,6,8,9,10 
```

哇，那个 4，6，8 是怎么回事？嗯，就像我说的，当我们通过索引访问元素时，跟踪迭代状态的责任落在了 *us* 身上，而不是运行时。我们做错了。当我们删除索引中的一个元素时，这个元素之后的所有元素的索引都下移一位。所以我们删除 I 处的元素，然后 for 循环将我们带到元素 i + 1。但是以前的 i + 1 已经下降为 I，所以我们现在计算以前的 i + 2，完全跳过 i + 1。每当在当前索引处移除时，请确保递减迭代索引，这样就不会跳过任何内容:

```
public static void Main()
{
  var list = Enumerable.Range(1, 10).ToList();
  for (var i = 0; i < list.Count; i++)
  {
    if (list[i] >= 3 && list[i] <= 7)
    {
      list.RemoveAt(i);
      i--;
    }
  }
  Console.WriteLine(string.Join(",", list));
} 
```

这样更好:

```
1,2,8,9,10 
```

现在我们有了一个方法，它不会创建额外的集合，事实上，只会收缩现有的集合。

因此，下次当您面临以一种非平凡的方式过滤一个大型数据集时，请仔细检查一下您的 LINQ 和 foreach，看看您是否能够通过回到原点来轻松地释放应用程序的一些内存压力。