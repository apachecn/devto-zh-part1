# 通过授权对象来避免贫血的领域模型

> 原文：<https://dev.to/developerscode/avoid-anemic-domain-models-by-empowering-your-objects>

我在今天的许多面向对象代码(包括我自己的代码)中看到的一个常见的反模式是 Martin Fowler 所说的*贫血的领域模型*。他在这里非常雄辩地描述了他们[。](http://www.martinfowler.com/bliki/AnemicDomainModel.html)

这个想法的要点是，我们经常创建一些类，它们的工作不外乎保存数据。用马丁的话说，“当你...意识到这些对象上几乎没有任何行为，使它们比一袋袋的 getters 和 setters 多不了多少。”

在某些情况下，这类对象是有意义的。除了将数据从一个服务转移到另一个服务之外，没有其他用途的数据传输对象(dto)就是一个典型的例子。然而，Martin 认为，在应用程序代码的业务领域中，对象应该将数据和过程混合在一起。这是面向对象编程的全部成果。

我把面向对象编程比作写小说中的伟大人物。写线性故事是一种技巧。写汉字又是另一回事。这样做可以让故事不断在读者的脑海中建立。续集、第三部以及以后的部分对作者来说更容易描述，因为人物从一开始就发展得很好。

面向对象的写作也是如此。贫血的物体相当于描述糟糕的人物。您越能避免这种趋势，随着时间的推移编写自然迭代时，就越容易构建代码的故事。不仅你的代码获得了优秀设计的所有切实好处，而且你的读者(其他阅读你代码的程序员)也能更好地理解你所描述的内容。好的角色就是那样让人记忆深刻。

我最近在处理自己的一些代码时，偶然发现了这个问题的一个真实例子。我想我应该写下它，希望能为那些还没有完全理解这个想法的人澄清这个反模式。

最近，我为一个新的移动应用程序编写了一些 C#代码，我们正在为我的产品 [DoneDone](https://www.getdonedone.com) 使用 [Xamarin](https://www.xamarin.com/) 构建这个应用程序。DoneDone 是一个简单的基于网络的问题跟踪器，适用于小型开发团队。在该应用程序上，我们希望显示给定一天内特定问题的活动。在数据库中，我们跟踪对问题的任何修改(编辑、评论、状态更改、重新分配)以及修改发生的日期。

在代码中，我们有一个名为`ActivityFilter`的类，它保存了用户针对其问题的活动的所有搜索标准。大概是这样的:

```
public class ActivityFilter 
{
    public DateTime ActivityDate { get; set; } 
    public int ProjectId { get; set; }
    public List<int> TagIds { get; set; }
    public FilterType SelectedFilterType { get; set; }
    public DueType SelectedDueType { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

对象是我们存储用户搜索输入的地方。当用户在应用上选择一个项目时，对象的`ProjectId`被更新和缓存。当用户选择查看带有特定标签的问题的活动时，对象的`TagIds`会被更新和缓存...诸如此类。

这个对象像手套一样符合 Martin 的贫血域模型的描述——只不过是“一包 getters 和 setters”但是，就目前而言，我实际上对此很满意。该类的唯一目的是存储一些过滤数据，其他对象将使用这些数据来做脏活。它贫血，因为它不需要更多的东西。

在移动界面上，您可以选择活动发生的具体日期——今天、昨天或几年前的任意一天。应用程序将这个日期存储在一个`ActivityFilter`对象中，然后该对象被缓存在内存中。如果您明天再回到应用程序，应用程序将从缓存的对象中读取数据，并显示符合您上次设置的搜索标准的活动。

当我摆弄这种行为时，感觉有些奇怪。假设您选择查看今天的活动。然后，你离开应用程序。当你明天下午再次打开它时，应用程序会显示你从昨天*开始的活动。这在程序上非常有意义，因为最后存储的`ActivityDate`恰好是昨天的日期。但是，感觉不太自然。*

 *假设你选择查看今天的活动，说今天是*2017 年 1 月 16 日*。如果你关闭应用程序，明天再回来，你会*实际上*期待应用程序再次显示*今天*的活动。今天是 17 号，而不是 16 号。这种期望变得更加明显，因为在 UI 上，如果您选择的日期是今天的日期，我们会在活动上方显示单词“Today ”,而不是日期。

但是，假设您明确选择查看过去一天的活动(比如 2016 年 7 月 8 日)。当你明天回来的时候，你应该还能看到那天的活动——它不应该默认你回到“今天”。

总之，我真正想要的`ActivityDate`的业务逻辑是这样的:

*   如果您将活动日期设置为今天以外的日期，您应该总是看到当天的活动，直到您选择新的日期。
*   但是，如果您将活动日期设置为今天的日期，您应该总是看到“今天”(现在、明天或三周后)的活动，直到您选择新的日期。

总的来说，这个业务逻辑并不复杂。但是，谁应该拥有这一点逻辑？当然，`ActivityFilter`对象需要存储额外的信息。乍一看，似乎我们应该在这个类上公开一些额外的布尔值来表示所选择的日期是否是今天。姑且称之为`IsActivityDateSetToToday` :

```
public class ActivityFilter
{
    public DateTime ActivityDate { get; set; } 
    public bool IsActivityDateSetToToday { get; set; } // new param

    // Hiding the other properties in this post for clarity
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当设置了`ActivityDate`时，我们可以对照当前系统日期检查是否也设置了`IsActivityDateToday`。假设`_CachedActivityFilter`是应用程序会话的`ActivityFilter`对象的缓存实例的名称，设置日期的应用程序代码可能如下所示:

```
public void onActivityDateChanged(DateTime selectedDate)
{
    _CachedActivityFilter.ActivityDate = selectedDate;
    _CachedActivityFilter.IsActivityDateSetToToday = false;

    if (selectedDate == DateTime.Now.Date)
    {
        _cachedActivityFilter.IsActivityDateSetToToday = true;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*旁注:在 C#中，没有仅仅保存日期的内置类。相反，`DateTime`类存储日期和时间信息。出于我们的目的，我们只关心日期部分。`DateTime`类的`Date`属性返回一个`DateTime`对象，其时间部分自动设置为 00:00:00。因此，在我们的代码片段中，我们通过`Date`属性来评估`DateTime`对象。*

有了这些额外的信息，读取缓存的`ActivityFilter`对象的应用程序代码将执行类似这样的逻辑来获取搜索活动的正确日期:

```
var dateToSearchFor = _CachedActivityFilter.ActivityDate;

if (_CachedActivityFilter.IsActivityDateSetToToday)
{
    dateToSearchFor = DateTime.Now.Date;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有明显的问题。`ActivityFilter`对象做要求它做的事情——它存储应用程序需要知道什么活动符合用户标准的信息。

但是，如果`ActivityFilter`对象的角色是用户选择标准的保管者，那么，它不应该也知道用户要求的日期吗？这可能是马丁在这里提出的论点。从某种意义上说，我们没有把`ActivityFilter`这个角色发展得特别好。

我们可以通过将活动日期业务逻辑推送到`ActivityFilter`对象本身来解决这个问题，而不是将该信息暴露给设置和检索日期的代码。

首先，让我们私有化`IsActivityDateSetToToday`。让我们也创建一个新的私有活动日期属性`activityDate`。然后，让我们扩展公共`ActivityDate`来处理一个值被设置时的“今天”逻辑:

```
public class ActivityFilter
{
    private DateTime activityDate;
    private bool isActivityDateSetToToday;

    public DateTime ActivityDate 
    { 
        get
        {
            // TODO
        }
        set
        {
            // Set the activity date to the passed in value
            activityDate = value.Date;

            // Note whether the date set is 'today'
            isActivityDateSetToToday = (DateTime.Now.Date == value.Date);
        }
    }

    // Hiding the other properties in this post for clarity
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这样做，我们不再公开“今天”逻辑中涉及的属性。这项工作被归入`ActivityFilter`类本身。如果设定的日期恰好是今天，班级会在内部记录下来。

现在，让我们继续讨论`ActivityDate`公共`get`方法。在这里，我们可以检查`isActivityDateSetToToday`的值。如果是真的，那么`ActivityDate`今天就回来了。否则，它将返回最后存储的活动日期:

```
public class ActivityFilter
{
    private DateTime activityDate;
    private bool isActivityDateSetToToday;

    public DateTime ActivityDate
    {
        get
        {
            // If last set date was that day, return 'today'
            if (isActivityDateSetToToday)
            {
                return DateTime.Now.Date;
            }

            return activityDate;
        }
        set
        {
            var today = DateTime.Now.Date;
            var setDate = value.Date;

            // Note if the date set is 'today'
            isActivityDateSetToToday = (today == setDate);
            activityDate = value;
        }
    }

    /// Hiding the other properties in this post for clarity
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们倒回并浏览一下重新焕发生机的`ActivityDate`酒店。

当属性被设置时，`ActivityFilter`对象不仅将一个内部属性设置为那一天，而且还记录那一天是否实际上是今天。当属性被检索时，`ActivityFilter`对象返回它最后被设置的日期，*，除非*它已经被设置为那一天。在这种情况下，它将返回“今天”是什么。

但是，对抗这种贫血会使代码库明显变得更好吗？当我们重构设置和获取活动日期的原始代码时，这种封装的美妙之处就实现了。首先，原来处理额外逻辑的代码现在只需要设置活动日期:

```
public void onActivityDateChanged(DateTime selectedDate)
{
    _CachedActivityFilter.ActivityDate = selectedDate;
} 
```

Enter fullscreen mode Exit fullscreen mode

类似地，检索日期然后查询正确活动的代码只需检索值`_CachedActivityFilter.ActivityDate`。

我们现在已经将活动日期逻辑隐藏到拥有该逻辑周围数据的对象中。尽管以前设置日期的代码*和检索日期的代码*都需要了解“今天”的细微逻辑，但现在它们不需要了。他们也不再承担多重责任。

这些对象现在负责它们理应拥有的进程。最后，我们通过更恰当地发展我们的角色，围绕我们的代码构建了一个更好的故事。*