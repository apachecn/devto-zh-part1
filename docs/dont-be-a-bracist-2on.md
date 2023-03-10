# 不要做一个支持者

> 原文：<https://dev.to/johannesvollmer/dont-be-a-bracist-2on>

触发警告:这篇文章假设应该避免种族歧视。

尽管我是一名学生，但我已经知道挖掘别人的作品是多么耗时。但是有一个特殊的习惯可以让阅读代码不那么尴尬，我们都知道:适当的格式。

有许多关于如何格式化代码的在线指南。大多数 IDE 甚至有一个漂亮的工具可以自动格式化你的源文件。但是有一个微小的不一致性，许多工具和指南选择忽略它。

## 普通括号缩进

这篇文章是关于组参数的 parantheses 和组语句的(花括号)的格式。JS、Java、C++、Rust、C 和数百种其他语言都以类似的方式使用它们。我们来看看 Java 中大括号通常是如何缩进的:

```
// inside KittenFactory
public List<Kitten> getKittensByAge(int minAge, int maxAge, boolean useGloves){
    final Filter ageFilter = new AgeFilter(minAge, maxAge);
    if (useGloves) {
        ageFilter.useHands();
        if (ageFilter.hasAnyGloves()) {
            ageFilter.removeWatch();
            ageFilter.enableGloves();
        }
    }
    return this.getKittens(ageFilter);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里开始看到一种模式:右括号缩进到与它们的父方法头或 if-keyword 相同的级别。大括号内的内容比它们的父关键字和右大括号多缩进一级。但是现在，让我用另一种缩进方式向你抛出同样的代码(对不起，手机用户):

```
public List<Kitten> getKittensByAge(int minAge, int maxAge, boolean useGloves){
    final Filter ageFilter = new AgeFilter(minAge, maxAge);
    if (useGloves) {
        ageFilter.useHands();
        if (ageFilter.hasAnyGloves()) {
            ageFilter.removeWatch();
            ageFilter.enableGloves();}}
    return this.getKittens(ageFilter);} 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在考虑写一篇关于丢失右括号的评论，请再看一遍。就在那里。我希望你喜欢这种困惑的感觉，因为还有更多:

```
public List<Kitten> getKittensByAge(int minAge, int maxAge, boolean useGloves){
    final Filter ageFilter = new AgeFilter(minAge, maxAge);
    if (useGloves) {ageFilter.useHands();
                    if (ageFilter.hasAnyGloves()) {ageFilter.removeWatch();
                                                   ageFilter.enableGloves();}}
    return this.getKittens(ageFilter);} 
```

Enter fullscreen mode Exit fullscreen mode

## 常见的并列句缩进

我感觉到你的困惑和不尊重，但我能感觉到你内心深处一种奇怪的熟悉感。因为我们以前都见过，但不是用括号，而是用 Paran themes:

```
factory.getKittensByAge(0.2,
                        0.3,
                        user.glovesAreAvailableFor("Peter",
                                                   2017,
                                                   Location.of("Peter"))); 
```

Enter fullscreen mode Exit fullscreen mode

见鬼，这种格式甚至可以在[甲骨文的格式指南](http://www.oracle.com/technetwork/java/javase/documentation/codeconventions-136091.html)中找到(正式过时)。

哇哦。

## 替代缩进

这种缩进方式大大降低了可读性。重要的源代码越来越向右移动，远离所有其他代码。此外，你可以看到一堆结束括号拥抱在一起。想象一下，在这样一个函数调用中必须调试一个丢失的参数。乱七八糟。但是不要害怕！有*个*个备选方案！我们已经在使用它们了，但是是用大括号，而不是 parantheses。看一看:

```
factory.getKittensByAge(
    0.2,
    0.3,
    user.glovesAreAvailableFor(
        "Peter",
        2017,
        Location.of("Peter")
    )
); 
```

Enter fullscreen mode Exit fullscreen mode

是的，就这么简单。这种风格有一些显著的好处:换行符实际上将代码移回左侧，您可以很容易地看到，哪个右括号属于哪个函数调用。毕竟，这就是我们使用这种风格的花括号的原因。此外，减少一个特殊的格式规则似乎更容易。

同样的缩进风格也可以应用于 if 语句和函数参数定义:

```
if(
    this.factory.filterType.hasGloves()
    && this.user.canUseGloves() // '&&' is important, so place it left
){
    factory.enableGloves();
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public void setAllFieldsAtOnce(
    double fluffiness,
    String name,
    double minAge,
    double maxAge,
    // ...
){
    this.fluffiness = fluffiness * Double.MAX_VALUE;
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

所以，**请不要成为一个支持者**。像对待牙套一样尊重父母。他们活该！最后，它们都被用来分组元素。此外，您的代码将更具可读性。

我知道你可能无法改变你公司的编码风格，但是你至少可以和你的朋友和同事分享这个想法。

我希望你喜欢我在 dev.to 上的第一篇帖子！