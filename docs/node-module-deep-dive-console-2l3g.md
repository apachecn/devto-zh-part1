# 节点模块深入研究:控制台

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-console-2l3g>

你好啊。是的，又是一个星期一。这意味着我该去 good ol ' GitHub dot com 深入了解另一个节点模块了。如果你是新来的，在过去的几周里，我一直在浏览和阅读节点代码库。我的目标是更多地了解节点标准库中的情况，并学习一些新的代码模式。本周，我将进入`console`模块。在这一点上，我们需要休息一下，并给予`console`应有的感谢，它是有史以来最可靠的调试工具！对于不熟悉的人，`console`公开了一套打印标准输出和标准误差的方法。一般是这样用的。

```
> console.log("Just a standard message going to standard out.");
Just a standard message going to standard out.
undefined
> console.error("Error: Something terrible has happened and this is going to stderr.");
Error: Something terrible has happened and this is going to stderr.
undefined
> console.info("This is also informative and is going to standard out.");
This is also informative and is going to standard out.
undefined 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮，是吧？好吧！让我们深入研究代码。像往常一样，这里的是一个到`console`模块版本的永久链接，我将在这篇文章中通读。至此，我已经阅读了一些代码，并且学到了一些东西:从底层开始阅读这些节点模块很有帮助。本质上，这给了我一个机会来弄清楚模块所公开的 API 是什么，并更有效地阅读相关的模块。在`console`模块的情况下，输出如下所示。

```
module.exports = new Console(process.stdout, process.stderr);
module.exports.Console = Console; 
```

Enter fullscreen mode Exit fullscreen mode

`console`模块的默认导出是`Console`对象的一个实例，但是该模块也导出对象本身的定义，以便用户可以在他们的代码中实例化他们自己的实例。`Console`对象在其原型上定义了几个函数。我已经知道了一些功能，比如`log`和`debug`，但是还有一些对我来说是新的，比如`time`和`count`。API 公开的函数背后的关键支持函数是`write`函数，它有以下函数定义。

```
function write(ignoreErrors, stream, string, errorhandler, groupIndent) { 
```

Enter fullscreen mode Exit fullscreen mode

我通读了函数体，并找出了每个参数的作用。

*   `ignoreErrors`:该参数为布尔值，定义是否忽略写入输出流时出现的错误。注意，默认情况下，这被设置为`true`。
*   `stream`:该参数定义了函数应该写入信息的流对象。
*   `string`:该参数定义了应该隐藏的字符串。
*   `errorhandler`:在写入流时遇到错误时执行的回调。
*   `groupIndent`:该参数定义每一新行缩进多少。例如，这在打印堆栈跟踪时特别有用，因为跟踪通常是缩进的。

我发现确定每个参数的作用特别有用，因为这使得阅读函数体更加容易。前几行检查`string`是否需要在每个新行后缩进，并检查`string`中是否有新行。如果在`string`中定义了`groupIndent`和换行符，换行符将被替换为适当的缩进。

```
if (groupIndent.length !== 0) {
  if (string.indexOf('\n') !== -1) {
    string = string.replace(/\n/g, `\n${groupIndent}`);
  }
  string = groupIndent + string;
}
string += '\n'; 
```

Enter fullscreen mode Exit fullscreen mode

代码库的下一部分处理上述的`ignoreErrors`参数。如果`ignoreErrors`为真，则字符串直接发送到流，不做任何错误处理。

```
if (!ignoreErrors) return stream.write(string); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，如果我们*想要处理错误，函数会执行一个 try-catch 子句。* 

```
try {
  // Add and later remove a noop error handler to catch synchronous errors.
  stream.once('error', noop);

  stream.write(string, errorhandler);
} catch (e) {
  // console is a debugging utility, so it swallowing errors is not desirable
  // even in edge cases such as low stack space.
  if (e.message === 'Maximum call stack size exceeded')
    throw e;
  // Sorry, there’s no proper way to pass along the error here.
} finally {
  stream.removeListener('error', noop);
} 
```

Enter fullscreen mode Exit fullscreen mode

我发现`stream.once('error', noop);`的陈述相当有趣，并决定做一些挖掘来弄清楚它到底是关于什么的。我最终找到了[这个拉动请求](https://github.com/nodejs/node/pull/9744)和[对应的问题](https://github.com/nodejs/node/issues/831)。添加该语句似乎是为了处理标准输出和标准错误不可用的情况。函数应该无声地失败，而不是抛出错误。然而，如果流开始写入后出现错误，该函数应该使用`errorhandler`来处理错误。

控制台 API 公开的大多数函数都利用了`write`函数。例如，经常使用的`log`函数是这样的。

```
Console.prototype.log = function log(...args) {
  write(this._ignoreErrors,
        this._stdout,
        util.format.apply(null, args),
        this._stdoutErrorHandler,
        this[kGroupIndent]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

而`warn`函数看起来有点像这样。

```
Console.prototype.warn = function warn(...args) {
  write(this._ignoreErrors,
        this._stderr,
        util.format.apply(null, args),
        this._stderrErrorHandler,
        this[kGroupIndent]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

控制台 API 中有一些对我来说是新的功能。例如，`time`和`timeEnd`函数用于测量代码库中两点之间经过的时间。例如，我们可以测试两个语句的执行间隔时间，如下所示。

```
> console.time("testing-time");
undefined
> for (var i = 0; i console.timeEnd("testing-time");
testing-time: 42570.609ms 
```

Enter fullscreen mode Exit fullscreen mode

`time`函数将一个键值对添加到`Console`对象的`_times`属性中，该属性定义了标签和由`process.hrtime`函数获取的当前时间戳之间的关系。

```
Console.prototype.time = function time(label = 'default') {
  // Coerces everything other than Symbol to a string
  label = `${label}`;
  this._times.set(label, process.hrtime());
}; 
```

Enter fullscreen mode Exit fullscreen mode

`timeEnd`检索由`time`函数存储的开始时间戳，并计算从那时起已经过去的时间量。

```
Console.prototype.timeEnd = function timeEnd(label = 'default') {
  // Coerces everything other than Symbol to a string
  label = `${label}`;
  const time = this._times.get(label);
  if (!time) {
    process.emitWarning(`No such label '${label}' for console.timeEnd()`);
    return;
  }
  const duration = process.hrtime(time);
  const ms = duration[0] * 1000 + duration[1] / 1e6;
  this.log('%s: %sms', label, ms.toFixed(3));
  this._times.delete(label);
}; 
```

Enter fullscreen mode Exit fullscreen mode

结合起来，`time`和`timeEnd`函数可以很好地对代码库的各个部分进行基准测试。

在阅读代码库时，另一组引起我注意的函数是`count`和`countReset`函数。这些函数用于维护给定特定`label`的`count`。

```
> console.count("red-fish");
red-fish: 1
undefined
> console.count("blue-fish");
blue-fish: 1
undefined
> console.count("red-fish");
red-fish: 2
undefined
> console.count("blue-fish");
blue-fish: 2
undefined
> console.count("red-fish");
red-fish: 3
undefined 
```

Enter fullscreen mode Exit fullscreen mode

`count`函数递增或重置为特定`label`定义的计数器，该计数器存储在控制台对象的`kCounts`属性中。

```
Console.prototype.count = function count(label = 'default') {
  // Ensures that label is a string, and only things that can be
  // coerced to strings. e.g. Symbol is not allowed
  label = `${label}`;
  const counts = this[kCounts];
  let count = counts.get(label);
  if (count === undefined)
    count = 1;
  else
    count++;
  counts.set(label, count);
  this.log(`${label}: ${count}`);
}; 
```

Enter fullscreen mode Exit fullscreen mode

并且`resetCount`函数重置特定标签的计数。

```
Console.prototype.countReset = function countReset(label = 'default') {
  const counts = this[kCounts];
  counts.delete(`${label}`);
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`countReset`函数的上方写有一个有趣的注释。

```
// Not yet defined by the https://console.spec.whatwg.org, but
// proposed to be added and currently implemented by Edge. Having
// the ability to reset counters is important to help prevent
// the counter from being a memory leak. 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，控制台 API 的[规范没有明确定义一个函数来重置标签的计数。考虑到标准为与`time`函数相关联的`timeEnd`函数定义了规范，我认为这非常有趣。无论如何，这个标准是一个生活标准，所以有足够的时间来增加它。](https://console.spec.whatwg.org)

就这样吧！`Console`对象没有其他一些要分析的函数复杂，但是我在阅读代码时确实发现了一些新的用途。

如果您对上述内容有任何问题或评论，请随时[问我问题](https://blog.safia.rocks/ask)或[在 Twitter 上联系我](https://twitter.com/captainsafia)。