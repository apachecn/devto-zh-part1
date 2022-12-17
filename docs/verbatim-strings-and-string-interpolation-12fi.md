# 逐字字符串和字符串插值

> 原文：<https://dev.to/simonreynolds/verbatim-strings-and-string-interpolation-12fi>

[![Verbatim strings and string interpolation](img/85f48bccb8483473e77fcd754905c1de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--shEdh7vU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1460036521480-ff49c08c2781%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在 C#中，总是有一些不同的方法来处理文本字符串，根据我们的需要，我们可以使用下面的任何一种方法；

```
string normalString = "This is a string, to include a line break we need to escape it like this \n so it doesn't terminate the string";

string verbatimString = @"This is a verbatim string where we can type enter
and the string contains the line break"; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想在字符串中包含变量，我们还有几个选择；

```
int x = 5;

string concatenation = "We can do this " + x " to include the variable";

string format = string.Format("We can do this {0} to include the variable", x);

string interpolate = $"We can do this {x} to include the variable"; 
```

Enter fullscreen mode Exit fullscreen mode

这里两个有趣的是逐字的和插值的字符串。如果我们需要构建一个包含变量的多行字符串，也许从一个模板中我们可以像这样组合它们。

```
var name = "Simon";
var templateName = "How to interpolate a verbatim string";
var now = DateTime.UtcNow;

var output = $@"Hi {name},  This template is a demo of {templateName}.  It was ran at {now.ToString("o")}"; 
```

Enter fullscreen mode Exit fullscreen mode

上述演示的输出是

> 这个模板演示了如何插入一个逐字的字符串。
> 
> 它运行于 2017-04-11t 14:46:09:08 z

对于填充大型多行模板，将字符串插值与逐字字符串格式化相结合的能力非常有用