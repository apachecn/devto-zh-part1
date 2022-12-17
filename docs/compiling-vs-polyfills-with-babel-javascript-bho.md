# 用 Babel 编译 vs . polyfill(JavaScript)

> 原文：<https://dev.to/tylermcginnis/compiling-vs-polyfills-with-babel-javascript-bho>

像 Babel 这样的编译器几乎肯定会成为构建 JavaScript 应用程序的基础工具。Babel 的一个基本部分是理解编译代码和多填充代码之间的区别。在大多数情况下，只做其中一项而不做另一项，只会让您的代码完成一部分。在这篇文章中，我们将分析两者之间的区别，这样你就可以自信地对你的代码进行未来检验。

### 视频

### 岗位

JavaScript 是一种不断进步的活语言。作为开发人员，这很好，因为我们在不断学习，我们的工具也在不断改进。这样做的缺点是浏览器通常需要几年时间才能赶上。每当一个新的语言提案被创建时，在被添加到官方语言规范之前，它需要经过五个不同的阶段。然后，它实际上需要被实现到你认为你的用户会使用的每一个浏览器中。由于这种延迟，如果您想使用最新的 JavaScript 特性，您需要等待最新的浏览器来实现它们(然后希望您的用户不要使用旧的浏览器)，或者您需要使用像 Babel 这样的工具来将新的现代代码编译回旧浏览器可以理解的代码。如果这样设计的话，几乎可以肯定的是，假设语言继续进步，像 Babel 这样的编译器将永远是构建 JavaScript 应用程序的基础部分。再说一遍，Babel 的目的是将你的代码转换成你所关心的任何浏览器都能理解的代码，这些代码使用了浏览器可能还不支持的新特性。

举个例子，像这样的代码，

```
const getProfile = username => {
  return fetch(`https://api.github.com/users/${username}`)
    .then((response) => response.json())
    .then(({ data }) => ({
      name: data.name,
      location: data.location,
      company: data.company,
      blog: data.blog.includes('https') ? data.blog : null
    }))
    .catch((e) => console.warn(e))
} 
```

Enter fullscreen mode Exit fullscreen mode

会被编译成类似这样的代码，

```
var getProfile = function getProfile(username) {
  return fetch('https://api.github.com/users/' + username).then(function (response) {
    return response.json();
  }).then(function (_ref) {
    var data = _ref.data;
    return {
      name: data.name,
      location: data.location,
      company: data.company,
      blog: data.blog.includes('https') ? data.blog : null
    };
  }).catch(function (e) {
    return console.warn(e);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，大多数 ES6 代码，如箭头函数和模板字符串，已经编译成常规的旧 ES5 JavaScript。然而，有两个特性没有被编译:`fetch`和`includes`。巴别塔的整个目标是获取我们的“下一代”代码(正如巴别塔网站所说)，并让它在我们关心的所有浏览器中工作。你可能会认为`includes`和`fetch`会被编译成类似`indexOf`和`XMLHttpRequest`的东西，但事实并非如此。所以现在问题变成了，为什么`fetch`或者`includes`没有被编译？`fetch`实际上不是 ES6 的一部分，所以假设我们只让 Babel 编译我们的 ES6 代码，至少有一点意义。`includes`然而是 ES6 的一部分却依然没有得到编译。这告诉我们，编译只能让我们的代码完成一部分。还有另一个步骤，如果我们使用某些新功能，我们需要采取聚合填充。编译和多填充有什么区别？当 Babel 编译你的代码时，它所做的是获取你的语法并通过各种语法转换来运行它，以获得浏览器兼容的语法。它没有向浏览器的全局名称空间或任何 JavaScript 原语添加任何您可能需要的新属性。你可以这样想，当你编译你的代码时，你正在转换它。添加多项填充时，会向浏览器添加新功能。

如果这仍然是模糊的，这里有一个新功能的列表。试着弄清楚它们是已编译的还是需要多填充的。

```
Arrow Functions
Classes
Promises
Destructuring
Fetch
String.includes 
```

Enter fullscreen mode Exit fullscreen mode

**箭头函数** : Babel 可以把箭头函数转化成常规函数，所以，可以编译。

**类**:像箭头函数一样，`Class`可以转换成有原型的函数，所以也可以编译。

**promises**:Babel 无法将 Promises 转换成浏览器能够理解的原生语法，更重要的是，编译不会向全局名称空间添加新的属性，比如`Promise`，所以 Promises 需要被多重填充。

**析构** : Babel 可以使用点符号将每一个析构的对象转化为普通变量。所以，编了。

**Fetch** : `fetch`需要多填充，因为根据前面提到的定义，当你编译代码时，你并没有添加任何你可能需要的新的全局或原始属性。`fetch`将是全局名称空间上的一个新属性，因此，需要对其进行多填充。

**String.includes** :这个比较棘手，因为它没有遵循我们通常的套路。有人可能会说`includes`应该被转换成使用`indexOf`，然而，同样，编译不会给任何原语添加新的属性，所以它需要被 polyfilled。

* * *

这里有一个来自 Babel 网站的非常广泛的列表，列出了哪些特性需要编译，哪些特性需要填充。

#### 需要编译的特性

```
Arrow functions
Async functions
Async generator functions
Block scoping
Block scoped functions
Classes
Class properties
Computed property names
Constants
Decorators
Default parameters
Destructuring
Do expressions
Exponentiation operator
For-of
Function bind
Generators
Modules
Module export extensions
New literals
Object rest/spread
Property method assignment
Property name shorthand
Rest parameters
Spread
Sticky regex
Template literals
Trailing function commas
Type annotations
Unicode regex 
```

Enter fullscreen mode Exit fullscreen mode

#### 需要多点填充的特征

```
ArrayBuffer
Array.from
Array.of
Array#copyWithin
Array#fill
Array#find
Array#findIndex
Function#name
Map
Math.acosh
Math.hypot
Math.imul
Number.isNaN
Number.isInteger
Object.assign
Object.getOwnPropertyDescriptors
Object.is
Object.entries
Object.values
Object.setPrototypeOf
Promise
Reflect
RegExp#flags
Set
String#codePointAt
String#endsWith
String.fromCodePoint
String#includes
String.raw
String#repeat
String#startsWith
String#padStart
String#padEnd
Symbol
WeakMap
WeakSet 
```

Enter fullscreen mode Exit fullscreen mode

因为在你的项目中添加新特性并不经常发生，所以我认为重要的是理解这两个概念背后的一般规则，而不是试图记住什么是编译的，什么是聚合填充的，然后如果你需要知道你是否应该为一个特定的特性包含一个聚合填充，请查看 [Babel 的网站](https://babeljs.io/)。