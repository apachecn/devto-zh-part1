# 用 PHP 选择字符串中的一个字符

> 原文：<https://dev.to/scottrobertson/select-a-character-in-a-string-with-php-ehk>

我喜欢在 PHP 中寻找我以前不知道的小东西。它们通常不重要或不先进，但仍然非常有趣。以下是我最近发现的几个:

不使用 substr 等选择一个带有字符串的字符:

```
$string = '1234';
echo $string[1]; // 2 
```

Enter fullscreen mode Exit fullscreen mode

我发现的另一个是“__halt_compiler”，这是我没有真正使用过的一个，但仍然很有趣。它基本上停止了 PHP 编译器，这样你就可以从那时开始输出 HTML，而不需要使用更多的服务器资源。

```
__halt_compiler(); 
```

Enter fullscreen mode Exit fullscreen mode

毫无疑问，我会找到更多这样的东西，而且很可能我对它们都没有用处。