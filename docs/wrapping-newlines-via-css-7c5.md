# 通过 CSS 换行

> 原文：<https://dev.to/teroauralinna/wrapping-newlines-via-css-7c5>

这是一个简单的技巧，告诉你如何换行而不把换行转换成`<br>`标签。如果您有一个没有格式的文本，并且唯一需要的是文本段落换行，这是非常方便的。您不必考虑 HTML 净化或转换。

```
.wrapped-text {
    white-space: pre-line;
} 
```

Enter fullscreen mode Exit fullscreen mode

在渲染之前，只需从文本的开头和结尾修剪换行符即可。值`pre-line`不会在文本中间呈现空白。

* * *

这篇博文最初发表于[aural linna . blog](https://auralinna.blog/post/2017/wrapping-newlines-via-css)