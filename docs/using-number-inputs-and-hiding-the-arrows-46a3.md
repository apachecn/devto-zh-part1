# 使用数字输入，隐藏箭头

> 原文：<https://dev.to/adamkdean/using-number-inputs-and-hiding-the-arrows-46a3>

很长一段时间以来，我们一直生活在一些基本的输入类型中；文本、密码、选择和提交。但是在 HTML5 中，我们可以使用更多的输入类型。其中一些还没有被完全支持，但是如果你像我一样正在开发一个单一的浏览器，而这个单一的浏览器恰好是 Chrome，[你很幸运](http://caniuse.com/input-number)。

在发现 Angular 的`ng-pattern`很麻烦之后，我决定检查一下[数字输入类型](http://dev.w3.org/html5/markup/input.number.html)。客户端验证正是我想要的，但是讨厌的向上/向下按钮让它看起来很丑，并且占用了我已经狭窄的用户界面上的宝贵空间。

借助谷歌的力量，我找到了禁用这些丑陋按钮的方法。

```
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
    -webkit-appearance: none;
    margin: 0;        
} 
```

Enter fullscreen mode Exit fullscreen mode

我遇到的下一个问题是它只接受整数。不利于货币操作。

幸运的是，我们有一些可用的属性。其中一个是`step`，它“指定元素值的值粒度”。将`step`设置为`any`允许你选择任何你喜欢的粒度。

另外两个有用的属性是`min`和`max`，它们允许您设置一个定义的范围。

```
<input type="number" step="any" min="0" max="100"> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个真正有用的元素，希望我们能看到 HTML5 更多的内置功能。