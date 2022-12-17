# 反转 AngularJS 滤镜的极性

> 原文：<https://dev.to/adamkdean/reverse-the-polarity-of-angularjs-filters-2ngc>

AngularJS 允许您使用字符串、对象和函数作为过滤器对象。您可以使用它们来过滤某些文本，如姓名列表中的姓名或电话号码列表中的号码。但是当你想过滤掉那些结果时会发生什么呢？

我们将假设我们有一个开放和关闭的支持票证的大列表，以及一个复选框，我们将使用它来切换过滤器。当它打开时，我们希望过滤掉关闭的票证。当它关闭时，我们希望看到所有的门票。

```
<input type="checkbox" 
    ng-true-value="closed" 
    ng-false-value="" 
    ng-model="hideClosedTickets"> 
Hide closed tickets 
```

Enter fullscreen mode Exit fullscreen mode

`ng-true-value`和`ng-false-value`有意思。它们允许你覆盖复选框的真/假值，在选中时将`hideClosedTickets`设置为`closed`，而不仅仅是`true`。

现在让我们使用`ng-repeat`指令来列出我们的票。它看起来像这样:

```
<ul>
    <li ng-repeat="ticket in tickets">
        # {{ticket.id}} - {{ticket.title}} - {{ticket.status}}
    </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这将给出我们的假想输出:

```
# 1 - Example ticket - open  
# 2 - My computer is broken - closed 
# 3 - jQuery fails to load - open  
# 4 - Somebody stole my drink - closed 
# 5 - Internet isn't working - open 
```

Enter fullscreen mode Exit fullscreen mode

为了过滤这些，我们将利用之前设置的`ng-repeat`的`filter`函数和范围变量`hideClosedTickets`。我们将传入一个对象，这样我们就可以只匹配票证状态。

我们*能不能*只过滤开放票的票，就像这样:

```
<li ng-repeat="ticket in tickets | filter: {status: 'open'}"> 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们有其他的票状态呢，比如`blocked`、`awaiting reply`或者`resolved`等等？

为此，我们需要反转过滤器的极性，因此我们匹配文本。我们可以通过在过滤器前面加上逻辑非操作符，强大的`!` :
来做到这一点

```
<li ng-repeat="ticket in tickets | filter: {status: '!' + hideClosedTickets}"> 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的问题是，如果`hideClosedTickets`是一个空字符串，它不会显示任何内容。我们首先检查`hideClosedTickets`是否被设置，然后匹配它:

```
<li ng-repeat="ticket in tickets | filter: {status: hideClosedTickets && '!' + hideClosedTickets}"> 
```

Enter fullscreen mode Exit fullscreen mode

当复选框被选中时，`hideClosedTickets`等于`closed`，我们将得到以下输出:

```
# 1 - Example ticket - open  
# 3 - jQuery fails to load - open  
# 5 - Internet isn't working - open 
```

Enter fullscreen mode Exit fullscreen mode

当复选框未选中时，`hideClosedTickets`是一个空字符串，因此我们得到所有内容:

```
# 1 - Example ticket - open  
# 2 - My computer is broken - closed 
# 3 - jQuery fails to load - open  
# 4 - Somebody stole my drink - closed 
# 5 - Internet isn't working - open 
```

Enter fullscreen mode Exit fullscreen mode

这是一个巧妙的小技巧，但一开始并不总是很明显。

如果你有更好的方法，请在评论中发表你的解决方案！

# 更新

刚刚有人向我指出，将`!`加到`ng-true-value`上也能达到同样的效果，所以:

```
<input type="checkbox" 
    ng-true-value="!closed" 
    ng-false-value="" 
    ng-model="hideClosedTickets"> 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
<li ng-repeat="ticket in tickets | filter: {status: hideClosedTickets}"> 
```

Enter fullscreen mode Exit fullscreen mode

~ ~我不确定为什么我没有早点想到这一点，我确定是我先尝试的，但也许这只是漫长的一天。~~

更新:我现在想起为什么我没有这样做。我想在其他地方使用这个值(“closed”)，这里我是 ng-show 而不是 ng-repeat filter。两种方法都有效，选择最适合你的！