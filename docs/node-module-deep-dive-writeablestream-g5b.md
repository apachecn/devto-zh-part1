# 节点模块深潜:可写流

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-writeablestream-g5b>

又来了！今天我在博客上做另一个节点模块的深入研究。我想我会一头扎进这个`WriteableStreams`物体。`WriteableStreams`公开允许你写一个流的方法。它们公开了多个事件，如`close`、`drain`和`pipe`，以及几个函数，如`cork`、`end`和`write`。在我深入研究`WriteableStream`对象之前，它有助于提供一个关于流概念的[快速入门。](https://medium.freecodecamp.org/node-js-streams-everything-you-need-to-know-c9141306be93)

好吧！现在我们已经建立了基础，是时候深入代码了。我将做一个[的代码演练，这个版本是`WritableStream`的](https://github.com/nodejs/node/blob/9531fcbb2e447c6a5ef0366c5446e10f90073cb5/lib/_stream_writable.js)。当我浏览文件时，我很高兴地发现在整个代码库中有相当多的注释来阐明库的不同部分是做什么的。这些解释性注释使得通读代码库更容易解析代码。我做的第一件事是检查在 WriteableState 对象上定义的属性[。代码库中的注释很好地描述了每个属性是什么，所以我在这里就不赘述了。从代码中可以看出，`WritableState`对象保存了当前`WriteableStream`的信息(这很有意义！).](https://github.com/nodejs/node/blob/9531fcbb2e447c6a5ef0366c5446e10f90073cb5/lib/_stream_writable.js#L45-L154)

在`WriteableState`上定义了一个函数，用来获取`Writeable`上的当前缓冲区作为一个列表。

```
WritableState.prototype.getBuffer = function getBuffer() {
  var current = this.bufferedRequest;
  var out = [];
  while (current) {
    out.push(current);
    current = current.next;
  }
  return out;
}; 
```

Enter fullscreen mode Exit fullscreen mode

`Writable`流的定义概述了函数的一些属性。也就是说，程序员可以为`Writable`指定特殊的`write`、`destroy`和`final`功能。

```
function Writable(options) {
  // Writable ctor is applied to Duplexes, too.
  // `realHasInstance` is necessary because using plain `instanceof`
  // would return false, as no `_writableState` property is attached.

  // Trying to use the custom `instanceof` for Writable here will also break the
  // Node.js LazyTransform implementation, which has a non-trivial getter for
  // `_writableState` that would lead to infinite recursion.
  if (!(realHasInstance.call(Writable, this)) &&
      !(this instanceof Stream.Duplex)) {
    return new Writable(options);
  }

  this._writableState = new WritableState(options, this);

  // legacy.
  this.writable = true;

  if (options) {
    if (typeof options.write === 'function')
      this._write = options.write;

    if (typeof options.writev === 'function')
      this._writev = options.writev;

    if (typeof options.destroy === 'function')
      this._destroy = options.destroy;

    if (typeof options.final === 'function')
      this._final = options.final;
  }

  Stream.call(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

在`Writeable`原型上定义的第一个函数引入了一个相当古怪的注释。

```
// Otherwise people can pipe Writable streams, which is just wrong.
Writable.prototype.pipe = function() {
  this.emit('error', new errors.Error('ERR_STREAM_CANNOT_PIPE'));
}; 
```

Enter fullscreen mode Exit fullscreen mode

你不能从一个`Writeable`流中读取数据，所以你当然不能通过管道从一个`WriteableStream`中输出数据，因为它根本就不存在。

接下来定义`write`功能。它需要三个参数:要写入的数据的`chunk`、数据的`encoding`，以及一旦写入完成就要执行的`cb`(回调)。

```
Writable.prototype.write = function(chunk, encoding, cb) {
  var state = this._writableState;
  var ret = false;
  var isBuf = !state.objectMode && Stream._isUint8Array(chunk);

  if (isBuf && Object.getPrototypeOf(chunk) !== Buffer.prototype) {
    chunk = Stream._uint8ArrayToBuffer(chunk);
  }

  if (typeof encoding === 'function') {
    cb = encoding;
    encoding = null;
  }

  if (isBuf)
    encoding = 'buffer';
  else if (!encoding)
    encoding = state.defaultEncoding;

  if (typeof cb !== 'function')
    cb = nop;

  if (state.ended)
    writeAfterEnd(this, cb);
  else if (isBuf || validChunk(this, state, chunk, cb)) {
    state.pendingcb++;
    ret = writeOrBuffer(this, state, isBuf, chunk, encoding, cb);
  }

  return ret;
}; 
```

Enter fullscreen mode Exit fullscreen mode

该函数获取`WritableStream`的当前状态，并检查写入流的数据是否由缓冲区或对象组成，并将这一区别存储在`isBuf`中。如果被写入流的数据应该是一个`Buffer`，但是传递的`chunk`不是一个`Buffer`，那么该函数假定它是一个整数数组并将其转换为一个`Buffer`。之后，有一些逻辑确保参数被正确映射。也就是说，用户不必向函数传递一个`encoding`参数。在这种情况下，传递的第二个参数实际上是要调用的回调。如果流已经结束，该函数将调用一个`writeAfterEnd`函数，该函数将向用户发出一个错误，因为您不能写入一个已经关闭的流。

```
function writeAfterEnd(stream, cb) {
  var er = new errors.Error('ERR_STREAM_WRITE_AFTER_END');
  // TODO: defer error events consistently everywhere, not just the cb
  stream.emit('error', er);
  process.nextTick(cb, er);
} 
```

Enter fullscreen mode Exit fullscreen mode

否则，如果数据是一个缓冲区，该函数将调用一个`writeOrBuffer`函数。

```
// if we're already writing something, then just put this
// in the queue, and wait our turn. Otherwise, call _write
// If we return false, then we need a drain event, so set that flag.
function writeOrBuffer(stream, state, isBuf, chunk, encoding, cb) {
  if (!isBuf) {
    var newChunk = decodeChunk(state, chunk, encoding);
    if (chunk !== newChunk) {
      isBuf = true;
      encoding = 'buffer';
      chunk = newChunk;
    }
  }
  var len = state.objectMode ? 1 : chunk.length;

  state.length += len;

  var ret = state.length < state.highWaterMark;
  // we must ensure that previous needDrain will not be reset to false.
  if (!ret)
    state.needDrain = true;

  if (state.writing || state.corked) {
    var last = state.lastBufferedRequest;
    state.lastBufferedRequest = {
      chunk,
      encoding,
      isBuf,
      callback: cb,
      next: null
    };
    if (last) {
      last.next = state.lastBufferedRequest;
    } else {
      state.bufferedRequest = state.lastBufferedRequest;
    }
    state.bufferedRequestCount += 1;
  } else {
    doWrite(stream, state, false, len, chunk, encoding, cb);
  }

  return ret;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情，所以让我们一点一点地了解一下。函数中的前几行检查传递的`chunk`是否不是缓冲区。如果不是，使用`decodeChunk`对`chunk`进行解码，使用`Buffer.from`函数从字符串中创建一个块。

```
function decodeChunk(state, chunk, encoding) {
  if (!state.objectMode &&
      state.decodeStrings !== false &&
      typeof chunk === 'string') {
    chunk = Buffer.from(chunk, encoding);
  }
  return chunk;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，它通过评估流的长度是否超过`highWaterMark`来检查流的容量是否已经达到，并适当地设置`needDrain`参数。然后，它将存储在状态中的`lastBufferedRequest`的值更新到作为参数传递的缓冲区，并调用将块写入流的`doWrite`函数。

接下来定义的函数是`cork`和`uncork`函数，定义如下。软木塞功能增加`corked`计数器。`corked`计数器实际上充当一个布尔值，当它有一个非零值时，意味着有写操作需要被缓冲。`uncork`功能减少`corked`参数并清空缓冲器。

```
 Writable.prototype.cork = function() {
  var state = this._writableState;

  state.corked++;
};

Writable.prototype.uncork = function() {
  var state = this._writableState;

  if (state.corked) {
    state.corked--;

    if (!state.writing &&
        !state.corked &&
        !state.finished &&
        !state.bufferProcessing &&
        state.bufferedRequest)
      clearBuffer(this, state);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下一个函数是一个短小精悍的函数，允许用户在`WriteableStream`上设置默认编码，或者在用户提供无效编码时引发错误。

```
Writable.prototype.setDefaultEncoding = function setDefaultEncoding(encoding) {
  // node::ParseEncoding() requires lower case.
  if (typeof encoding === 'string')
    encoding = encoding.toLowerCase();
  if (!Buffer.isEncoding(encoding))
    throw new errors.TypeError('ERR_UNKNOWN_ENCODING', encoding);
  this._writableState.defaultEncoding = encoding;
  return this;
}; 
```

Enter fullscreen mode Exit fullscreen mode

当最后一个`chunk`需要写入流时，调用`end`函数。它通过调用我们在上面探索过的`write`函数来写块，完全公开它，并通过调用`endWriteable.`
来清除`WritableState`

```
Writable.prototype.end = function(chunk, encoding, cb) {
  var state = this._writableState;

  if (typeof chunk === 'function') {
    cb = chunk;
    chunk = null;
    encoding = null;
  } else if (typeof encoding === 'function') {
    cb = encoding;
    encoding = null;
  }

  if (chunk !== null && chunk !== undefined)
    this.write(chunk, encoding);

  // .end() fully uncorks
  if (state.corked) {
    state.corked = 1;
    this.uncork();
  }

  // ignore unnecessary end() calls.
  if (!state.ending && !state.finished)
    endWritable(this, state, cb);
}; 
```

Enter fullscreen mode Exit fullscreen mode

就这样吧！我仔细检查并阅读了`WriteableStream`对象的主要部分。我承认，在费力地阅读代码之前，我对发生在引擎盖下的一切有点不知所措。一个功能一个功能地检查和阅读代码无疑让我明白了很多事情。

如果您对上述内容有任何问题或评论，请随时[问我问题](https://blog.safia.rocks/ask)或[在 Twitter 上联系我](https://twitter.com/captainsafia)。