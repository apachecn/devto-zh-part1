# 编码的理由

> 原文：<https://dev.to/kayis/a-reason-to-code>

几个月前，我读到一种叫做 [Reason](https://reasonml.github.io/) 的新编程语言。它是作为一个更好的 javascript 卖给我的，就像许多编译到 JavaScript 的语言一样。我们见过他们， [CoffeeScript](http://coffeescript.org/) ， [LiveScript](http://livescript.net/) ， [Elm](http://elm-lang.org/) ， [ClojureScript](https://github.com/clojure/clojurescript) 。所以我有点不愿意去研究它，但是几天前我开始阅读它，我被震撼了。

## 原因是什么？

Reason 是一种可以编译成 JavaScript 和 native 的语言，就像你从 C/C++中知道的那样。因此，你可以用它来编写 Node.js 应用程序，或者你的前端，或者编写一个普通的原生应用程序，就像人们过去做的那样；)

关于理性有趣的事情是，它并不完全是新的。基本上可以看作是 [OCaml](http://ocaml.org/) 的新语法。所以你可以把 Reason 转换成 OCaml 再转换回来，没有任何损失。

为什么是新语法？嗯，我想目标群体是 web 开发人员，所以他们想让它看起来更像 JavaScript。

例如，JavaScript 为数组扩展了语法:

```
 const x = 1;
    const a = [2, 3, 4];
    const b = [x, ...a]; // -> [1,2,3,4]; 
```

Enter fullscreen mode Exit fullscreen mode

用 OCaml 做这件事看起来是这样的:

```
 let x = 1
    let a = [2; 3; 4]
    let b = x :: a 
```

Enter fullscreen mode Exit fullscreen mode

理智地这样做看起来是这样的:

```
 let x = 1;
    let a = [2, 3, 4];
    let b = [x, ...a]; 
```

Enter fullscreen mode Exit fullscreen mode

## 如何与 JavaScript 配合使用？

OCaml 编译器有一个插件系统，你可以为它实现你自己的*后端*来创建你需要的输出。因此，[彭博](https://www.bloomberg.com/europe)的开发人员构建了 [BuckleScript](https://bucklescript.github.io/bucklescript/) ，这是输出 JavaScript 的 OCaml 编译器的后端。

现在，人们对此并不太感兴趣，因为，正如我上面提到的，许多语言以前都是这样做的，OCaml 也没有太熟悉的语法，但是 Reason 的创建者发现 OCaml 编译器相当不错，并希望为 Web 利用这种能力，所以他们开始创建这种新的语法，同时试图获得完整的 OCaml 兼容性。

所以发生的事情是，你编写 Reason，将其转换成 OCaml(无损)并在 BuckleScript 的帮助下编译成 JavaScript。据我所知，它是最快的 to-javascript 编译器之一，它甚至能生成人类可读的代码，这很有趣，因为它能从 OCaml 字节码编译成 javascript。

## 为什么有人要在意？

OCaml 非常强大。

首先，[它有一个可靠的静态类型系统](https://reasonml.github.io/guide/language/type/#design-decisions)，可以给出非常好的错误消息，比如 Elm 级别的错误，比如“你写了 X，你的意思是 Y 吗？”，但是的，有些人不太关心这个，那些已经在使用 Elm、Flow 或 TypeScript 的人。(btw。流程是在 OCaml 中构建的)

第二，[为模块](https://reasonml.github.io/guide/language/module#every-re-file-is-a-module)的隐式导入和导出。你知道我们现在在 JavaScript 中得到的所有导入吗？如果你不使用 IDE 来折叠你的导入，今天更复杂的 JS 文件会显示一个导入列表，你必须向下滚动才能看到真正的*T4 代码。此外，我们经常忘记进口一些东西，一切都搞砸了。OCaml 为您做了这一切。每个`.re`文件都是一个模块，并导出其所有顶层变量。* 

```
 /* MyModule.re */
    let a = 1;
    let b = 2;
    {
      let notExported = 3;
    }

    /* MyOtherModule.re */
    let a = MyModule.a;
    let b = MyModule.b;
    let c = MyModule.notExported; // -> error 
```

Enter fullscreen mode Exit fullscreen mode

第三，OCaml 计算出它能在编译时**计算出什么值** *和***消除死代码**。这就像打了类固醇的摇树。Prepack 试图为 JavaScript 做到这一点。

例如这个原因代码:

```
 let z = {
      let x = 10;
      let y = 20;
      x + y
    } 
```

Enter fullscreen mode Exit fullscreen mode

可以天真地转换成这个 JavaScript:

```
 let z;
    {
      const x = 10;
      const y = 20;
      z = x + y;
    }
    exports.z = z; 
```

Enter fullscreen mode Exit fullscreen mode

但它实际上转换成(类似于)这样:

```
 var z = 30;
    exports.z = z; 
```

Enter fullscreen mode Exit fullscreen mode

它会抛出你不使用的模块，因为你自己从来不会导入它们，OCaml 会帮你做，然后它还会尝试预先计算值并抛出死代码。

最后，它已经包含了类似 ESLint T1 和 T2 beautiful T3(灵感来自 Reason formatter ref mt)的特性。代码提示和自动格式化，因此您不必再考虑缩进或分号。

另外，[它甚至有一个类似 JSX 的语法](https://reasonml.github.io/guide/language/jsx)，这对 React 开发者来说是一个巨大的胜利，它甚至比 JSX 还要轻一点。

```
 // JavaScript JSX
    <Component key={a} ref={b} foo={c} d={d}> {child1} {child2} </Component> 
    // Reason JSX
    <Component key=a ref=b foo=c d> child1 child2 </Component> 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有一些更小的东西有助于日常编码，如命名函数参数或使用类型系统而不是字符串进行决策，最终编译成布尔值或数字而不是使用字符串等。

## 结论

理性似乎是一个非常有趣的软件。它解决了我在日常 JavaScript 编码中看到的一系列问题，这让我觉得它是一种实用的语言。当我听说它基本上是 OCaml - >一种函数式编程语言时，我并没有这样想。

所以我认为这值得一试，至少对我的私人项目来说。