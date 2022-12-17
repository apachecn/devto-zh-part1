# 节点模块深潜:事件发射器

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-eventemitter-3oeg>

因此，我在我的[上一篇与节点相关的博客文章](https://dev.to/captainsafia/how-does-processbinding-in-node-work-45a6)中使用了一些 C++代码，我想我应该通过更多的 JavaScript 阅读回到我的舒适区。

当我第一次开始学习 Node 时，我很难理解的一件事是该语言的事件驱动特性。我没有真正接触过事件驱动的编程语言。嗯，事后看来，我想是的。在学习 Node 之前，我已经在代码中使用了 jQuery 的`.on`和`.click`，这是一种事件驱动的编程风格。那时，我并没有真正意识到我正在编写事件驱动的代码。不管怎么说，我一直好奇要深入研究的一件事是 Node 中的事件发射器。让我们开始吧。

如果你不熟悉 Node 的事件驱动特性，你可以看看一些比我解释得更好的博客文章。这里有几个可能对你有帮助。

*   [了解 Node.js 事件驱动架构](https://medium.freecodecamp.org/understanding-node-js-event-driven-architecture-223292fcbc2d)
*   [事件驱动架构 node.js](https://garywoodfine.com/event-driven-architecture-node-js/)
*   [了解 Node.js 事件循环](http://nodesource.com/blog/understanding-the-nodejs-event-loop/)
*   [node . js 中的事件文档](https://nodejs.org/api/events.html)

好吧！所以我想读一下 EventEmitter 的代码，看看我是否能弄清楚这个`EventEmitter`类到底是怎么回事。你可以在这里找到我将要引用的代码[。](https://github.com/nodejs/node/blob/61b4d60c5d9694e79069b1680b3736c96a5de501/lib/events.js)

所以任何`EventEmitter`对象中最关键的两个函数是`.on`函数和`.emit`函数。`.on`函数是负责监听特定类型事件的函数。`.emit`函数负责分派特定类型的事件。我决定从钻研这些特定函数的代码开始我的探索。我将从`.emit`开始，因为在查看事件如何被监听之前查看事件是如何发出的是有意义的。

因此，如果您使用过 EventEmitter 对象，那么`emit`的函数声明是非常简单明了的。它接受一个类型参数(通常是一个字符串)和一组将传递给处理程序的参数。

```
EventEmitter.prototype.emit = function emit(type, ...args) { 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，我注意到的第一件事是“错误”类型的事件和其他类型的事件的处理方式不同。老实说，我花了一段时间才弄清楚下面的代码到底发生了什么，尤其是那一小部分`if-else if`。所以基本上，这段代码所做的就是检查发出的事件是否是一个错误。如果是，它检查在连接到`EventEmitter`的监听器集合中是否有一个`error`事件的监听器。如果附加了监听器，该函数返回

```
let doError = (type === 'error');

const events = this._events;
if (events !== undefined)
  doError = (doError && events.error === undefined);
else if (!doError)
  return false; 
```

Enter fullscreen mode Exit fullscreen mode

如果没有事件侦听器(如注释所述)，那么发射器将向用户抛出一个错误。

```
// If there is no 'error' event listener then throw.
if (doError) {
  let er;
  if (args.length > 0)
    er = args[0];
  if (er instanceof Error) {
    throw er; // Unhandled 'error' event
  }
  // At least give some kind of context to the user
  const errors = lazyErrors();
  const err = new errors.Error('ERR_UNHANDLED_ERROR', er);
  err.context = er;
  throw err;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，如果抛出的类型不是错误，那么`emit`函数将检查 EventEmitter 对象上附加的侦听器，查看是否有任何侦听器已经为特定的`type`声明并调用它们。

```
const handler = events[type];

if (handler === undefined)
  return false;

if (typeof handler === 'function') {
  Reflect.apply(handler, this, args);
} else {
  const len = handler.length;
  const listeners = arrayClone(handler, len);
  for (var i = 0; i < len; ++i)
    Reflect.apply(listeners[i], this, args);
}

return true; 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。这很简单。关于`on`功能…

EventEmitter 中的`on`函数隐式调用内部函数`_addListener`,该函数用如下声明定义。

```
function _addListener(target, type, listener, prepend) 
```

Enter fullscreen mode Exit fullscreen mode

这些参数中的大部分都是不言自明的，我唯一好奇的是`prepend`参数。事实证明，这个参数默认为`false`，开发者不能通过任何公共 API 对其进行配置。

旁注:开个玩笑！我发现了一些 GitHub 提交消息来解决这个问题。看来它在`_addListener`对象中被设置为 false，因为许多开发人员不恰当地访问了 EventEmitter 对象的内部`_events`属性，以将侦听器添加到列表的开头。如果你想这样做，你应该使用`prependListener`。

`_addListener`函数从做一些基本的参数验证开始。我们不希望任何人搬起石头砸自己的脚！一旦添加了参数，该函数会尝试将`type`的`listener`添加到当前`EventEmitter`对象的`events`属性中。我觉得有趣的代码之一是下面的代码。

```
if (events === undefined) {
  events = target._events = Object.create(null);
  target._eventsCount = 0;
} else {
  // To avoid recursion in the case that type === "newListener"! Before
  // adding it to the listeners, first emit "newListener".
  if (events.newListener !== undefined) {
    target.emit('newListener', type,
                listener.listener ? listener.listener : listener);

    // Re-assign `events` because a newListener handler could have caused the
    // this._events to be assigned to a new object
    events = target._events;
  }
  existing = events[type];
} 
```

Enter fullscreen mode Exit fullscreen mode

我对这里的`else`特别好奇。因此，看起来如果已经在当前的 EventEmitter 对象上初始化了`events`属性(这意味着我们之前已经添加了一个侦听器)，那么就有一些有趣的边缘情况检查正在进行。我决定做一些 GitHub 人类学来弄清楚这个特定的代码变化是什么时候添加的，以获得更多关于这个 bug 是如何出现的以及为什么添加它的背景信息。我很快意识到这是一个坏主意，因为这种特殊的逻辑已经在代码中存在了大约 4 年，我很难找到它的起源。我试图更仔细地阅读代码，看看这到底是在检查什么类型的边缘情况。

我最终不是通过阅读代码，而是通过阅读文档找到了答案。孩子们，别忘了吃蔬菜和阅读所有的文件！节点文档说明:

> 当添加新的侦听器时，所有 EventEmitters 都发出事件`'newListener'`,当删除现有侦听器时，发出事件`'removeListener'`。

所以基本上，当一个新的侦听器被添加到之前的*时，就会发出`newListener`事件，实际的侦听器被添加到 EventEmitter 的`_events`属性中。这是因为如果你正在添加一个`newListener`事件监听器，并且它在默认情况下`newListener`发出之前被添加到事件列表中，那么它将结束调用自身。这就是为什么这个`newListener` emit 代码被放在函数的顶部。*

下一段代码试图判断这个`type`的侦听器是否已经被附加。基本上，这样做是为了确保如果一个事件只有一个监听器，那么它被设置为`_events`关联数组中的一个函数值。如果它们不止一个监听器，它被设置为一个数组。这是一个小的优化，但许多小的优化是什么使节点伟大！

```
if (existing === undefined) {
  // Optimize the case of one listener. Don't need the extra array object.
  existing = events[type] = listener;
  ++target._eventsCount;
} else {
  if (typeof existing === 'function') {
    // Adding the second element, need to change to array.
    existing = events[type] =
      prepend ? [listener, existing] : [existing, listener];
    // If we've already got an array, just append.
  } else if (prepend) {
    existing.unshift(listener);
  } else {
    existing.push(listener);
  } 
```

Enter fullscreen mode Exit fullscreen mode

在这个函数中进行的最后一次检查试图确认对于一个特定的事件类型，在一个特定的事件发射器上是否附加了太多的侦听器。如果是这种情况，可能意味着代码中有错误。总的来说，我不认为在一个事件上附加很多侦听器是好的做法，所以 Node 会做一些有用的检查来警告你是否这样做。

```
 // Check for listener leak
  if (!existing.warned) {
    m = $getMaxListeners(target);
    if (m && m > 0 && existing.length > m) {
      existing.warned = true;
      // No error code for this since it is a Warning
      const w = new Error('Possible EventEmitter memory leak detected. ' +
                          `${existing.length}  ${String(type)} listeners ` +
                          'added. Use emitter.setMaxListeners() to ' +
                          'increase limit');
      w.name = 'MaxListenersExceededWarning';
      w.emitter = target;
      w.type = type;
      w.count = existing.length;
      process.emitWarning(w);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！最后，这个`.on`函数返回它所附加的 EventEmitter 对象。

我非常喜欢阅读 EventEmitter 的代码。我发现它非常清晰易懂(不同于我上次进行的 C++冒险)——尽管我怀疑这与我对语言的熟悉程度有很大关系。