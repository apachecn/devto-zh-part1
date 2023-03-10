# 垂直居中 HTML 元素

> 原文：<https://dev.to/prestonjlamb/vertical-centering-html-elements-3ch4>

我不知道你们中有多少人以前尝试过垂直居中 HTML 元素，但这可能是一个大麻烦。一位同事曾经给过我一些提示和几个 CSS 类，我可以用它们来实现这个目标。不过，这真的不容易，让其他人知道发生了什么也很难。

这是我同事的建议:

```
.vertical-container:before {
    content: '';
    display: inline-block;
    vertical-align: middle;
    height: 100%;
}
.vertical-center {
    display: inline-block;
    vertical-align: middle;
} 
```

Enter fullscreen mode Exit fullscreen mode

这看起来并不复杂，但是您必须将`.vertical-container`类放在父元素上，而不是放在`.container`元素上。所以本质上看起来是这样的:

```
<div class="container">
    <div class="vertical-container">
        <div class="vertical-center">
            ...
        </div>
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

还有一些意想不到的副作用可能会突然出现，但并不总是如此。有时候，你想要垂直居中的元素最终显示在`.container`元素之后，解决这个问题的方法是将`.vertical-container`元素的`font-size`设置为 0。然后，您必须重置`.vertical-center`元素中各个元素的字体大小。这显然不太理想。

## Flexbox

今天我开始做 [@geddski](https://www.twitter.com/geddski) 的 [Flexbox 僵尸](https://flexboxzombies.com)课程。太棒了。我只完成了大约 10 分钟的课程，并了解到我可以垂直居中元素(没有副作用)，有三个样式属性:

```
.vertical-center {
    display: flex;
    flex-direction: column;
    justify-content: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。没有嵌套元素，没有`font-size`hack，什么都没有。只是一个简单的类，带有一些简单的 flexbox 属性，仅此而已。您也可以将`div`居中对齐，或者将其放在行的开头或结尾，以及列的顶部或底部。我知道还有很多东西需要学习，我很高兴能够理解并实施它。我已经可以看到使用 flexbox 制作一个响应式的列布局将比以前简单得多！

不管怎样，flexbox 看起来是合法的，并且将会使令人敬畏的布局更容易实现。去看看 [Flexbox 僵尸](https://flexboxzombies.com)，这样你也可以学习如何使用 Flexbox！