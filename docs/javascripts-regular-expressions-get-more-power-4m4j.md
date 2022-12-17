# JavaScripts 正则表达式功能更强大

> 原文：<https://dev.to/kayis/javascripts-regular-expressions-get-more-power-4m4j>

*[封面图片由迈克·莱温斯基在 Flicker 上拍摄，由我裁剪。](https://www.flickr.com/photos/ikewinski/)T3】*

正则表达式是大多数开发人员最喜欢的解决方案。有些人非常喜欢他们的正则表达式，他们甚至用它来解析根本没有正规语法的文本，比如 [HTML](https://stackoverflow.com/questions/1732348/regex-match-open-tags-except-xhtml-self-contained-tags#1732454)

无论如何，作为 JavaScript 一部分的`RegExp`类在特性方面有点欠缺，但是这在(希望)不久的将来会改变。

## `s`旗

正则表达式的常规用户非常了解`.`操作符。它匹配一个字符。遗憾的是，在 JavaScript 中，它不匹配像换行符或`\n`这样的字符。

使用`s`新的 s 标志改变了这种行为。

```
/^.$/s.test('\n') // == true 
```

Enter fullscreen mode Exit fullscreen mode

## 回望断言

通常，您希望匹配由特殊字符包围的文本，但不希望包含这些特殊字符。JavaScript 只允许查找那些跟在你需要的文本后面的。有了这个更新，你还可以查看你的文本之前的内容。

`(?=<PATTERN>)`为正前视
`(?!<PATTERN>)`为负前视
`(?<=<PATTERN>)`为正后视
`(?<!<PATTERN>)`为负后视

```
// Positive lookahead, matches text that precedes "XYZ" 
/[\w]*(?=XYZ)/

// Negative lookahead, matches text that isn't preceded with "XYZ"
/[\w]*(?!XYZ)/

// Positive lookbehind, matches text that follows "XYZ"
/(?<=XYZ)[\w]*/

// Negative lookbehind, matches thaxt that doesn't follow "XYZ"
/(?<!XYZ)[\w]*/ 
```

Enter fullscreen mode Exit fullscreen mode

## 命名捕获组

一个经常出现的问题是匹配文本的许多部分并在以后使用结果。正则表达式将很快允许您命名这些结果，因此它们更容易使用。

您可以使用`(?<<NAME>><PATTERN>)`创建一个具有与您的`<PATTERN>`相匹配的特定`<NAME>`的群组。它们稍后将被存储在匹配结果对象的`groups`属性中。

例如，匹配特定格式的日期。

```
const pattern = /(?<day>[\d]{2})\.(?<month>[\d]{2})\.(?<year>[\d]{4})/;

const {day, month, year} = patter.exec("30.04.2017").groups; 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

很多时候，我使用一些在线的[正则表达式创建器](https://regexr.com/)来构建相当复杂的模式，只是意识到 JavaScript 不支持它们。通常，这导致我将模式分开，并将它们与 JavaScript 逻辑混合在一起以使其工作。有了新的补充，这不再是一个常见的问题。