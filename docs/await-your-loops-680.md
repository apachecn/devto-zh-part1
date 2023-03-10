# 等待你的循环

> 原文：<https://dev.to/kayis/await-your-loops-680>

*[封面图片由达科塔雷在 Flickr 上](https://www.flickr.com/photos/dakotaray/)*

如果你最近读了一些关于 JavaScript 的东西，你可能已经知道它时不时会有新的特性。其中之一是**异步迭代**。

您可能已经了解了 iterables 和 async/await，但是如果还不了解，请不要担心，我会首先更新您的信息。

## 可重复项

[Iterables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols) 是在`Symbol.iterator`字段中有一个方法的对象，该方法用`next()`方法返回一个对象。这可用于获取该对象的所有可迭代值。

在数组的情况下，一个 JS 内置的对象，它看起来像这样:

```
const a = [1, 2, 3];
const iteratorOfA = a[Symbol.iterator]();

iteratorOfA.next(); // { value: 1, done: false }
iteratorOfA.next(); // { value: 2, done: false }
iteratorOfA.next(); // { value: 3, done: false }
iteratorOfA.next(); // { value: undefined, done: true} 
```

Enter fullscreen mode Exit fullscreen mode

好的一面是，您可以在 for-in-loop 中使用它，而不需要所有额外的语法。

```
const a = [1, 2, 3];
for(let i in a) console.log(i); 
```

Enter fullscreen mode Exit fullscreen mode

但是，是的，这不是太令人兴奋的，基本的 JavaScript 东西。

最酷的部分是，你可以写你自己的 iterables:

```
const iterable = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
  [Symbol.iterator]: function() {
    const keys = Object.keys(this);
    let i = 0;
    return {
      next: () => {
        if (i == keys.length) return {value: null, done: true};
        return {
          value: [keys[i], this[keys[i++]]],
          done: false
        };
      }
    }
  }
};

for(let item of iterable) console.log(item); 
```

Enter fullscreen mode Exit fullscreen mode

`Object.keys()`只将非符号键作为数组返回，所以`Symbol.iterator`不会出现在数组中。

现在，当调用该返回对象的下一个方法时，我将一个新的键和值对作为数组返回。当我找不到更多对时，我用`done: true`返回一个对象，告诉调用者我完成了。

正如您最后看到的，这个新对象可以像 for-in-loop 中的数组一样使用。

## 异步/等待

一个不太基本的 JavaScript 特性是异步函数，或者说 [async/await](https://dev.to/kayis/javascript-awaits) 。

本质上，它给承诺添加了语法糖。

如果没有 async/await，它看起来会是这样:

```
function load(url) {
  return fetch(url)
  .then(response => response.json())
  .(json => json.data);
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以写代码让*看起来*又同步了:

```
async function load(url) {
  const response = await fetch(url);
  const json = await response.json();
  return json.data;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 异步迭代

正如您可能想象的那样，有相当多的异步操作不是一个承诺就能完成的。

但是你不能简单地写这样的东西:

```
function processRows(filePath) {
  for(let row of getRow(filePath)) {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为`getRow()`调用会影响文件系统，这是一个异步操作。在得到要处理的单行之前，您必须读取整个文件。

或者它可能是一个服务器调用，最终被分页，您将不得不发送多个这样的调用来获取所有页面。

但是现在也有一个关于这个的提案。

不使用`Symbol.iterator`和返回您的值的 next-方法，而是使用`Symbol.asyncIterator`和返回那些值的承诺的 next-方法。

```
const asyncIterable = {
  a: 1,
  b: 2,
  c: 3,
  d: 4,
  [Symbol.asyncIterator]: function() {
    const keys = Object.keys(this);
    let i = 0;
    return {
      next: () => {
        if (i == keys.length) return Promise.resolve({value: null, done: true});
        return Promise.resolve({
          value: [keys[i], this[keys[i++]]],
          done: false
        });
      }
    }
  }
};

async function process() { 
  for await (let item of asyncIterable) console.log(item);
}

process(); 
```

Enter fullscreen mode Exit fullscreen mode

我们又回到了可以添加 try/catch 和所有其他好的同步特性的代码。

如您所见，如果您的可迭代数据完成了，您可以简单地返回一个解析为带有`done: true`的对象的承诺。就像，当服务器不再返回任何东西。

在这个例子中，数据在内存中，但是现在可能来自任何地方。

## 结论

异步可迭代是使 async/await 语法更加根深蒂固的另一个步骤。它们通过使异步代码看起来越来越同步来简化编写异步代码的工作。

另一种选择，我在其他帖子中也提到过，是由 [RxJS](https://github.com/ReactiveX/rxjs) 提供的[可观察的](https://github.com/tc39/proposal-observable)。