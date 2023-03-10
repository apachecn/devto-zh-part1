# frontjs 的依赖注入

> 原文：<https://dev.to/fahad19/dependency-injection-with-frintjs-1oki>

> *本文首次发表于[媒体](https://medium.com/frintjs/dependency-injection-with-frintjs-cbc4861987fa)。*

[![frintjs-dependency-injection](img/a2dc78eadf9dae7c444eb82494c806bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v03msVPe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wo5qsegdzqzi1o2ggpmo.png)

> 对于那些还不知道的人来说， [FrintJS](https://github.com/frintjs/frint) 是一个环境不可知的框架，用于构建支持 **React** 和 **Vue.js** 的可扩展反应式应用。

自 2017 年初 v1 发布以来， [FrintJS](https://frint.js.org) 框架的核心主要负责处理应用程序中的依赖关系。而其他包主要是围绕核心 frint 包的 API 构建的。

## 提供者

我们对 FrintJS 应用中的依赖关系有一个术语。我们称他们为[供应商](https://frint.js.org/docs/packages/frint/#understanding-providers)。提供者是您可以在 FrintJS 应用程序中定义的任何值，稍后可以通过它们唯一的名称来获取。

创建一个 FrintJS 应用程序非常简单:

```
import { createApp } from 'frint';

const MyApp = createApp({
  name: 'MyAppName',
}); 
```

Enter fullscreen mode Exit fullscreen mode

下面我们将向您展示如何在您的应用程序中以三种不同的方式定义提供者。

> 你会发现与 Angular 2+有一些相似之处，那是因为`frint`使用了一个 [`diyai`](https://github.com/fahad19/diyai) 的分支，这是 Angular 的注入器 API 的一个端口，以一种不需要类型脚本就可以使用的方式构建

### 已知值

如果您已经知道您的提供者的值，您可以像这样使用`useValue`属性:

```
import { createApp } from 'frint';

const MyApp = createApp({
  name: 'MyAppName',
  providers: [
    {
      name: 'foo',
      useValue: 'foo value here',
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，一旦实例化了您的应用程序，您就可以获得如下的`foo`值:

```
const app = new MyApp();

const foo = app.get('foo'); // `foo value here` 
```

Enter fullscreen mode Exit fullscreen mode

### 生成值

有些时候，你并不真正知道你的提供者的价值，只能在应用程序本身被实例化的时候产生。`useFactory`属性可用于以下场景:

```
import { createApp } from 'frint';

const MyApp = createApp({
  name: 'MyAppName',
  providers: [
    {
      name: 'bar',
      useFactory: function () {
        return 'bar value here';
      },
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在可以从您的应用程序实例中获得`bar`提供者，如下所示:

```
const app = new MyApp();

const bar = app.get('bar'); // `bar value here` 
```

Enter fullscreen mode Exit fullscreen mode

请注意，生成`bar`值的函数在应用程序实例化期间只被调用一次。之后，它会从应用程序内部所有提供者的注册表中不断返回相同的缓存值。

### 从 ES6 类生成

也有这样的情况，你可以先把你的提供者写成 ES6 类:

```
class Baz {
  getValue() {
    return 'baz value here';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

要将 ES6 类设置为提供者，我们可以在 FrintJS 应用程序中定义它们时使用`useClass`属性:

```
import { createApp } from 'frint';

const MyApp = createApp({
  name: 'MyAppName',
  providers: [
    {
      name: 'baz',
      useClass: Baz,
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当您的应用程序被实例化时，它也会实例化`Baz`类，并将实例设置为 baz provider 的值。

```
const app = new MyApp();

const baz = app.get('baz'); // instance of Baz class
console.log(baz.getValue()); // `baz value here` 
```

Enter fullscreen mode Exit fullscreen mode

与`useFactory`类似，该类将在应用程序的实例化过程中仅实例化一次，并在您每次执行`app.get('baz')`时返回相同的缓存值。

### 在其他提供者中注入提供者

既然我们已经了解了如何在 FrintJS 应用程序中以各种方式定义提供者，我们可以进一步深入到高级用例中，其中一个提供者可能依赖于另一个提供者。

从上面的例子中，假设`bar`提供者需要知道`foo`的值。我们如何将`foo`注入`bar`？

我们可以使用`deps`(依赖关系的简称)属性:

```
import { createApp } from 'frint';

const MyApp = createApp({
  name: 'MyAppName',
  providers: [
    {
      name: 'foo',
      useValue: 'foo value here',
    },
    {
      name: 'bar',
      useFactory: function (deps) {
        const { foo } = deps; // `foo value here`
        return foo + ', bar value here';
      },
      deps: ['foo'],
    },
  ],
}); 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们刚才所做的:

*   定义`foo`供应商
*   定义`bar`供应商
*   对于`bar`，将`foo`列为依赖项
*   生成`bar`值的函数现在将接收一个`deps`对象，它的所有依赖项都以它们的名称为关键字。因为我们只将`foo`列为附属地，所以它现在只能接收`foo`。
*   `bar`生成的值现在返回`foo value here, baz value here`。

可以自己试试:

```
const app = new MyApp();

const foo = app.get('foo');
const bar = app.get('bar');

console.log(bar); // `foo value here, bar value here` 
```

Enter fullscreen mode Exit fullscreen mode

您也可以对`useClass`应用类似的技术。然后，`deps`对象将作为它的第一个构造函数参数:

```
class Baz {
  constructor(deps) {
    console.log(deps);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在 frint 软件包的官方文档中进一步了解它:[https://frint.js.org/docs/packages/frint/](https://frint.js.org/docs/packages/frint/)。