# TIL:匹配正则表达式中的 unicode 字母

> 原文：<https://dev.to/svedi/til-match-unicode-letters-in-regex-55ab>

注意:这个帖子最初发布在[sven-dittmer.netlify.com](https://sven-dittmer.netlify.com/2017/12/20/match-unicode-letters-in-regex.html)。

今天我在[exercisem . io](http://exercism.io)上面临一个有趣的挑战。
对我来说，最有趣的部分是发现一个句子是否被大喊。如果一个句子的所有单词都是大写的，那么这个句子被认为是大喊大叫。

你可以在这里找到整个挑战:[Bob in elixir-exercisem . io](http://exercism.io/exercises/elixir/bob/readme)

下面是我的第一次尝试:

```
String.upcase(input) == input 
```

Enter fullscreen mode Exit fullscreen mode

这种方法通过了一些测试，但是对于不包含任何单词的句子，比如“ *1，2，3* ”，这种方法就失败了。既然没有大写单词，那就不应该算吼了。
下一次尝试:

```
input =~ ~r/\w/u && String.upcase(input) == input 
```

Enter fullscreen mode Exit fullscreen mode

使用这个正则表达式，我确保这个单词至少包含一个字母。因为我还在检查每个字母都是大写的，所以应该可以了，对吗？不完全是。

这个练习的最后一个测试是关于用俄语喊的一个句子。(链接:
[鲍勃在药剂测试套件](http://exercism.io/exercises/elixir/bob/test-suite#L81) )
原来`\w`只匹配拉丁字母。相当于`[a-zA-Z]`。为了让正则表达式工作，我必须匹配任何 unicode 字母。稍微搜索了一下，我在这里找到了解决方案:[正则表达式. info](https://www.regular-expressions.info/unicode.html) 。有了 <abbr title="Perl Compatible Regular Expressions">PCRE</abbr> ，我们可以用`\p{<some_unicode_category>}`匹配不同类别的 unicode。对于字母，你可以用`\p{Letter}`或者它的简称`\p{L}`。下面是通过最后一次测试的最终代码:

```
input =~ ~r/[\p{L}]/u && String.upcase(input) == input 
```

Enter fullscreen mode Exit fullscreen mode

这是我在 dev.to 上的第一篇帖子。非常感谢反馈！