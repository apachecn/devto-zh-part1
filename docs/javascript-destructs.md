# JavaScript 析构

> 原文：<https://dev.to/kayis/javascript-destructs>

在这篇文章中，我将谈论 ES2015 添加到 JavaScript 中的一个特性:**析构**

我还将谈论 ES2015 添加到*初始化符号*的特性，也称为对象或数组文字，这是故事的*另一面*。

我将介绍一些(目前)还不属于 ECMAScript 标准的特性，但在不久的将来，它们将成为 ECMAScript 标准的一部分。像 Babel 这样的编译器已经实现了它们。

# 解构

析构是一种基于对象或数组声明新变量的方法，它们通常可以消除对一些间接变量的需要。

让我们从一个简单的例子开始。

```
// Without destructuring you would write this:

const someObject = {head: "ok", value: 11};

if (someObject.head == "ok" && someObject.value > 10) doSomething();

// With destructuring you could write it like that:

const {head, value} = {head: "ok", value: 11};

if (head == "ok" && value > 10) doSomething(); 
```

Enter fullscreen mode Exit fullscreen mode

为了增加更多的意义，让我们想象你从一个函数中得到`someObject`:

```
const {head, value} = getSomeObject();

if (head == "ok" && value > 10) doSomething(); 
```

Enter fullscreen mode Exit fullscreen mode

或者把它作为回调的参数:

```
function (otherArgument, someObject, anotherArgument) {

  const {head, value} = someObject;

  if (head == "ok" && value > 10) doSomething();

} 
```

Enter fullscreen mode Exit fullscreen mode

这可以简化为:

```
function (otherArgument, {head, value}, anotherArgument) {

  if (head == "ok" && value > 10) doSomething();

} 
```

Enter fullscreen mode Exit fullscreen mode

析构也允许你*更深入*:

```
const { a: { b: { c } } } = { a: { b: { c: 10 } } };

alert(c); 
```

Enter fullscreen mode Exit fullscreen mode

正如我提到的，它也适用于数组:

```
const [firstElement, SecondElement] = [ 1, 2, 3 ];

alert(firstElement + SecondElement); 
```

Enter fullscreen mode Exit fullscreen mode

作为论据:

```
function ([first, second]) {

  return first + second;

} 
```

Enter fullscreen mode Exit fullscreen mode

交换一些没有代理变量的元素:

```
const a = 1;
const b = 2;

[b, a] = [a, b]; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，当使用数组时，你可以自由地使用任何你想要的标识符，当使用对象时，你必须使用正确的属性标识符。

Arrays destructuring 也有一个 *rest* 语法，它允许您提取一些元素并用剩余的元素快速创建一个新的数组。

```
const [stringWithOne, ...arrayWithTwoAndThree] = ["one", "two", "three"]; 
```

Enter fullscreen mode Exit fullscreen mode

甚至还有针对对象的 rest 语法，这还不是 ECMAScript 的一部分，但可以通过 Babel 等编译器获得。

```
const {a, ...objWithBAndC} = {a: 10, b: 11, c: 12}; 
```

Enter fullscreen mode Exit fullscreen mode

将它用于函数参数的一个好处是，您可以将它与默认值特性一起使用。所以你可以从一个对象或数组中提取正确的值*并*一次性为它们设置默认值:

```
// Before
function parse(a) {
  const type = a.type || "default"
  ...
}

// After
function parse({type = "default"}) {
  ...
}

// More complex
function parse(a = 10, [first, second, ...rest], {b: {c: [deepFirst = 99]}}) {
  return rest.map(function(x) {
    return a * first * second * x + deepFirst;
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

还有一个专门针对参数的 rest 语法，可以在没有对象或数组的情况下使用。它作为`arguments`的替代，是一个*真正的*阵列。

```
function (a, b, ...otherArgs) {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 初始值设定项符号

另一方面是初始化符号，通常也被称为对象字面语法，这有一点点调整。

你从某个地方提取了数据，现在你想用它创建一个新的对象。

```
 // Before
 const a = 10;
 const b = 11;

 const myObject = {a: a, b: b};

 // After
 const a = 10;
 const b = 11;

 const myObject = {a, b};

 // Like it worked with arrays before:
 const myArray = [a, b]; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您还可以在文本中使用变量值作为对象的名称:

```
 // Before
 const key = "hello";
 const value = "world";

 const newObject = {};
 newObject[key] = value;

 // After
 const key = "hello";
 const value = "world";

 const newObject = {

 };

 // With some computation
 const newObject = {

 } 
```

Enter fullscreen mode Exit fullscreen mode

对象的 rest 语法也有一个反转。 *spread* 操作符，它还不是 ECMAScript 的一部分。它允许您动态创建新对象。

```
 const props = {size: 10, color: "red"};
 const newProps = {...props, color: "green", weight: 100}; 
```

Enter fullscreen mode Exit fullscreen mode

这样你就可以基于其他对象创建对象，并且可以添加属性(比如`weight`)，或者覆盖基础对象的属性(比如`color`)。

但是还是要小心一点。你得到了一个新的对象，**但是**如果你在基础对象(嵌套对象)中得到一些间接对象，这些不会被克隆。