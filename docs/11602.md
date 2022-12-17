# 用 CSS 覆盖选择样式

> 原文：<https://dev.to/adamkdean/override-selection-style-with-css-4jkp>

你可能已经注意到了某些设计，比如[惊人的 SI 数字网站](http://sidigital.co/)，对于文本的选择有一个定制的风格。如果使用得当，例如与你的设计主色调相配，这能给人一种非常漂亮和优雅的感觉。

实现起来也非常简单。您可以更改前景`color`和`background`的颜色，如下所示:

```
::selection { color: white; background: #5AB54A; }
::-moz-selection { color: white; background: #5AB54A; } 
```

Enter fullscreen mode Exit fullscreen mode

然而，并不是所有的属性都可以使用`::selection`来设置。可以设置*的属性有`color`、`background`、`background-color`、`text-shadow`。虽然根据[这条](https://developer.mozilla.org/en-US/docs/Web/CSS/::selection)，`background-image`被忽略。设置背景使用图像也被忽略，不透明度似乎也被设置，无法更改。*

尽管如此，对于现代浏览器来说，这是一个非常好的特性，可以真正为您的设计添加点睛之笔。