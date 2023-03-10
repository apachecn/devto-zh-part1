# 节点模块深入研究:路径

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-path-51j0>

又是一个周一，又是一个节点模块演练！对于这个多部分系列的第二部分，我决定研究一下 Node 中的`path`模块。`path`模块让开发人员能够处理文件和目录路径。你可能用它来做一些事情，比如确定路径的底部或者连接多条路径。下面的代码片段展示了`path`模块是如何工作的。

```
> const path = require('path');
> path.basename('~/this/is/a/test/file.html');
'file.html'
> path.dirname('~/this/is/a/test/file.html');
'~/this/is/a/test'
> path.extname('~/this/is/a/test/file.html');
'.html'
> path.join('~', 'this', 'is', 'a', 'test');
'~/this/is/a/test' 
```

Enter fullscreen mode Exit fullscreen mode

如果您对探索`path`模块 API 的全部范围感兴趣，您可以查看模块的[文档。](https://nodejs.org/api/path.html)

好吧！现在是深入代码本身的时候了。像上次一样，[这里的](https://github.com/nodejs/node/blob/ff21851816f8512a1488265a878140d8aa67683a/lib/path.js)是一个链接，链接到我将在这篇文章中介绍的代码版本。我将包含嵌入的代码片段，但也会引用代码库的相关部分，并提供指向特定行的链接。

在开始深入研究代码本身之前，我快速浏览了一遍，以了解代码的常见模式。在很大程度上，这个模块中定义的许多函数迭代地计算不同的字符串操作。有许多 for 循环和条件句的 T4。另一件要注意的事情是 API 函数从‘path’模块中公开的方式。“路径”模块定义了两个对象`win32`和`posix`，它们存储了每个平台的函数定义。在最终的导出中，模块评估模块是从哪个平台被调用的，并导出与该平台匹配的函数。在我的代码阅读中，我特别关注为`posix`平台定义的函数，因为这是我最常开发和部署的平台。

```
if (process.platform === 'win32')
  module.exports = win32;
else
  module.exports = posix; 
```

Enter fullscreen mode Exit fullscreen mode

由于`path`模块 API 公开了相当多的函数，我将把重点放在一些我认为最流行的函数上。我研究的第一个函数是`join`函数。您可能已经在 Express 服务器的配置和基于电子的桌面应用程序中看到过这种情况。

```
join: function join() {
    if (arguments.length === 0)
      return '.'; 
```

Enter fullscreen mode Exit fullscreen mode

我发现这段代码有趣的第一点是它在 JavaScript 中使用了`arguments`对象。`argument`对象是一个基于列表的数据结构，对应于基于函数的参数。`join`方法将一列要连接的路径作为其输入。`join`没有为函数定义一个特殊的参数，而是依赖于`arguments`对象，该对象使用户能够为函数提供任意数量的参数。

```
 var joined;
    for (var i = 0; i 0) {
        if (joined === undefined)
          joined = arg;
        else
          joined += '/' + arg;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

该函数遍历每个参数，检查它是否是有效路径，并将其连接到一个名为`joined`的串联字符串。`assertPath`函数是一个简单的函数，它检查传递给`assertPath`函数的参数是否是一个字符串。

```
function assertPath(path) {
  if (typeof path !== 'string') {
    throw new errors.TypeError('ERR_INVALID_ARG_TYPE', 'path', 'string');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，该函数将连接的路径规范化并返回它。

```
 if (joined === undefined)
      return '.';
    return posix.normalize(joined); 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，这是一个非常简单的函数，是绝对属于标准库的代码类型的一个很好的例子。

我想研究的最后一个函数是`dirname`和`basename`函数。

```
dirname: function dirname(path) {
  assertPath(path);
  if (path.length === 0)
    return '.'; 
```

Enter fullscreen mode Exit fullscreen mode

该函数首先评估作为参数传入的`path`的有效性。

```
 var code = path.charCodeAt(0);
    var hasRoot = (code === 47/*/*/); 
```

Enter fullscreen mode Exit fullscreen mode

该函数检查所提供的路径中的第一个字符是否是一个`/`,这表明所提供的路径是一个从文件系统的根目录开始的绝对路径(因为我们在一个 POSIX 系统上)。这个检查稍后用于在最终字符串中保留根`/`的存在。

```
 if (end === -1)
      return hasRoot ? '/' : '.';
    if (hasRoot && end === 1)
      return '//'; 
```

Enter fullscreen mode Exit fullscreen mode

函数的主体是一个 for 循环，它通过提供的`path`向后迭代，寻找一个`/`字符。如果找到了，它就跳出 for 循环，把它的索引存储在一个名为`end`的变量中。稍后，它使用`end`在提供的路径中获取适当的子字符串。

```
 return path.slice(0, end); 
```

Enter fullscreen mode Exit fullscreen mode

所以从本质上来说，`dirname`函数的逻辑如下:在`path`中从结束到开始往回走，直到碰到一个`/`字符。如果是这样，这意味着您已经遍历了文件名中的所有字符，剩余的字符对应于目录名。

`basename`函数稍微复杂一些。它有两个参数:`path`，在其中查找基本名称的路径，以及可选的`ext`，从基本名称截断的扩展名。像往常一样，它首先评估传入参数的有效性。

```
basename: function basename(path, ext) {
    if (ext !== undefined && typeof ext !== 'string')
      throw new errors.TypeError('ERR_INVALID_ARG_TYPE', 'ext', 'string');
    assertPath(path); 
```

Enter fullscreen mode Exit fullscreen mode

如果用户提供了一个`ext`参数，那么与它如何迭代相关的逻辑有点复杂，所以我从查看在没有`ext`参数的情况下调用`basename`的逻辑开始。

```
 for (i = path.length - 1; i >= 0; --i) {
        if (path.charCodeAt(i) === 47/*/*/) {
          // If we reached a path separator that was not part of a set of path
          // separators at the end of the string, stop now
          if (!matchedSlash) {
            start = i + 1;
            break;
          }
        } else if (end === -1) {
          // We saw the first non-path separator, mark this as the end of our
          // path component
          matchedSlash = false;
          end = i + 1;
        }
      }

      if (end === -1)
        return '';
      return path.slice(start, end); 
```

Enter fullscreen mode Exit fullscreen mode

该函数向后遍历字符串(类似于`dirname`函数)，直到找到`/`字符的第一个实例。这一次，它使用该字符的索引作为路径上切片的*起点*。

用户确实提供了一个`ext`参数，这种情况的逻辑更加复杂，因为它必须跟踪字符串中的扩展名。

```
for (i = path.length - 1; i >= 0; --i) {
        const code = path.charCodeAt(i);
        if (code === 47/*/*/) {
          // If we reached a path separator that was not part of a set of path
          // separators at the end of the string, stop now
          if (!matchedSlash) {
            start = i + 1;
            break;
          }
        } else { 
```

Enter fullscreen mode Exit fullscreen mode

它向后遍历字符串，找到第一个`/`字符，并将其存储为切片的`start`点，然后退出循环。如果在它所在的位置没有找到一个`/`字符，它会交替地计算一些逻辑来确定扩展名在哪里结束，并将其存储为字符串的结尾。

```
 if (firstNonSlashEnd === -1) {
            // We saw the first non-path separator, remember this index in case
            // we need it if the extension ends up not matching
            matchedSlash = false;
            firstNonSlashEnd = i + 1;
          }
          if (extIdx >= 0) {
            // Try to match the explicit extension
            if (code === ext.charCodeAt(extIdx)) {
              if (--extIdx === -1) {
                // We matched the extension, so mark this as the end of our path
                // component
                end = i;
              }
            } else {
              // Extension does not match, so our result is the entire path
              // component
              extIdx = -1;
              end = firstNonSlashEnd;
            } 
```

Enter fullscreen mode Exit fullscreen mode

就这样吧！由`path`模块公开的大多数函数都是简单的字符串操作(和许多边缘情况处理)。从快速阅读代码来看，与基于`win32`的文件系统相关的函数肯定要复杂得多，尤其是处理驱动器(`C:`和`D:`)和转义(有趣！).

如果您有任何意见或问题，请随时[问我问题](https://blog.safia.rocks/ask)或在 Twitter 上联系我。