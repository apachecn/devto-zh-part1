# Javascript 中 curry 的不那么可怕的解释

> 原文：<https://dev.to/loujaybee/the-not-so-ccary-explanation-of-currying-in-javascript>

如果说我最讨厌开发人员社区，那就是技术中的许多概念都用不必要的复杂性简单地解释了。我发现“奉承”就是这些想法中的一个。奉承是最简单的形式，相当简单。让我们来看看吧。

为了说明如何以及为什么使用奉承，让我们看一个例子。

# 阿谀奉承简单解释道

在重构代码时，我发现了一个使用 Currying 的好机会。

我看到的代码看起来像这个简化的例子:

```
var contains = function(one, two){
  // Computation
};

var one = {};

contains(one, "Some string")
contains(one, "A different string");
contains(one, "Another string"); 
```

Enter fullscreen mode Exit fullscreen mode

# 闻着码

当编写/审查/重构代码时，我们经常寻找所谓的“代码气味”。代码味道类似于反模式。它会让你停下脚步，让你说“嗯”。

这段代码给了我一股强烈的代码味道。

为什么？代码`contains(one`的重复。

# 潜在的升级

我立即开始寻找缩短这段代码的方法。

一种可能的改进是将`one`值硬编码到`contains`函数中。像这样:

```
var contains = function(two){
  var one = value;

  // Computation
};

contains("Some string")
contains("A different string");
contains("Another string"); 
```

Enter fullscreen mode Exit fullscreen mode

这个*确实*停止了总是传递第一个值的重复。但是它减少了对`contains`代码的重用。

它将它与一个值的实现结合起来。

那么我们如何在不产生紧耦合的情况下减少这种重复呢？

# 输入奉承话

为了防止第一个变量的重复，我们可以利用 javascripts 的闭包功能。

```
var contains = function(one){
  return function(two) {
    // Computation
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
var boundContains = contains(one);

boundContains("Some string")

boundContains("A different string");

boundContains("Another string"); 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么事？

*   用变量`one`调用一次`contains`函数。
*   Javascript 闭包然后保存对闭包中一个变量的引用
*   bind contains 方法现在等同于从`contains`内部返回的函数
*   我们现在可以使用 bindContains 方法，而不必总是传递一个参数。
*   如果一个值改变了，现在我们只有一个引用需要更新。

# 结论

这是一个简化的例子。但是希望您能看到如何使用 curry 来干燥我们的代码。

也没必要这么深奥。

你甚至可以通过创建一个`curry`方法来进一步阿谀奉承，但这是以后的课程！