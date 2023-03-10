# 打字头在前

> 原文：<https://dev.to/harry/typescript-head-first>

在这篇文章中，我将介绍一些基本的类型脚本魔法，以及它来自 JavaScript 的自然感觉。如果你像我一样，因为没有时间或认为 JS 就足够了而放弃学习 TS，那就先试试吧。我保证这很棒。

> # 打字稿就是 Java Script 语言而已！

当使用 TypeScript 时，我们可以准确地选择想要使用多少 TS 功能。随着时间的推移，这对于在大型项目中实现 TypeScript 非常有用，因为我们可以将常规 JS 与 TypeScript 一起使用，即使是在相同的文件中。

> # Let's just write some typescripts at the end

您所需要的只是节点和(虽然不是 100%必要，但绝对ðÿ' %有用，VSCode)。首先，创建一个新目录，随你怎么命名。然后运行`sudo npm install typescript -g`，这样我们就可以输入`tsc FILE_NAME.js`来传输我们的`.ts`文件
*是的，有 Gulp、Webpack 和 Browserify 以及大约一百万种其他的东西，我们可以使用它们来自动传输打字稿，但是对于这个练习的目的来说不值得浪费精力*

在你最喜欢的文本编辑器(最好是 vs codeðÿ˜œ)中打开你的新目录，创建一个新文件`test.ts`。在`test.ts`中，我们将声明一个名为 foo 的常量:

```
const foo = 'foo'; 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。！你刚刚写了你的第一行打字稿ðÿž‰
开个玩笑*有点儿*...就像我说的，TypeScript 只是 JavaScript。

对于我们真正的 TypeScript 的第一行，我们将温和地介绍一些很酷的 TS 特性。让我们从强类型开始。

在`test.ts`中写下这两行:

```
let foo: string = 'foo';
foo = 13; 
```

Enter fullscreen mode Exit fullscreen mode

并在您的终端中运行`tsc test.ts --w`(每次文件改变*(在保存时)*时，`--w`将‘监视’文件并运行 TypeScript，这样我们就不需要在每次更新文件时重新键入`tsc test.ts`)。

如果您使用的是 VSCode，您会立即在编辑器中看到:`Type '13' is not assignable to type 'string'`。如果您正在使用另一个没有 TypeScript 插件的编辑器，您将在控制台中看到该消息。打印错误消息是因为我们使用了不兼容的类型(试图将“数字”赋给“字符串”)。

*注意:即使有类型错误，TypeScript 仍然会吐出一个 JS 文件。这是为迁移现有项目的人准备的，因此他们可以在他们的项目中使用 TypeScript，即使他们的一些类型可能不是 100%兼容。更多信息见[这期](https://github.com/Microsoft/TypeScript/issues/828)。*

此时你可能会想:“好的，这阻止了我给一个字符串赋值...哦，哦，该死的ðÿ™„".这就是我们介绍`interface`的地方。接口允许你为一个对象的“框架”和它的属性(以及它们的类型)定义一个名字，以确保传递给一个函数或者分配给一个变量的值是正确的类型。*对于我的 react folk 来说，这些真的很方便。*一个`interface`会这样实现:

```
interface Foo {
  bar: string,
  baz?: number
}

function interfaceTest(foo: Foo) {
  console.log(foo.bar);
  console.log(foo.baz ? foo.baz : `the property "baz" is optional so it does not have to be passed into the function`);
}

interfaceTest({bar: 'bar'}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您自己测试过，您可能会惊讶地发现 TypeScript 没有对我们大喊大叫。这是因为属性`baz`是 *`optional`* *(用一个问号表示在属性名之后，就在`:`之前)*。这意味着不必实现该属性来满足接口的契约(类型要求)。

这就引出了类型别名。类型别名就像接口一样，但是有一些约束和很酷的附加条件。类型别名不能用于[声明合并](https://www.typescriptlang.org/docs/handbook/declaration-merging.html)，这是一个编译器特性的花哨术语，它采用多个同名接口，并将它们转换成一个`interface`声明。它们也不能被类扩展或实现。类型别名的巧妙之处在于，我们可以将它们用作文字基本类型(字符串、数字、数组)的变量...那种东西)*T5 这里的是原语类型的完整列表*。这意味着我们可以将多个类型存储为一个 *`type alias`* (这在演示中更有意义):

```
type OptionalStringReturn = string | void;

function foo(str: string): OptionalStringReturn {
    //in here we can elect to return a string, or return nothing
} 
```

Enter fullscreen mode Exit fullscreen mode

这很有帮助，因为 TypeScript 中的函数不支持用`?`来表示可选值。接口不能作为基本类型的占位符，所以类型别名在这里肯定会派上用场。*`|`*表示该类型的属性值可以是类型别名声明中* `|` *分隔的任何一种类型。这些东西你想用多少就用多少。**

 *非常感谢您让我的小型 TypeScript 演示结束。我的下一篇 TypeScript 文章将使用 TS 在 React 中实现一个可搜索的 CollectionView。

再次感谢ðÿ——哈利*