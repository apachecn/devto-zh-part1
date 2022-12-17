# 关于 Javascript 的自动分号插入，您需要知道什么

> 原文：<https://dev.to/promisetochi/what-you-need-to-know-about-javascripts-automatic-semi-colon-insertion-78a>

省略分号是 Javascript 为您提供的便利。Javascript 允许您在分号通常出现的地方省略分号，也就是说，在语句的结尾或者按照惯例在新的行字符之前。

```
const foo = "food"; const randomNumber = Math.random() 
```

Enter fullscreen mode Exit fullscreen mode

可以改写为

```
const foo = "food"
const randomNumber = Math.random() 
```

Enter fullscreen mode Exit fullscreen mode

这要感谢官方 ECMAScript 规范中包含的自动分号插入规则。

虽然这是一种便利，但如果没有正确理解，可能会引入许多 Javascript 陷阱场景。因此，在本文中，我将讨论自动分号插入是如何工作的。

下面的两个代码片段产生了完全不同的结果，这一点可能不明显

```
/**/
const val = "foo";
['semi']
console.log(val) //foo 
```

Enter fullscreen mode Exit fullscreen mode

```
const foo = {
  semi: 'not semi'
}
const val = foo
['semi']
console.log(val) //not semi 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道为什么会这样，下面是 ECMAScript 6 规范关于自动分号插入应该如何工作的说明:

> 当从左向右解析脚本或模块时，如果遇到语法的任何产品都不允许的标记(称为违规标记)，则如果满足以下一个或多个条件，则会在违规标记之前自动插入分号:

1.  至少用一个行终止符将有问题的标记与前一个标记分隔开
2.  有问题的令牌是}
3.  前一个标记是),插入的分号将被解析为 do-while 语句的终止分号

我试图用下面的代码示例来解释上面的规则

```
/* Rule 1.1 */
const foo = 'food'
const bar = 'bar' /* Yo, I found token const on a new line, parsing it with the previous line as a single statement doesn't make sense, insert semi-colon before line 3 */

/* Rule 1.2 */
const baz = () => {
    const boo = true
  return boo } /* Found token }, insert semi-colon before it, parse previous statement */

/* Rule 1.3 */
do {
    const GT = foo + 'bar'
} while (baz()) /* Insert semi-colon after closing parentheses, gold could be on the next line for all I care */ 
```

Enter fullscreen mode Exit fullscreen mode

总结第一条规则的另一种方式是，

“嘿，JS 引擎，如果解析代码没有错误，继续并忽略换行符。如果有解析错误，进行所有这些检查，并在必要的地方插入分号。”

这里要注意的重要一点是，浏览器不会在行尾插入分号，如果将一个新行和前一行一起解析为一个语句，仍然会产生有效的 Javascript。回到本文开头的例子:

```
const foo = {
  semi: 'not semi'
}
const bar = foo
['semi'] 
```

Enter fullscreen mode Exit fullscreen mode

即使`[â€˜semi’]`在一个新行上，与前一行一起解析仍然会产生有效的 Javascript。
所以它被解析为`const bar = foo[â€˜semi’]`，这导致“not semi”
虽然省略了分号，但开发人员在用这些字符开始新行时必须特别小心:

1.  [
2.  （
3.  +
4.  /
5.  -
6.  {

```
const one = '1'
const two = 1
+one // this is a common technique for converting type 'string' to 'number'
console.log(two) // 11 
```

Enter fullscreen mode Exit fullscreen mode

在 JS 项目中经常会看到类似于下面例子的代码，它可能不会立即明白为什么它被解析为正确的 Javascript 语法，但是这要归功于我们刚刚讨论的自动分号插入规则。

```
object
.childObject
.method() 
```

Enter fullscreen mode Exit fullscreen mode

该说明书还指出，

> 当从左到右解析脚本或模块时，遇到语法的某些产生式所允许的标记，但是该产生式是受限产生式，并且该标记将是受限产生式中紧跟注释“[此处无行终止符]”之后的终端或非终端的第一个标记(因此这样的标记被称为受限标记)，并且该受限标记与前一个标记被至少一个行终止符分隔开，则在该受限标记之前自动插入分号。

这只是说在某些标记(关键字)之后没有换行符，比如 return、break、后缀表达式(-、++)、throw 等等。

```
return //semi-colon is automatically inserted here
4 // no

break //semi-colon is automatically inserted here
a //no

continue //semi-colon is automatically inserted here
a = 4

const arr = arr () //semi-colon is automatically inserted here
=> {} // no

... 
```

Enter fullscreen mode Exit fullscreen mode

我希望你现在可以更自信地编写不带分号的 Javascript:)