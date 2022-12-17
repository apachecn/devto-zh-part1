# 节点模块深潜:缓冲区

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-buffer-1kc0>

你好啊。是时候开始我的节点模块的另一部分了——深潜系列！本周，我将深入研究 Node 中的 Buffer 对象。我承认，当我打开文件进行初步浏览时，我有点害怕。这是一段令人叫绝的 1599 行代码(对于包含注释的行，可能会有一些出入)。但是你知道吗？我已经做了足够多的通读，不会被吓倒，所以我继续前进。

在深入实际代码之前，先介绍一下缓冲区可能会有所帮助。Node 中的缓冲区使开发人员能够与二进制数据流进行交互，这对于像读写文件系统中的文件这样的事情特别有用。如果你在`fs`模块中使用过像`fs.createReadStream`或`fs.createWriteStream`这样的函数，你就已经和缓冲区交互过了。举个例子，这里有一个包含单词“Safia”在 Node 中的表示的缓冲区。

```
> Buffer.from("Safia")
<Buffer 53 61 66 69 61> 
```

Enter fullscreen mode Exit fullscreen mode

好吧！是时候深入本质，看看代码本身了。像往常一样，[这里的](https://github.com/nodejs/node/blob/9fb390a1c66c91796626fe199176619ce4a54f62/lib/buffer.js)是一个永久链接，指向我将要浏览的`Buffer`类的特定版本。我通常从文件的底部开始读取代码，以确定特定模块公开了什么类和 API。下面看看缓冲模块输出了什么。

```
module.exports = exports = {
  Buffer,
  SlowBuffer,
  transcode,
  INSPECT_MAX_BYTES: 50,

  // Legacy
  kMaxLength,
  kStringMaxLength
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来它导出了两个类，一个`Buffer`和一个`SlowBuffer`。目前我不确定它们之间的规格区别是什么，除了一个很慢而另一个不慢的明显区别。除了这些类导出之外，这个模块似乎还导出了几个常量和一个函数。

我想做的第一件事是弄清楚什么是`SlowBuffer`,为什么它会存在于代码库中。我去了 Node 最新版本下的文档页面`Buffer`,发现在`SlowBuffer`类的[部分，它实际上已经过时了。一个`SlowBuffer`实际上是一个未被缓冲的`Buffer`对象的变体。未缓冲区是指没有为缓冲区实例初始化内存的缓冲区。](https://nodejs.org/api/buffer.html#buffer_class_slowbuffer)

现在我明白了这一点，我开始查看`Buffer`类本身的代码。这个类公开了很多函数，所以我想把重点放在我在日常开发工作中使用的几个函数上。

首先，我想从看一看`Buffer.from`方法开始。`Buffer.from`允许开发者从字符串、数组或另一个`Buffer`中创建一个`Buffer`对象。方法定义要求开发人员提供一个`value`、`encodingOrOffeset`和`length`参数。后两个参数只有在开发人员传递的`value`是一个数组时才适用，在这种情况下，它们表示数组中第一个字节的索引(T8 对象将公开)和`Buffer`对象中要公开的总字节数。如果`value`是一个字符串，第二个参数是字符串的编码(例如，UTF-8 或 ASCII)。

```
Buffer.from = function from(value, encodingOrOffset, length) { 
```

Enter fullscreen mode Exit fullscreen mode

函数中的前几行代码定义了当`value`的类型是字符串或数组时该做什么。该方法相应地调用`fromString`和`fromArrayBuffer`函数。

```
if (typeof value === 'string')
  return fromString(value, encodingOrOffset);

if (isAnyArrayBuffer(value))
  return fromArrayBuffer(value, encodingOrOffset, length); 
```

Enter fullscreen mode Exit fullscreen mode

我决定先看看`fromString`函数。如上所述，它的函数定义需要一个`string`和一个`encoding`。

```
function fromString(string, encoding) { 
```

Enter fullscreen mode Exit fullscreen mode

该函数从处理开发人员提供的参数中的潜在边缘情况开始。例如，如果用户没有提供字符串或编码，函数将返回一个空缓冲区。

```
 if (typeof encoding !== 'string' || encoding.length === 0) {
    if (string.length === 0)
      return new FastBuffer(); 
```

Enter fullscreen mode Exit fullscreen mode

如果开发人员没有提供编码，该函数将使用 UTF-8 作为默认编码。`length`变量定义了字符串中的字节数，假设它是用 UTF-8 编码的。

```
encoding = 'utf8';
length = byteLengthUtf8(string); 
```

Enter fullscreen mode Exit fullscreen mode

下一个 if 语句检查字符串中的字节长度是否比`(Buffer.poolSize >>> 1)`长。我对`(Buffer.poolSize >>> 1)`有点困惑，所以我做了一些调查。`Buffer.poolSize`的值是`8 * 1024`或`8192`字节。这个数字表示内部缓冲区对象使用的字节数。然后，使用零填充右移将该值右移 1 位。零填充右移不同于“标准”右移(`>>`)，因为当比特向右移动时，它不从左侧添加比特。因此，每一个向右移位填零的数字总是正数。本质上，if 语句确定用户试图创建缓冲区的字符串是否适合默认情况下缓冲区中预分配的 8192 字节。如果是这样，它将相应地加载字符串。

```
return createFromString(string, encoding); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，如果字符串中的字节数大于缓冲区中预分配的字节数，它会在将字符串存储到缓冲区之前为字符串分配更多的空间。

```
if (length > (poolSize - poolOffset))
  createPool();
var b = new FastBuffer(allocPool, poolOffset, length);
const actual = b.write(string, encoding);
if (actual !== length) {
  // byteLength() may overestimate. That's a rare case, though.
  b = new FastBuffer(allocPool, poolOffset, actual);
}
poolOffset += actual;
alignPool();
return b; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我深入研究了当用户将一个数组缓冲区传递给`Buffer.from`时执行的`fromArrayBuffer`函数。`fromArrayBuffer`函数的函数定义接受数组对象、字节偏移量和数组缓冲区的长度。

```
function fromArrayBuffer(obj, byteOffset, length) { 
```

Enter fullscreen mode Exit fullscreen mode

该函数首先响应传递给它的可能混乱的参数。它首先检查用户是否没有向函数传递一个`byteOffset`，在这种情况下，它使用偏移量 0。在其他情况下，该函数确保`byteOffset`是一个正数。

```
if (byteOffset === undefined) {
  byteOffset = 0;
} else {
  byteOffset = +byteOffset;
  // check for NaN
  if (byteOffset !== byteOffset)
    byteOffset = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

缓冲区的长度定义为输入缓冲区数组的长度减去偏移量。

```
const maxLength = obj.byteLength - byteOffset; 
```

Enter fullscreen mode Exit fullscreen mode

如果`byteOffset`大于输入缓冲区的长度，那么函数抛出一个错误。

```
if (maxLength < 0)
    throw new errors.RangeError('ERR_BUFFER_OUT_OF_BOUNDS', 'offset'); 
```

Enter fullscreen mode Exit fullscreen mode

最后，该函数执行一些检查，以确保新 ArrayBuffer 的长度在新 offset 对象的范围内是一个正数。

```
if (length === undefined) {
  length = maxLength;
} else {
  // convert length to non-negative integer
  length = +length;
  // Check for NaN
  if (length !== length) {
    length = 0;
  } else if (length > 0) {
    if (length > maxLength)
      throw new errors.RangeError('ERR_BUFFER_OUT_OF_BOUNDS', 'length');
  } else {
    length = 0;
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后使用来自旧的`obj`数组缓冲区的修改后的`byteOffset`和`length`参数创建新的缓冲区。

```
return new FastBuffer(obj, byteOffset, length); 
```

Enter fullscreen mode Exit fullscreen mode

回到`Buffer.from`函数，它做了一些验证检查，以确保用户试图创建缓冲区的`value`是有效的。

```
if (value === null || value === undefined) {
  throw new errors.TypeError(
    'ERR_INVALID_ARG_TYPE',
    'first argument',
    ['string', 'Buffer', 'ArrayBuffer', 'Array', 'Array-like Object'],
    value
  );
}

if (typeof value === 'number')
  throw new errors.TypeError(
    'ERR_INVALID_ARG_TYPE', 'value', 'not number', value
  ); 
```

Enter fullscreen mode Exit fullscreen mode

然后该函数检查用户传递的`value`是否包含一个`valueOf`函数。`valueOf`函数是在 JavaScript 中的对象原型上定义的，它返回 JavaScript 中特定对象的原始类型的值。例如，开发人员可能会创建一个特殊的`Cost`对象来存储一个对象的价格，并创建一个`valueOf`函数来返回一个数字形式的价格(浮点)。从某种意义上说，`Buffer.from`方法的这一部分试图从作为`value`传递给函数的任何对象中提取出一个原始类型，并使用它来生成一个新的缓冲区。

```
const valueOf = value.valueOf && value.valueOf();
if (valueOf !== null && valueOf !== undefined && valueOf !== value)
  return Buffer.from(valueOf, encodingOrOffset, length); 
```

Enter fullscreen mode Exit fullscreen mode

然后函数尝试调用`fromObject`函数，返回这个函数创建的缓冲区(假设非空)。

```
var b = fromObject(value);
if (b)
  return b; 
```

Enter fullscreen mode Exit fullscreen mode

下一个检查评估传递的值是否定义了一个`toPrimitive`函数。`toPrimitive`函数从给定的 JavaScript 对象中返回一个原始值。`Buffer.from`函数试图从该函数返回的原语创建一个缓冲区(如果可用的话)。

```
if (typeof value[Symbol.toPrimitive] === 'function') {
  return Buffer.from(value[Symbol.toPrimitive]('string'),
                     encodingOrOffset,
                     length);
} 
```

Enter fullscreen mode Exit fullscreen mode

在所有其他情况下，该函数会引发 TypeError。

```
throw new errors.TypeError(
  'ERR_INVALID_ARG_TYPE',
  'first argument',
  ['string', 'Buffer', 'ArrayBuffer', 'Array', 'Array-like Object'],
  value
); 
```

Enter fullscreen mode Exit fullscreen mode

所以本质上，`Buffer.from`函数将尝试处理字符串或数组缓冲区的值，然后尝试处理类似数组的值，然后尝试提取原始值来创建缓冲区，然后在所有其他情况下向用户发出 TypeError。

我想通读的`Buffer`对象的下一个函数是`write`函数。`Buffer.write`函数的函数定义要求开发者传递`string`来写，传递`offset`给出的写字符串前跳过的字节数，`length`给出的写字节数，`string`的`encoding`。

```
Buffer.prototype.write = function write(string, offset, length, encoding) { 
```

Enter fullscreen mode Exit fullscreen mode

如果没有给定偏移量，该函数将字符串写入缓冲区的开头。

```
if (offset === undefined) {
  return this.utf8Write(string, 0, this.length);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果没有给定`offset`或`length`，该函数从 0 的`offset`开始，并使用缓冲区的默认长度。

```
// Buffer#write(string, encoding)
} else if (length === undefined && typeof offset === 'string') {
  encoding = offset;
  length = this.length;
  offset = 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果开发者同时提供了一个`offset`和一个`length`，该函数将确保它们是有效的有限值，并在给定一个`offset`的情况下正确地计算出`length`。

```
} else if (isFinite(offset)) {
  offset = offset >>> 0;
  if (isFinite(length)) {
    length = length >>> 0;
  } else {
    encoding = length;
    length = undefined;
  }

  var remaining = this.length - offset;
  if (length === undefined || length > remaining)
    length = remaining;

  if (string.length > 0 && (length < 0 || offset < 0))
    throw new errors.RangeError('ERR_BUFFER_OUT_OF_BOUNDS', 'length', true);
} 
```

Enter fullscreen mode Exit fullscreen mode

在所有其他情况下，该函数假设开发人员试图使用过时版本的`Buffer.write` API，并引发一个错误。

```
 else {
   // if someone is still calling the obsolete form of write(), tell them.
   // we don't want eg buf.write("foo", "utf8", 10) to silently turn into
   // buf.write("foo", "utf8"), so we can't ignore extra args
   throw new errors.Error(
     'ERR_NO_LONGER_SUPPORTED',
     'Buffer.write(string, encoding, offset[, length])'
   );
 } 
```

Enter fullscreen mode Exit fullscreen mode

一旦该函数适当地设置了`offset`和`length`变量，它将根据不同的可能`encodings`决定做什么。如果没有给出`encoding`，默认情况下，`Buffer.write`方法采用 UTF-8。

```
if (!encoding) return this.utf8Write(string, offset, length); 
```

Enter fullscreen mode Exit fullscreen mode

在其他情况下，该函数调用适当的`xWrite`函数，其中`x`是编码。我觉得有趣的是，用于评估潜在编码的 switch 语句检查了`encoding`字符串的长度，然后检查了`encoding`的实际值。本质上，该函数评估 switch 语句不同分支中编码为`utf8`和`utf-8`的情况。

```
 switch (encoding.length) {
    case 4: ...
    case 5: ...
    case 7: ...
    case 8: ...
    case 6: ...
    case 3: ...
  } 
```

Enter fullscreen mode Exit fullscreen mode

我希望在 Buffer 类中通读一些更有趣的函数，但我可能会将它们放在这篇博客文章的第 2 部分中。现在，我就讲到这里。如果您对上述内容有任何问题或评论，请随时[问我问题](https://blog.safia.rocks/ask)或[在 Twitter 上联系我](https://twitter.com/captainsafia)。