# 用 spread 运算符封装 console.log

> 原文：<https://dev.to/adamkdean/encapsulating-console-log-with-spread-operator-1nl7>

[ES6 或 ES2015](http://www.ecma-international.org/ecma-262/6.0/) 包含许多好东西。其中一个，我最喜欢的，是传播操作符。它“允许表达式在需要多个参数(用于函数调用)或多个元素(用于数组文字)的地方进行扩展。”

例如，您可以像这样组合两个数组:

```
var start = ['a', 'b', 'c'],
    end = ['d', 'e', 'f'];

var combined = [...start, ...end];

console.log(combined);

// => ['a', 'b', 'c', 'd', 'e', 'f'] 
```

Enter fullscreen mode Exit fullscreen mode

我更喜欢的是，你可以用函数参数来做这件事。假设我们想要包装`console.log`,这样我们就可以用它做一些有趣的事情。使用 spread 操作符可以很容易地做到这一点。

```
var log = function (format, ...args) {
    if (args.length > 0) console.log(format, args);
    else console.log(format);
}

log('test');
log('this is an %s test', 'interpolation');

// => test
// => this is an interpolation test 
```

Enter fullscreen mode Exit fullscreen mode

这改变了一切。[在 MDN](https://dev.ton-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) 阅读更多。

ES7 还应该带对象扩散操作符，所以可以做`{ ...initial, ...change }`。