# KnockoutJS 简单截断文本绑定处理程序

> 原文：<https://dev.to/forforeach/knockoutjs-simple-truncate-text-binding-handler-4jmg>

大家好，

在我目前的项目中，我需要截断一些标题中的文字。因为我们使用 KnockoutJS，所以我决定编写自定义绑定处理程序。于是我照做了。

自定义绑定处理程序是一个很好的解决方案，它让您能够控制 observables 如何与 DOM 元素交互。此外，您封装了您的自定义行为，因此它们变得可重用。当你使用第三方窗口小部件时，当可观察值更新时，它非常有用。

这里是我的截断绑定处理器的一个例子:

```
ko.bindingHandlers.truncatedText = {
 update: function (element, valueAccessor, allBindingsAccessor) {
 var originalText = ko.utils.unwrapObservable(valueAccessor()),
 // 10 is a default maximum length
 length = ko.utils.unwrapObservable(allBindingsAccessor().maxTextLength) || 20,
 truncatedText = originalText.length &gt; length ? originalText.substring(0, length) + “…” : originalText;
 // updating text binding handler to show truncatedText
 ko.bindingHandlers.text.update(element, function () {
 return truncatedText; 
 });
 }
}; 
```

你可以试试这里的

使用这个绑定处理程序和一般的绑定处理程序。

享受吧。

* * *