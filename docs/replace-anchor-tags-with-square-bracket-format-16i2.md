# 用方括号格式替换定位标记

> 原文：<https://dev.to/adamkdean/replace-anchor-tags-with-square-bracket-format-16i2>

由于我不想深入讨论的原因，我发现自己需要用方括号格式的锚标记替换一批*锚标记，就像这样:*

```
// from
<a href="#">link</a>

// to
[a href="#"]link[/a] 
```

Enter fullscreen mode Exit fullscreen mode

显然，我们需要在这里使用正则表达式，为了将来的参考(主要是我自己)，下面是匹配和替换模式:

```
// match
<a .*?href=['""](.+?)['""].*?>(.+?)</a>

// replace
[a href="$1"]$2[/a] 
```

Enter fullscreen mode Exit fullscreen mode

不妨反过来说:

```
// match
\[a .*?href=['""](.+?)['""].*?\](.+?)\[/a\]

// replace
<a href="$1">$2</a> 
```

Enter fullscreen mode Exit fullscreen mode

如果你用 C#来做这个，就用这个方法:

```
string Regex.Replace(string input, string match, string replace) 
```

Enter fullscreen mode Exit fullscreen mode

好了，省去了我一年的繁琐工作！