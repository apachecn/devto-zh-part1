# ES 模块:默认导入不是名称空间导入！

> 原文：<https://dev.to/mapleleaf/es6-modules-and-default-imports-p0>

这里我们有两个导入语句:

```
import React from 'react'
import * as React from 'react' 
```

Enter fullscreen mode Exit fullscreen mode

我在很多书面代码中都看到了这两种情况，前者比后者更常见。他们看起来完成了同样的结果，但是取决于很多因素，他们最终会做完全不同的事情。我写这篇文章是希望澄清这两者之间的区别，尤其是对于那些可能没有意识到甚至有区别的人。

## 名称空间导入

命名空间导入将所有导出的变量从一个文件导入到一个名称下。例如:

```
// my-module.js
export const foo = 123
export const bar = 456

// main.js
import * as stuff from './my-module'

// stuff is: { foo: 123, bar: 456 } 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？这里不多解释了。

## 默认导入

另一方面，默认导入从另一个模块导入特定的默认导出。也就是在导入的模块中导出为`export default`的变量。

```
// my-module.js
export default {
  foo: 123,
  bar: 456,
}

// main.js
import stuff from './my-module'

// stuff is: { foo: 123, bar: 456 } 
```

Enter fullscreen mode Exit fullscreen mode

默认导出的目的是为从另一个模块中导入单个功能单元提供一个有吸引力的接口:单个类、单个实用函数等等。默认导出意味着促进单一责任模块的实践。

导出的变量通常在其变量名下标记。`export const foo = 123`以`foo`的名称导出，也可以以`foo`的名称导入。

模块的默认导出被标记在名称`default`下。考虑到这一点，我们可以像这样重写前面的例子来获得相同的结果:

```
// my-module.js
export const default = {
  foo: 123,
  bar: 456,
}

// main.js
import { default as stuff } from './my-module'

// stuff is: { foo: 123, bar: 456 } 
```

Enter fullscreen mode Exit fullscreen mode

结论:默认值不是一个神奇地隐藏在模块正常导出旁边的值。默认只是建立在正常导出的基础上的语法糖。

然而...

## 默认导入是*而不是*命名空间导入

...也不应该如此对待。示例:

```
// my-module.js
export const foo = 123
export const bar = 456

// main.js
import stuff from './my-module' 
```

Enter fullscreen mode Exit fullscreen mode

import 语句正在寻找默认导出以用作`stuff`，但是模块没有任何默认导出。脚本会失败，抱怨在`my-module.js`中找不到`default`。

...除此之外，如果你曾经使用过 babel、webpack 或其他打包器/传输器，你会知道它有时确实有效。为什么？

大多数模块都是用 CommonJS 编写的，因此，transpilers 和 bundlers 使用各种策略来解决差异，这样您就可以无缝地一起使用不同的模块系统。最常见的策略是从 CommonJS 模块中获取`module.exports`对象，并将其视为默认导出。

简而言之，在今天的大多数构建管道中，如果将`my-module.js`编写为 CommonJS 模块，前面的例子将会如您所期望的那样工作。

另外，当从 ES 模块传输到 CommonJS 模块时，transpilers 会向`module.exports`对象添加一个特殊的`__esModule`属性。这告诉模块加载器，原来的模块是作为 ES 模块编写的，应该作为 ES 模块使用。因此，所描述的`module.exports`到`default`策略只适用于最初用 CommonJS 编写的库。

## 那么，我该如何导入反应呢？

[从 React 回购:](https://github.com/facebook/react/blob/743201387246d0cde523700c151550786f0afc2e/packages/react/src/React.js#L75)

```
export default React; 
```

Enter fullscreen mode Exit fullscreen mode

很多代码使用默认的导出，所以这看起来是他们在这里使用的。考虑到事物的结构，我个人会选择一个命名空间的导出，但事实就是如此。Â \_(ãƒ„)_/Â

## 结论

知道你的模块的导出，知道它使用哪个模块系统，并且知道是否有默认的导出。如果你想了解更多，这里有一篇关于 ES 模块的好文章。

如果我错过了什么，做错了什么，或者说得不清楚，请随时发表评论。这是我在这里的第一篇文章，所以非常感谢反馈。谢了。