# 关于 JSX 你应该知道的 9 件事

> 原文：<https://dev.to/sarah_chima/9-things-you-should-know-about-jsx-3bm>

```
 const hello = <h1>Hello World</h1> 
```

Enter fullscreen mode Exit fullscreen mode

上面那行代码是用 JSX 写的。JS 什么？？

你可能以前遇到过术语 JSX，但不知道它是什么。您可能已经使用过它，或者正在用它创建应用程序。无论如何，这篇文章应该会让你感兴趣。它讨论了关于 JSX 你应该知道的 9 件事。这包括 JSX 是什么以及如何使用它。让我们开始吧。

### JSX 是什么

JSX 是 JavaScript 的语法扩展。基本上，它扩展了 JavaScript，这样类似 HTML/XML 的结构可以和 JavaScript 一起使用。它允许开发人员使用 HTML 语法来编写 JavaScript 组件。这使得用清晰熟悉的语法来定义带有属性的 DOM 树结构成为可能。

虽然它看起来很像 HTML，但它实际上属于一个 JS 文件。因为它是 JavaScript 的扩展，除非它被编译成普通的 JavaScript，否则你的浏览器不会理解它。所以你需要使用 JSX 编译器，比如 Babel。

### JSX 是一个 JavaScript 表达式

JSX 表达式也是 JavaScript 表达式。当编译时，它们实际上变成了常规的 JavaScript 对象。例如，下面的代码:

```
const hello = <h1 className = "greet"> Hello World </h1> 
```

Enter fullscreen mode Exit fullscreen mode

将被编译为

```
 const hello = React.createElement {
       type: "h1",
       props: {
           className: "greet",  
           children: "Hello World" 
          }
    } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，使用了`className`而不是`class`。我很快会解释的。

因为它们被编译成对象，所以只要可以使用常规 JavaScript 表达式，就可以使用 JSX。它可以像上面一样保存在变量中，在循环中使用，作为参数传递给函数，可以从函数中返回，存储在数组和对象中...凡是可以使用 JavaScript 表达式的地方。

### 在 JSX 筑巢

就像 HTML 一样，你可以在一个元素中嵌套几行 JSX。但是要做到这一点，您需要将它们都放在括号()中，如下例所示。

```
 (
       <div>
            <h1> Hello World!!! </h1>
            <p> I am a human </p>
       </div>
    ) 
```

Enter fullscreen mode Exit fullscreen mode

### 请给我一个最外面的

JSX 表达式必须只有一个外部元素。因此，尽管这将起作用

```
 const headings = (
        <div id = "outermost-element">
           <h1>I am a heading </h1>
           <h2>I am also a heading</h1> 
        </div>
    ); 
```

Enter fullscreen mode Exit fullscreen mode

这是行不通的

```
 const headings = (

       <h1>I am a heading </h1>
       <h2>I am also a heading</h1>

      ); 
```

Enter fullscreen mode Exit fullscreen mode

### 为了对骆驼的爱

因为 JSX 比 HTML 更接近 JavaScript，所以 JSX 属性使用 camelCase 命名约定，而不是 HTML 属性名。这就是为什么在第 2 点中，使用了`className`和`tabIndex`而不是`tabindex`。JSX 的事件听众也是如此。在 HTML 中，小写用于事件侦听器，而 camelCase 用于 JSX。

```
 This will work in JSX
    <button onClick = {handleClick}>Click Me</button>

    This will not work in JSX
    <button onclick = {handleClick}>Click Me</button> 
```

Enter fullscreen mode Exit fullscreen mode

### JavaScript 在 JSX

你可以在 JSX 中嵌入 JavaScript，但是要做到这一点，你需要使用花括号。例如，如果你想在 JSX 表达式中添加`2+3`，你可以这样做。

```
 <p> 2+3 = {2+3} </p>

    will result to 

    2+3 = 5 
```

Enter fullscreen mode Exit fullscreen mode

### 显示 JSX

为了让 JSX 出现在浏览器的屏幕上，它必须被渲染。`ReactDOM.render()`是 JSX 最常见的渲染方式。`ReactDOM`是一个 JavaScript 库，包含几个以某种方式处理 DOM 的方法。

`ReactDOM.render()`获取 JSX 表达式，创建相应的树节点并将其添加到 DOM 中。这样你的 JSX 表情就会出现在屏幕上。为了让您的 JSX 出现在屏幕上，您向`ReactDOM.render()`传递两个参数。第一个参数是要渲染的 JSX，可以是原始的 JSX 本身，也可以是包含 JSX 的变量的名称。第二个参数是 html 文件中要将 JSX 追加到的目标元素。
为了更好地理解，请看下面的例子。

```
 const names = (
        <ul>
            <li>Sarah</li>
            <li>Annabel</li>
        </ul>
       );

    ReactDOM.render(names , document.getElementById("app")); 
```

Enter fullscreen mode Exit fullscreen mode

[在 Codepen 上试试](https://codepen.io/sayrah901/pen/dVwbzX)

### JSX 的自动结束标签

对于有自结束标签的 HTML 元素，比如`<img>`、`<hr>`、`<input>`和`br`，结束尖括号前的正斜杠应该包含在 JSX 中。虽然这在 HTML 中是可选的，但在 JSX**却是强制的**。

```
 while this is good in JSX
    <hr />

    this is not good in JSX
    <hr> 
```

Enter fullscreen mode Exit fullscreen mode

### 别忘了钥匙

在 JSX 制作列表时，您的列表应该包含一个名为`Keys`的 JSX 属性。`Keys`类似于`id`,由 React 内部使用来跟踪列表项。如果没有这个，React 可能会打乱列表的顺序。因此，如果您的列表顺序很重要，那么`key`属性也很重要。下面是你如何使用钥匙。

```
 const toDO = (
        <ul>
            <li key = "todo-1"> Design </li>
            <li key = "todo-2"> Develop </li>
            <li key = "todo-3"> Be Awesome </li>
        </ul>
    ); 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了，关于 JSX 你应该知道的 9 件事。我错过什么了吗？我很想知道这件事。