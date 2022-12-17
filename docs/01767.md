# JavaScript 模板文字

> 原文：<https://dev.to/tiffany/javascript-template-literals-2o7>

*图片来源于[Hongkiat](https://www.hongkiat.com/blog/ecmascript-6-template-literals/)T3】*

传统上，JavaScript 中的字符串是有限的，尤其是在使用 Ruby 和 Python 这样的语言时。在这两种语言中，字符串插值意味着您可以替换字符串，并且可以进行多行字符串连接，而不会出现漏洞。

在 JavaScript 中，直到 ES6 才出现这样的东西。现在，我们有了 JavaScript 模板文字或模板字符串。

## 语法

模板字符串使用反勾号而不是引号来表示字符串。模板字符串可能是这样的:

```
let hello = `Hello World!`; 
```

## 字符串替换

字符串替换的好处在于，您可以将任何 JavaScript 表达式放入模板文本中，它将作为字符串的一部分输出。

语法如下:

```
let name = “Tiffany”;
console.log(`Hey, ${name}!`); 
```

这类似于 Ruby 的字符串插值:

```
name = "Tiffany"

p "Hello #{name}, how are you?"

age = 99

p "I am #{age} years old." 
```

因为模板文字中的字符串替换是 JavaScript 表达式，所以我们可以在模板文字中计算任何类型的表达式。我们可以对一个数学表达式求值，例如:

```
let a = 13;
let b = 20;
console.log(`The Mac first launched ${a+b} years ago. I, for one, welcome my bitten fruit overlord.`);

// => The Mac launched 33 years ago. I, for one, welcome my bitten fruit overlord.

console.log(`I am old, but not ${2 * (a+b)} yet.`);

// => I am old, but not 66 yet.
// I know. It doesn’t make sense. Bear with my pitiful examples. 
```

您甚至可以将函数或方法调用放在模板文本中:

```
// Functions inside expressions
function func() { return “I am the result of foo” }
console.log(`bar ${func} baz.`);

// => I am the result of foo bar baz.

//Method calls

let hacker = {name: ‘Elliot Anderson’}
console.log(`You forgot to quit Vim, ${hacker.name.toUpperCase()}`);
// => You forgot to quit Vim, ELLIOT ALDERSON 
```

## 多行字符串

JavaScript 中的多行字符串需要反斜杠 hack 和字符串串联:

```
let greeting = “Hello “ +
“World”; 
```

模板字符串使这变得简单多了。在需要的地方添加新的行，模板文字中反勾号内的空白将包含在字符串中，如下所示:

```
console.log(`I'm a string on one line
I’m a string on another line`); 
```

## 标记的模板

标记模板可用于强大的字符串转换。您可以通过在模板字符串前放置一个函数名来创建一个标记模板。下面是一个自动转义 HTML 函数的例子:

```
html`<p title="${title}">Hello ${name}!</p>` 
```

返回替换了适当变量但替换了所有不安全字符的字符串。

尼古拉斯·扎卡斯在他的书[理解 ES6](https://leanpub.com/understandinges6/read#leanpub-auto-multiline-strings) 中详细阐述了这一点。

## 总结

模板文字或模板字符串是 JavaScript 语言的强大补充，它带来了非常需要的字符串插值和转换。