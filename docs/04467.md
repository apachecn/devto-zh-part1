# 介绍 TypeScript(有一节是关于 JSX 的)

> 原文：<https://dev.to/createdd/introducing-typescript-with-a-section-on-jsx-lj>

[![](img/e3676b85736a03020c88a09ea291d5fb.png)](https://unsplash.com/photos/4Cjn0FDEud8) 
*照片由[【关于非致命性】](https://unsplash.com/photos/4Cjn0FDEud8)* 拍摄

由于我越来越高级，是时候研究一下 Typescript 了。不是因为 JavaScript 代码的简单林挺，而是因为静态类型的概念。本文提供了一个介绍。

为了提供一定的质量标准，我主要参考并引用以下资料:

*   [微软](https://github.com/Microsoft/TypeScript/wiki/TypeScript-Design-Goals)的 Github 库和 wiki(根据 [Apache License 2.0](https://github.com/Microsoft/TypeScript-Handbook/blob/master/LICENSE) 授权)
*   Basarat Ali Syed 及其贡献者的打字稿 deep dive git book(根据[知识共享 4.0](https://creativecommons.org/licenses/by/4.0/) 获得许可)

* * *

> "缺乏证明文件正成为验收的一个问题."-维泽·韦内马

* * *

## 它是什么，它驶向何方？

TypeScript 背后的想法是为 JavaScript 提供一个可选的类型系统。

它通过以下方式提高代码质量

*   在进行重构时增加你的灵活性。对于编译器来说，在运行时捕捉错误比让事情失败要好。
*   类型是你能拥有的最好的文档形式之一。函数签名是定理，函数体是证明。

> 您的 JavaScript 代码。js 文件可以被重命名为. ts 文件，TypeScript 仍然会返回有效。js 等同于原始的 JavaScript 文件。TypeScript 是带有可选类型检查的 JavaScript 的超集。

它的目标(根据官方文件)是:

*   静态识别可能出错的结构。
*   为较大的代码片段提供结构化机制。
*   不对发出的程序强加运行时开销。
*   发出干净、惯用、可识别的 JavaScript 代码。
*   创造一种可组合且易于推理的语言。
*   与当前和未来的 ECMAScript 提案保持一致。
*   保留所有 JavaScript 代码的运行时行为。
*   避免添加表达式级语法。
*   使用一致的、完全可擦除的结构化类型系统。
*   做一个跨平台的开发工具。
*   不要对 TypeScript 1.0 进行重大更改。

## 概念

* * *

> 本质上，TypeScript 是林挺 JavaScript。只是比其他没有类型信息的 linters 做得更好。
> 
> * * *

#### 基础知识类型

使用`:TypeAnnotation`语法对类型进行注释。(例如`var num: number = 123;`

*   Boolean ( `let isDone: boolean = false;` )
*   号码(`let decimal: number = 6;`)
*   字符串(`let color: string = "blue";`)
*   数组(`let list: number[] = [1, 2, 3];`或`let list: Array<number> = [1, 2, 3];`)
*   元组(`let x: [string, number]; x = ["hello", 10];`)
*   枚举(`enum Color {Red, Green, Blue} let c: Color = Color.Green;`)
*   Any(退出类型检查，让一些值通过编译时检查)
*   Void(没有任何类型)
*   Null / Undefined(是所有其他类型的子类型。这意味着您可以将 null 和 undefined 赋给类似 number 的值)
*   Never(函数表达式或箭头函数表达式的返回类型，总是抛出异常或从不返回)

#### 界面

接口是 TypeScript 中将多个类型批注组合成单个命名批注的核心方法。

```
interface Name {
    first: string;
    second: string;
}

var name: Name;
name = {
    first: 'John',
    second: 'Doe'
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 仿制药

在 C#和 Java 这样的语言中，工具箱中用于创建可重用组件的主要工具之一是泛型，也就是说，能够创建一个可以处理多种类型而不是单一类型的组件。

不带泛型:

```
function identity(arg: number): number {
    return arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然使用 any 肯定是通用的，因为它将接受 arg 类型的任何和所有类型，但当函数返回时，我们实际上正在丢失有关该类型的信息。

带泛型:

```
function identity<T>(arg: T): T {
    return arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

t 允许我们捕获用户提供的类型(例如，数字)，以便我们以后可以使用这些信息。

#### 路口

`extend`是 JavaScript 中的一种模式，你获取两个对象，然后创建一个具有这两个对象特性的新对象。

交叉点允许定义这些对象。

```
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U> {};
//some code
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 和 JSX 反应过来

#### 设置

包含 JSX 的文件必须以`.tsx`结尾，而不是只有`.ts`才能正确传输。

根据项目设置，您可以启用三种 [JSX 模式:保留、反应和反应-原生](https://github.com/Microsoft/TypeScript-Handbook/blob/master/pages/JSX.md#basic-usage)。

#### 概念

> React 既可以呈现 HTML 标签(字符串)，也可以反应组件(类)。这些元素的 JavaScript 发出是不同的(React.createElement('div ')与 react . createelement(my component))。这是由第一个字母的大小写决定的。foo 被视为一个 HTML 标签，而 Foo 被视为一个组件。

React 呈现字符串或类的事实对于 TypeScript 来说是必不可少的。

> 为了理解使用 JSX 的类型检查，您必须首先理解内在元素和基于值的元素之间的区别。给定一个 JSX 表达式`<expr />` , `expr`可能是指环境中固有的东西(例如 DOM 环境中的 div 或 span ),也可能是指您创建的自定义组件。

内部元素可以用接口来检查，比如

```
declare namespace JSX {
    interface IntrinsicElements {
        foo: any
    }
}

<foo />; // ok
<bar />; // error 
```

Enter fullscreen mode Exit fullscreen mode

而基于值的元素在它们自己的范围内被识别，如

```
import MyComponent from "./myComponent";

<MyComponent />; // ok
<SomeOtherComponent />; // error 
```

Enter fullscreen mode Exit fullscreen mode

因此，

> 基于组件的 props 属性对组件进行类型检查。

例如:

```
interface Props {
  foo: string;
}
class MyComponent extends React.Component<Props, {}> {
    render() {
        return <span>{this.props.foo}</span>
    }
}

<MyComponent foo="bar" /> 
```

Enter fullscreen mode Exit fullscreen mode

检查内在元素的属性类型是:

```
declare namespace JSX {
  interface IntrinsicElements {
    foo: { bar?: boolean }
  }
}

// element attributes type for 'foo' is '{bar?: boolean}'
<foo bar />; 
```

Enter fullscreen mode Exit fullscreen mode

而基于值的元素的属性检查如下:

```
declare namespace JSX {
  interface ElementAttributesProperty {
    props; // specify the property name to use
  }
}

class MyComponent {
  // specify the property on the element instance type
  props: {
    foo?: string;
  }
}

// element attributes type for 'MyComponent' is '{foo?: string}'
<MyComponent foo="bar" /> 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读我的文章！欢迎留下任何反馈！