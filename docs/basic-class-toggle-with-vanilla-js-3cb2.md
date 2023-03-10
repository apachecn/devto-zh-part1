# 普通 JS 的基本类切换

> 原文：<https://dev.to/magnificode/basic-class-toggle-with-vanilla-js-3cb2>

我今年的[目标之一是开始多掌握一点普通的 JS。当我在学校的时候，Javascript 和 jQuery 几乎是齐头并进的。除了基础知识之外，很少有人讲授普通的 JS，而是推崇 jQuery 的荣耀。这是可以理解的，句法糖是相当不可抗拒的。](/update/2015/12/29/2015.html)

我将讲述一个编写 Javascript 时非常普遍的愿望，类切换。

让我们来看看我们通常如何用 jQuery 做到这一点。但是要小心，你会看到下一个片段，并且想“为什么我们要用普通的 JS 来做这个呢？!"。我知道...但是看看[所有的](http://alistapart.com/blog/post/choosing-vanilla-javascript) [这些](http://gomakethings.com/ditching-jquery-for-vanilla-js/) [的帖子](https://teamtreehouse.com/community/pure-javascript-vs-jquery-2)鼓吹香草 JS 的性能优势。我不会在这里深入讨论这个问题，这纯粹是为了在你决定做出改变时作为一个参考。

好的，那么基本的 jQuery 类切换函数应该是这样的:

```
$('.toggle-me').click( function() {
  $(this).toggleClass('active');
}); 
```

相当直接。我们会一点一点地把它拆开。对于 vanilla JS，我们需要做的第一件事是实际定位元素。现在，我相信你知道总是有利用`getElementByID`的选择。但在这种情况下，我们试图针对一个类。实际上，如果可以的话，我们可能希望避免使用 id，因为这种特异性可能会导致以后出现问题。

## 输入查询选择器

MDN [声明](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelector)查询选择器:

> 返回文档中匹配指定选择器组的第一个元素。

这非常适合我们的情况，我们目前在页面上只有一个元素的类名是`.toggle-me`。如果页面上有多个元素具有相同的类名，您需要查看 [querySelectorAll](https://developer.mozilla.org/en-US/docs/Web/API/Document/querySelectorAll) ，我将在另一篇博客文章中对此进行深入探讨。

因此，让我们将类名设置为一个变量，以便于引用。

```
var el = document.querySelector('.toggle-me'); 
```

很好，现在我们需要某种方法来检测元素被点击的时间。如果你有幸看到人们使用 inline `onclick`函数的日子，那么你可能已经猜到我们将走向何方。

## 点击

属性允许我们使用一个函数表达式，或者一个定义在它内部的命名函数。在这个例子中，我将使用一个函数表达式来保持简洁。

```
var el = document.querySelector('.toggle-me');

el.onclick = function() {
  console.log('clicked!');
} 
```

太棒了。如果你检查你的控制台，你应该看到我们的“点击！”伐木。

## [类列表](#classlist)

我们需要的最后一点代码将完成类的实际切换。如果你做了一些搜索，你会注意到 Javascript 中没有一个独立的切换属性。幸运的是， [classList](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList) 前来救援。

属性本身是不言自明的。它输出传递的元素的类属性的集合。如果我们要在我们的`.toggle-me`类上运行它:

```
var el = document.querySelector('.toggle-me');

el.onclick = function() {
  console.log(el.classList);
} 
```

您会注意到控制台中的输出为我们提供了一个 DOMTokenList 对象。展开该对象将显示该对象中的第一个值是“toggle-me”。

有了这个对象，我们现在可以使用与`classList`属性相关联的`toggle()`方法。与 jQuery 非常相似，除了`toggle()`，`classList`属性让我们可以访问`add()`，`remove()`和`item()`方法。出于我们的目的，我们将坚持使用`toggle()`。

## 结果

不幸的是，对于普通 JS 来说,`$(this)`属性并不存在。然而，由于我们缓存了我们正在操作的元素，我们需要做的就是将`classList`属性附加到我们的元素，然后链接我们的`toggle()`方法，像这样传递我们的活动类:

```
var el = document.querySelector('.toggle-me');

el.onclick = function() {
  el.classList.toggle('active');
} 
```

瞧！现在，您已经利用纯 Javascript 在元素上完成了一个简单的类切换。为节省了额外的 HTTP 请求并消除了对 jQuery 的依赖而沾沾自喜。