# ES6 中的符号-快速指南

> 原文：<https://dev.to/mildrenben/symbols-in-es6---a-quick-guide-dhm>

## 概观

符号是 ES6 中引入的一种新的基本类型。

符号是完全唯一的标识符。就像它的原始对应物一样，可以使用返回一个`Symbol`的工厂函数`Symbol()`来创建它们。

```
const foo = Symbol() 
```

下面的两个变量，`foo`和`bar`是不一样的，它们都是唯一的。假设每个`Symbol()`调用都返回一个很长随机字符串。

```
const foo = Symbol()

const bar = Symbol()

foo === bar // false

// I imagine Symbols looking like this 'NqkvK1kq7q#R99l9&7YH*@7wa8cFJc' 
```

它带有一个可选的描述参数，仅用于调试目的。

```
const foo = Symbol('foo') 
```

与它的对应物(布尔、数字和字符串)不同，符号没有文字，也不能用作构造函数。

```
const foo = new Symbol()
// TypeError: Symbol is not a constructor 
```

## 使用

符号的主要用例是使*成为私有的*对象属性，它只能是字符串或符号类型(出于好奇，数字会自动转换成字符串)。

```
const sym = Symbol()

const foo = {
  [sym]: 'someValue'
}

foo[sym] // 'someValue' 
```

为对象属性使用符号对于*隐藏*某些可能与其他库冲突的属性来说很方便。

* * *

它们对于在对象上定义元数据也非常有用，因为符号是不可枚举的，因此在使用`for...of`循环和其他返回对象属性的函数时，它们不会被*或*迭代。

```
const sym = Symbol()

const foo = {
  name: 'Ben',
  age: 25,
  [sym]: 'someHiddenMetadata'
}

for(let val of foo) {
  console.log(val) // Ben, 25
}

Object.getOwnPropertyNames(foo) // Ben, 25

Object.keys(foo) // Ben, 25 
```

作为对象属性的符号并没有完全隐藏，这就是为什么我在这篇文章中用斜体显示了 *hidden* 。您仍然可以使用以下方法访问符号:

```
Object.getOwnPropertySymbols(foo) // Symbol()

Reflect.ownKeys(foo) // Symbol() 
```

因此，它们并不完全是私有的，但是在常见的迭代周期中会被跳过。

* * *

正如您所料，它们也可以用于任何对象属性名，包括方法。

```
const bar = {
  [Symbol('method')] () { 
    console.log('hello')  
  }
} 
```

* * *

### 不带宾语的用法

尽管符号的主要应用似乎是作为对象属性名，但它们在其他地方也有价值，最明显的是作为常量中字符串的替代。

许多项目都有一组常量，如下所示:

```
const ARTICLE1 = 'ARTICLE1'
const ARTICLE2 = 'ARTICLE2'
const ARTICLE3 = 'ARTICLE3' 
```

然后，这些常量可能会在发出请求的另一个文件中使用:

```
import * as c from './constants'

const getRequestURL = req => {
  switch(req) {
    // Standard articles
    case c.ARTICLE1: {
      // do stuff
      return `https://api.com/${c.ARTICLE1}`
    }
    case c.ARTICLE2: {
      // do stuff
      return `https://api.com/${c.ARTICLE2}`
    }
    case c.ARTICLE3: {
      // do stuff
      return `https://api.com/${c.ARTICLE3}`
    }
    // Articles written by users get handled here
    default: {
      // do stuff
      return `https://api.com/userGeneratedContent/${req} }
  }
} 
```

显然，以上是一个相当做作的例子，但你得到了图片。许多前端应用程序的结构与此类似。

让我们想象一下，偶然有人将他们文章的标题命名为“ARTICLE1”。它不会到达它想要到达的 switch 语句的`default`函数，它会在上面被拦截。你可以看到，因为我们的常数不是唯一的，它们可以以意想不到的方式相互作用。

这个问题的解决方案是使用符号作为常量。

```
const ARTICLE1 = Symbol('ARTICLE1')
const ARTICLE2 = Symbol('ARTICLE2')
const ARTICLE3 = Symbol('ARTICLE3') 
```

现在这些常数不可能和另一个常数冲突。

## 细节和注意事项

### 全球符号

全局符号似乎违背了符号的全部意义:它们不是唯一的。但是他们确实有目的。

存在一个全局符号注册表，您可以在其中存储和访问全局符号。您可以使用`Symbol.for(key)`方法来创建和访问全局符号。

```
const foo = Symbol.for('hello') // If the Symbol does not exist, it's created

const bar = Symbol.for('hello') // The Symbol exists, so it is returned

foo === bar // true 
```

注意这里的`key`不像常规符号那样是可选的描述，它是一个标识符。

如果您有符号本身并想要密钥，您可以反向查找全局符号。

```
const foo = Symbol.for('someKey')

const bar = Symbol.keyFor(foo) // someKey 
```

全球符号存在于*个领域*中。领域是代码存在的上下文，就像范围一样。模块、全局变量等。都存在于领域内的*。浏览器中的每个框架都有自己的领域，所以 iframe 与主框架有不同的上下文。全局符号实际上跨领域存在，并且可以在它们之间使用。*

### “众所周知”的符号

javascript 中内置了许多“众所周知”的符号，它们都有特定的功能。

这些所谓的“众所周知”的符号中最有用的是`Symbol.iterator`，它允许我们使自己的对象可迭代。`for...of`循环调用`Symbol.iterator`来迭代一组值。

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator) 提供了这个简单的例子来展示如何使用`Symbol.iterator`。

```
var myIterable = {}
myIterable[Symbol.iterator] = function* () {
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable] // [1, 2, 3] 
```

你可以在这里看到“众所周知的”符号的完整列表。

### 没有到字符串的自动转换

与许多其他类型不同，符号不会自动转换为字符串。您可能甚至没有注意到其他类型也发生了这种情况，但是想想当您 alert()一个数字或 alert()一个数组时。它们会自动转换成字符串。

符号不支持这一点。必须显式调用`.toString()`方法。

这个函数在这里帮助我们，因为通常它们不应该被转换。

```
const sym = Symbol();
const foo = '' + sym
// TypeError: Cannot convert a Symbol value to a string

alert(sym)
// TypeError: Cannot convert a Symbol value to a string

alert(sym.toString()) // Symbol() 
```

因此，您需要在对象文字中使用方括号，就像这样`const foo = { [Symbol()]: 'hey' }`。

### 它们什么时候被复制？

符号被复制到`Object.assign`和对象扩展操作符`{ ... }`中。

```
const sym = Symbol('hey')

const a = { [sym]: 'a' }

const b = { ...a } // { Symbol('hey'): 'a' }

const c = Object.assign(a, {}) // { Symbol('hey'): 'a' } 
```

## 进一步阅读

*   [ECMAScript 6 中的符号由 2ality](http://2ality.com/2014/12/es6-symbols.html)
*   Keith Cirkel 的 ES6 元编程