# 如何让 CSS 第一个孩子工作

> 原文：<https://dev.to/moreonfew/how-to-make-css-first-child-work-2j24>

随着 CSS3 的出现，引入了许多新的选择器。这些新的选择者对我们帮助很大。`first-child`选择器就是这样一种选择器。使用`first-child`选择器，您可以将元素作为其父元素的第一个子元素。这意味着如果你想定位一个无序列表的第一个`<li>`或`<ul>`，你可以使用`:first-child`选择器来定位第一个“li”元素。它的代码如下所示:

```
ul li:first-child{
  color:red;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码将针对第一个`<ul>`的`<li>`，并将其颜色改为红色。这是一个非常简单而有用的属性。但是，请记住，只有当您的目标元素是父元素的第一个子元素时，`:first-child`才会起作用。这意味着**如果在你的目标元素之前有任何其他的兄弟元素，那么第一个孩子的属性将不会像预期的那样工作**。让我们考虑下面的例子:

```
<div>
 <h1>Some Heading</h1>
 <p>My first paragraph</p>
 <p>My second paragraph</p>
</div>

<style>
/* This would NOT work as expected ! */

div p:first-child{
 color:red;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，您可以注意到`<div>`中的第一个`<p>`被作为目标。然而，这是行不通的，因为从技术上讲，`<p>`不是`<div>`的第一个孩子。`<div>`的第一个孩子是`<h1>`标签。

## 那么，如何让:第一个孩子在 CSS 中工作呢？

如果在作为目标的“第一个子元素”之前有其他兄弟元素，那么应该使用 **`first-of-type`** 选择器。第一类型选择器的目标是“特定类型或标签”的元素，并且是其父元素中该类型的第一个元素。为了清楚地理解这一点，请看看下面的例子:

```
<div>
 <h1>Some Heading</h1>
 <p>My first paragraph</p>
 <p>My second paragraph</p>
</div>

<style>
/* This would work ! */

div p:first-of-type{
 color:red;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，`<div>`中的第一段文字颜色为红色。这里的关键是理解`<p>`或段落在技术上不是第一个孩子，并且当你想要定位段落或`<p>`时，你应该定位`<p>`，它是其父`<div>`的第一个孩子，并且是“确定”类型或

类型”。

希望你理解这个概念，并喜欢这篇文章。请在[脸书](https://www.facebook.com/moreonfew)关注我们，在[推特](https://twitter.com/moreonfew)关注我们。

帖子[如何让 CSS 第一个孩子工作](https://www.moreonfew.com/make-css-first-child-work/)最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。