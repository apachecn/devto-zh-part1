# CSS 仅部分宽度边框

> 原文：<https://dev.to/designfrontier/css-only-partial-width-borders-4lcf>

我最近实现的一个设计需要部分宽度的下划线和部分高度的边框作为两个元素之间的分隔符。

但是我不想在 DOM 中添加任何额外的元素。因为，嗯...如果可以避免，我为什么要添加非语义元素呢？

我想到的解决方案真的很简单。它使用一个`:before`来处理边界的定位和大小调整。

```
.partial-border: {
    display: inline-block;
    position: relative;
  }
  .partial-border:before {
    content: ’’;
    position: absolute;
    bottom: 0;
    width: 80%;
    left: 10%; /*this centers it based on the above width*/
    border-bottom: 1px solid lightcoral;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这就很管用了...没有额外的标记和部分宽度的边界。

父元素上的`position: relative`允许其宽度决定绝对定位伪元素的宽度。除此之外，这是不言自明的。

如果你想自己玩这个[，它在 codepen](http://codepen.io/designfrontier/pen/jAGEpv) 上。尽情享受吧！