# 更改文本选择颜色

> 原文：<https://dev.to/adamkdean/changing-text-selection-colours-3g0c>

给你的网站添加一点润色的小技巧是改变文本选择的颜色。Stockapps 博客有一个漂亮的橙色，当然， [Si Digital](http://sidigital.co/) 有他们众所周知的紫红色突出。

您可以使用伪元素`::selection`轻松实现这一点。

```
::selection {
    color: white;
    background: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，Gecko 需要前缀，所以您还必须加上`::moz-selection`:

```
::moz-selection {
    color: white;
    background: red;
}
::selection {
    color: white;
    background: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，这是实验性的，已经从规范中删除了，所以我想你还不应该依赖它来做任何“重要”的事情。希望它会坚持下来！