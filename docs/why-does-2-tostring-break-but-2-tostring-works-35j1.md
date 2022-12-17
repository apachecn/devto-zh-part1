# 为什么` 2.toString()'会断开而` 2..toString()`作品？

> 原文：<https://dev.to/gnclmorais/why-does-2-tostring-break-but-2-tostring-works-35j1>

```
> 2.toString()
Uncaught SyntaxError: Invalid or unexpected token
> 2..toString()
"2" 
```

Enter fullscreen mode Exit fullscreen mode

`2.toString()`在 JavaScript 中不起作用而`2..toString()`起作用的原因是我一直感到困惑但从未弄清楚的……直到现在，当我发现自己有一些空闲时间时，这个问题又回到了我的脑海中。

对这种古怪行为的解释似乎与 JavaScript 解析器有关，它以一种**贪婪**的方式运行，即每次都试图匹配最长的有效操作符。在这种情况下，当它一个字符接一个字符地分析和评估时，当它读取数字`2`时，它期望分析一个数字，所以`2`是有效的。下一个字符，`.`，在数字中也有效(*十进制*数字，但仍然有效)。现在，下面的字符`t`在数字中无效，因此抛出一个错误:

`Uncaught SyntaxError: Invalid or unexpected token`。

在第二种情况下，`2..toString()`，它以同样的方式处理一切，但是，当它遇到第二个点时，它知道它不可能是一个数字，因为它之前找到了一个点，即小数点分隔符。所以它目前拥有的数字(`2.`)被转换成一个`Number` ( `2.0`，也就是`2`)，然后`toString`被调用，最后返回`"2"`。允许对一个数字进行`toString()`调用的一个简单方法是用括号将该数字括起来，清楚地封装数字求值:`(2).toString()`。

重要的是，`2..toString()`和`2.0.toString()` :
是一样的

```
> 2..toString()
"2"
> 2.0.toString()
"2" 
```

Enter fullscreen mode Exit fullscreen mode

### 资源

*   [JavaScript 中的双点语法](https://stackoverflow.com/a/24891388/590525)
*   [为什么“a + + b”可以，而“a++b”不行？](https://stackoverflow.com/a/7491960/590525)