# ES6 模块

> 原文：<https://dev.to/tiffany/es6-modules-2bi>

ES6 模块允许我们在单独的文件中拥有可重用的代码。我们这样做是为了消除最初大量的 js 文件，比如 Node 和 Express 项目中的`app.js`。模块允许我们在相关的文件中传播代码。

拿着这个`students.js`文件

```
const students = [‘Peter’, ‘Paul’, ‘Mary’];

export const students = [‘Peter’, ‘Paul’, ‘Mary’]; 
```

Enter fullscreen mode Exit fullscreen mode

导出允许我们将函数、对象和原始值导出到另一个文件中。

在`app.js`中，我们将这些值作为模块导入到主 js 文件:

```
import { students } from ‘./students’; 
```

Enter fullscreen mode Exit fullscreen mode

…其中`./students`表示与`app,js`存储在同一目录中的文件。

## 导入多个值

您可以从一个模块中导入多个值。取`students.js` :

```
export const total = 42; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将这个变量和`students`数组一起导入到`app.js`中:

```
import { students, total } from ‘./students’; 
```

Enter fullscreen mode Exit fullscreen mode

## 导出文件间的数据和函数

您可以在文件之间导出函数*，比如这个`calc.js`文件:* 

```
const add = (x, y) => {
  return x + y;
}

const divide = (x, y) => {
  return x / y;
}

export { add, divide }; 
```

Enter fullscreen mode Exit fullscreen mode

将该模块导入`app.js` :

```
import { add, divide } from ‘./calc’; 
```

Enter fullscreen mode Exit fullscreen mode

导出不仅限于原始数据类型。

## 默认导出回退

如果我们想让一个函数成为模块的主函数呢？

我们可以添加一个`default`关键字来进行回退:

```
export { add, divide };
export default multiply; 
```

Enter fullscreen mode Exit fullscreen mode

这将允许模块在另一个函数失败时退回到另一个函数。

## 注意这个陷阱

做这样的事情可能很诱人:

```
export default const add = (x, y) => {
  return x + y;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将导致类型错误。

为什么？

关键字`default`创建了一个名为`default`的变量。同时添加`default`和`const`，JavaScript 会尝试声明两个不同的变量，这在大多数编程语言中是不允许的。

## 更来了

ES6 是现在的标准，所以我正在尽快学习。让我知道一些你最喜欢的 ES6 的功能。

帖子 [ES6 模块](https://tiffanywhite.tech/es6-modules/)首先出现在 [Tiffany R. White 博客](https://tiffanywhite.tech)上。