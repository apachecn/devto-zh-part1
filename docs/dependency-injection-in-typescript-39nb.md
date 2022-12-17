# TypeScript 中的依赖注入

> 原文：<https://dev.to/nehalist/dependency-injection-in-typescript-39nb>

[![Dependency Injection in TypeScript](img/7d3c44ba01212f9eb9a70ce094add8c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--81WJTVo---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ghost.nehalist.io/conteimg/2019/06/di-ts.png)

我非常喜欢成熟框架的一点是，它们都实现了某种依赖注入。最近，我在 TypeScript 中试用了这项技术，以便更好地理解*如何在表面下工作。*

## 什么是依赖注入(DI)？

如果你不知道 DI 是什么，我强烈推荐[和它联系](https://en.wikipedia.org/wiki/Dependency_injection)。既然这个帖子不应该是关于*什么的？*但更多关于*的情况如何？*在这一点上，让我们尽量保持简单:

> 依赖注入是一种技术，通过它一个对象提供另一个对象的依赖。

*引自[维基](https://en.wikipedia.org/wiki/Dependency_injection)T3】*

那是什么意思？你的软件的某个*部分*(通常被称为*注入器*)负责构建对象，而不是手动构建你的对象。

想象下面的代码:

```
class Foo {
}

class Bar {
  foo: Foo;

  constructor() {
    this.foo = new Foo();
  }
}

class Foobar {
  foo: Foo;
  bar: Bar;

  constructor() {
    this.foo = new Foo();
    this.bar = new Bar();
  }
} 
```

这是*不好的*原因有很多，比如类之间有直接的、不可交换的依赖关系，测试真的很难*真的很难*，遵循你的代码变得很难，组件的重用变得更难，等等..另一方面，依赖注入*将*依赖注入到你的构造函数中，使得所有这些*不好的*事情都过时了:

```
class Foo {
}

class Bar {
  constructor(foo: Foo) {
  }
}

class Foobar {
  constructor(foo: Foo, bar: Bar) {
  }
} 
```

*更好的*。

要获得`Foobar`的实例，您需要按照以下方式构建它:

```
const foobar = new Foobar(new Foo(), new Bar(new Foo())); 
```

*不酷。*

通过使用负责创建对象的*注入器*，你可以简单地做一些事情，比如:

```
const foobar = Injector.resolve<Foobar>(Foobar); // returns an instance of Foobar, with all injected dependencies 
```

*更好。*

关于*为什么*你应该依赖注入有很多理由，包括可测试性、可维护性、可读性等等..再说一遍，如果你还不知道，那是时候学习一些重要的东西了。

## 依赖注入在 TypeScript 中

这篇文章是关于我们自己的(也是最基本的)`Injector`的实现。如果你只是在寻找一些现有的解决方案来在你的项目中使用 DI，你应该看看 [InversifyJS](http://inversify.io/) ，一个非常简洁的用于 TypeScript 的 IoC 容器。

在这篇文章中，我们要做的是实现我们自己的注入器类，它能够通过注入所有必要的依赖项来解析实例。为此，我们将实现一个定义我们的服务的`@Service`装饰器(如果你习惯使用 Angular，你可能会知道这个是`@Injectable`),以及将解析实例的实际的`Injector`。

在深入实现之前，您可能需要了解一些关于 TypeScript 和 DI 的知识:

### 倒影和装饰者

我们将使用 [reflect-metadata](https://www.npmjs.com/package/reflect-metadata) 包在运行时获得[反射功能](https://en.wikipedia.org/wiki/Reflection_(computer_programming))。有了这个包，就有可能获得关于一个类是如何实现的信息——一个例子:

```
const Service = () : ClassDecorator => {
  return target => {
    console.log(Reflect.getMetadata('design:paramtypes', target));
  };
};

class Bar {}

@Service()
class Foo {
  constructor(bar: Bar, baz: string) {}
} 
```

这将记录:

```
[[Function: Bar], [Function: String] ] 
```

因此，我们确实知道需要注入哪些依赖项。如果你不明白为什么这里的`Bar`是一个`Function`:我将在下一节讨论这个问题。

**重要的**:需要注意的是，没有装饰者的*类没有任何元数据。这似乎是`reflect-metadata`的设计选择，尽管我不确定[背后的原因](https://stackoverflow.com/questions/48547005/why-is-reflect-metadata-only-working-when-using-a-decorator/)。*

### 的类型`target`

一开始我很困惑的一件事是我的`Service`装饰器的`target`类型。`Function`看起来很奇怪，因为它显然是一个`object`而不是一个函数。但那是因为 JavaScript 的工作方式；类只是[的特殊功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) :

```
class Foo {
    constructor() {
        // the constructor
    }
    bar() {
        // a method
    }
} 
```

变成了

```
var Foo = /** @class */ (function () {
    function Foo() {
        // the constructor
    }
    Foo.prototype.bar = function () {
        // a method
    };
    return Foo;
}()); 
```

编译后。

但是`Function`不是我们想要用于类型的东西，因为它太通用了。因为此时我们没有处理实际的实例，所以我们需要一个类型来描述用`new` :
调用我们的目标后得到的类型

```
interface Type<T> {
  new(...args: any[]): T;
} 
```

`Type<T>`能够告诉我们一个对象的实例是什么——或者换句话说:当我们用`new`调用它时，我们得到了什么。回头看看我们的`@Service`装饰器，实际类型应该是:

```
const Service = () : ClassDecorator => {
  return target => {
    // `target` in this case is `Type<Foo>`, not `Foo`
  };
}; 
```

这里困扰我的一件事是`ClassDecorator`，它看起来像这样:

```
declare type ClassDecorator = <TFunction extends Function>(target: TFunction) => TFunction | void; 
```

这很不幸，因为我们现在已经知道了对象的类型。为类装饰者获得一个更加灵活和通用的类型:

```
export type GenericClassDecorator<T> = (target: T) => void; 
```

### 接口编译后消失

因为接口不是 JavaScript 的一部分，所以在你的 TypeScript 被编译后它们就消失了。没什么新的，但这意味着我们**不能**使用接口进行依赖注入。一个例子:

```
interface LoggerInterface {
  write(message: string);
}

class Server {
  constructor(logger: LoggerInterface) {
    this.logger.write('Service called');
  }
} 
```

我们的注入器没有办法知道在这里注入什么，因为接口在运行时已经消失了。

这实际上是一个遗憾，因为这意味着我们总是需要类型提示我们的真正的类，而不是接口。尤其是在测试的时候，这可能会变得非常不幸。

有变通的办法，比如用类代替接口(这感觉很奇怪，并且剥夺了接口的意义)或者类似
的东西

```
interface LoggerInterface {
  kind: 'logger';
}

class FileLogger implements LoggerInterface {
  kind: 'logger';
} 
```

但是我真的不喜欢这种方法，因为它是多余的，而且很难看。

### 循环依赖带来麻烦

如果你想做类似于:
的事情

```
@Service()
class Bar {
  constructor(foo: Foo) {}
}

@Service()
class Foo {
  constructor(bar: Bar) {}
} 
```

你会得到一个`ReferenceError`，告诉你:

```
ReferenceError: Foo is not defined 
```

原因很明显:`Foo`在 TypeScript 试图获取关于`Bar`的信息时并不存在。

我不想在这里详述，但是一个可能的解决方法是实现类似 Angulars [forwardRef](https://github.com/angular/angular/blob/master/packages/core/src/di/forward_ref.ts) 的东西。

## 实现我们自己的注入器

好了，理论到此为止。让我们实现一个非常基本的注入器类。

我们将使用从上面学到的所有东西，从我们的`@Service`装饰师开始。

### `@Service`装潢师

我们将修饰所有的服务，否则它们不会发出元数据(使得不可能注入依赖)。

```
// ServiceDecorator.ts

const Service = () : GenericClassDecorator<Type<object>> => {
  return (target: Type<object>) => {
    // do something with `target`, e.g. some kind of validation or passing it to the Injector and store them
  };
}; 
```

### `Injector`

注入器能够解析请求的实例。它可能有额外的功能，比如存储解析的实例(我喜欢称它们为*共享实例*)，但是为了简单起见，我们现在尽可能简单地实现它。

```
// Injector.ts

export const Injector = new class {
  // Injector implementation
}; 
```

导出常量而不是类(比如`export class Injector [...]`)的原因是我们的注入器是一个[单例](https://en.wikipedia.org/wiki/Singleton_pattern)。否则我们永远不会得到我们的`Injector`的同一个实例，这意味着每次你`import`这个注入器，你都会得到一个没有注册服务的实例。(*像每个单身族一样，这也有一些缺点，尤其是在测试方面。*)

我们需要实现的下一件事是解析实例的方法:

```
// Injector.ts

export const Injector = new class {
  // resolving instances
  resolve<T>(target: Type<any>): T {
    // tokens are required dependencies, while injections are resolved tokens from the Injector
    let tokens = Reflect.getMetadata('design:paramtypes', target) || [],
        injections = tokens.map(token => Injector.resolve<any>(token));

    return new target(...injections);
  }
}; 
```

就是这样。我们的`Injector`现在能够解析请求的实例。让我们回到开头的例子(现在稍微扩展了)并通过`Injector` :
解决它

```
@Service()
class Foo {
  doFooStuff() {
    console.log('foo');
  }
}

@Service()
class Bar {
  constructor(public foo: Foo) {
  }

  doBarStuff() {
    console.log('bar');
  }
}

@Service()
class Foobar {
  constructor(public foo: Foo, public bar: Bar) {
  }
}

const foobar = Injector.resolve<Foobar>(Foobar);
foobar.bar.doBarStuff();
foobar.foo.doFooStuff();
foobar.bar.foo.doFooStuff(); 
```

控制台输出:

```
bar
foo
foo 
```

这意味着我们的`Injector`成功地注入了所有的依赖项。 *Wohoo！*

### 结论

依赖注入是一个你绝对应该利用的强大工具。这篇文章是关于*DI 是如何工作的，应该会让你对如何实现你自己的注入器有所了解。*

还有很多事情要做。举几个例子:

*   错误处理
*   处理循环依赖关系
*   存储已解析的实例
*   能够注入不止一个构造函数标记
*   等等。

但基本上这就是注射器的工作原理。

和往常一样，完整的代码(包括例子和测试)可以在 [GitHub](https://github.com/nehalist/di-ts) 上找到。

* * *

如果你喜欢这篇文章，请留下❤，在 Twitter 上关注我，订阅我的时事通讯。本帖原载于 2018 年 2 月 5 日 [nehalist.io](https://nehalist.io/dependency-injection-in-typescript/) 。