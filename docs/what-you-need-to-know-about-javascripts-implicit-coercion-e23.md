# 关于 Javascript 的隐式强制，您需要知道什么

> 原文：<https://dev.to/promisetochi/what-you-need-to-know-about-javascripts-implicit-coercion-e23>

Javascript 的隐式强制只是指 Javascript 试图将意外的值类型强制为预期的类型。所以你可以传递一个字符串，它需要一个数字，一个对象，它需要一个字符串，等等，它会尝试把它转换成正确的类型。这是最好避免的 Javascript 特性。

```
3 * "3" //9
1 + "2" + 1 //121

true + true //2
10 - true //9

const foo = {
  valueOf: () => 2
}
3 + foo // 5
4 * foo // 8

const bar = {
  toString: () => " promise is a boy :)"
}
1 + bar // "1 promise is a boy :)"

4 * [] // 0
4 * [2] // 8
4 + [2] // "42"
4 + [1, 2] // "41,2"
4 * [1, 2] // NaN

"string" ? 4 : 1 // 4
undefined ? 4 : 1 // 1 
```

Enter fullscreen mode Exit fullscreen mode

## 数值表达式中的非数值

### 字符串

每当您将一个字符串作为操作数传递到一个包含这些操作符的数值表达式中:`-, *, /, %`，数字的转换过程类似于对值调用内置的`Number`函数。这非常简单，任何只包含数字字符的字符串都将被转换成它的等价数字，但是包含非数字字符的字符串将返回`NaN`。如下图，

```
3 * "3" // 3 * 3
3 * Number("3") // 3 * 3
Number("5") // 5

Number("1.") // 1
Number("1.34") // 1.34
Number("0") // 0
Number("012") // 12

Number("1,") // NaN
Number("1+1") // NaN
Number("1a") // NaN
Number("one") // NaN
Number("text") // NaN 
```

Enter fullscreen mode Exit fullscreen mode

#### 为+运算符的情况

+运算符与其他数学运算符不同，它执行两个功能:

1.  数学加法
2.  串并置

当字符串是+运算符的操作数时，Javascript 不是将字符串转换为数字，而是将数字转换为字符串。

```
 // concatenation
1 + "2" // "12"
1 + "js" // "1js"

// addition
1 + 2 // 3
1 + 2 + 1 // 4

//addition, then concatenation
1 + 2 + "1" // "31"
(1 + 2) + "1" // "31"

//concatenation all through
1 + "2" + 1 // "121"
(1 + "2") + 1 // "121" 
```

Enter fullscreen mode Exit fullscreen mode

### 物体

大多数 Javascript 对象转换通常会导致`[object Object]`，例如

```
"name" + {} // "name[object Object] 
```

Enter fullscreen mode Exit fullscreen mode

每个 javascript 对象都继承了一个`toString`方法，每当一个对象要被转换成一个字符串时，就会调用这个方法。`toString`方法的返回值用于字符串连接和数学表达式等操作。

```
const foo = {}
foo.toString() // [object Object]

const baz = {
  toString: () => "I'm object baz"
}

baz + "!" // "I'm object baz!" 
```

Enter fullscreen mode Exit fullscreen mode

当它是一个数学表达式时，Javascript 会尝试将返回值转换成数字，如果不是的话。

```
const foo = {
  toString: () => 4
}

2 * foo // 8
2 / foo // 0.5
2 + foo // 6
"four" + foo // "four4"

const baz = {
  toString: () => "four"
}

2 * baz // NaN
2 + baz // 2four

const bar = {
  toString: () => "2"
}

2 + bar // "22"
2 * bar // 4 
```

Enter fullscreen mode Exit fullscreen mode

#### 数组对象

数组的继承的`toString`方法的工作方式有点不同，它的工作方式类似于在没有任何参数的情况下调用数组的`join`方法。

```
[1,2,3].toString() // "1,2,3"
[1,2,3].join() // "1,2,3"
[].toString() // ""
[].join() // ""

"me" + [1,2,3] // "me1,2,3"
4 + [1,2,3] // "41,2,3"
4 * [1,2,3] // NaN 
```

Enter fullscreen mode Exit fullscreen mode

因此，当您传递一个需要字符串的数组时，Javascript 会将`toString`方法的返回值与第二个操作数连接起来。如果它需要一个数字，它会尝试将返回值转换为数字。

```
4 * [] // 0
4 / [2] // 2

//similar to
4 * Number([].toString())
4 * Number("")
4 * 0

//

4 / Number([2].toString())
4 / Number("2")
4 / 2 
```

Enter fullscreen mode Exit fullscreen mode

#### “真”、“假”

```
Number(true) // 1
Number(false) // 0
Number("") // 0

4 + true // 5
3 * false // 0
3 * "" // 0
3 + "" // "3" 
```

Enter fullscreen mode Exit fullscreen mode

#### `valueOf`法

也可以定义一个`valueOf`方法，每当您传递一个对象，而该对象需要一个字符串或数值时，Javascript 就会使用这个方法。

```
 const foo = {
  valueOf: () => 3
}

3 + foo // 6
3 * foo // 9 
```

Enter fullscreen mode Exit fullscreen mode

当在一个对象上同时定义了`toString`和`valueOf`方法时，Javascript 会使用`valueOf`方法。

```
const bar = {
  toString: () => 2,
  valueOf: () => 5
}

"sa" + bar // "sa5"
3 * bar // 15
2 + bar // 7 
```

Enter fullscreen mode Exit fullscreen mode

valueOf 方法适用于应该表示数值的对象。

```
const two = new Number(2)

two.valueOf() // 2 
```

Enter fullscreen mode Exit fullscreen mode

## 虚假与真实

~~我真的很想做那件虚假而可信的事~~

每个 Javascript 值都可以被强制为 true 或 false。强制为布尔值`true`意味着值是真实的。强制为布尔值`false`意味着值为 falsy。

Javascript 中有一些值会返回 falsy 值，它们是:

1.  错误的
2.  Zero
3.  空
4.  不明确的
5.  ""
6.  圆盘烤饼
7.  -0

其他一切都是真实的

```
if (-1) // truthy
if ("0") // truthy
if ({}) // truthy 
```

Enter fullscreen mode Exit fullscreen mode

上面的片段是可以的，但是当试图确定一个值的真实性时，更好的做法是显式的。基本上，不要依赖 Javascript 的隐式强制，即使你觉得你非常了解它们。
而不是下面的代码片段，

```
const counter = 2

if (counter) 
```

Enter fullscreen mode Exit fullscreen mode

根据您的需求，以下任何一种都是更好的做法

```
if (counter === 2)

//or

if (typeof counter === "number") 
```

Enter fullscreen mode Exit fullscreen mode

这是因为，例如，您定义了一个应该与数字
一起工作的函数

```
 const add = (number) => {
  if (!number) new Error("Only accepts arguments of type: number")
  //your code
} 
```

Enter fullscreen mode Exit fullscreen mode

所以如果我用 0 调用 add 函数，我总是会得到一个意外的错误

```
add(0) // Error: Only accepts arguments of type: number

//better check

const add = (number) => {
  if (typeof number !== "number") new Error("Only accepts arguments of type: number")
  //your code
}

add(0) // no error 
```

Enter fullscreen mode Exit fullscreen mode

## 楠

`NaN`是不等于自身的特殊数值。

```
NaN === NaN // false

const notANumber = 3 * "a" // NaN

notANumber == notANumber // false
notANumber === notANumber // false 
```

Enter fullscreen mode Exit fullscreen mode

`NaN`是唯一不等于自身的 Javascript 值。因此，您可以通过与自身进行比较来检查`NaN`。

```
if (notANumber !== notANumber) // true 
```

Enter fullscreen mode Exit fullscreen mode

ECMAScript 6 引入了一个检查 NaN 的方法，`Number.isNaN`

```
Number.isNaN(NaN) // true
Number.isNaN("name") // false 
```

Enter fullscreen mode Exit fullscreen mode

当心全局`isNaN`函数，它试图在检查它是否是`NaN`之前强制它的参数。例如，

```
isNaN("name") // true
isNaN("1") // false 
```

Enter fullscreen mode Exit fullscreen mode

应该避免使用全局`isNaN`函数，其工作方式类似于下面的
函数

```
const coerceThenCheckNaN = (val) => {
  const coercedVal = Number(val)
  return coercedVal !== coercedVal ? true : false
}

coerceThenCheckNaN("1a") // true
coerceThenCheckNaN("1") // false
coerceThenCheckNaN("as") // true
coerceThenCheckNaN(NaN) // true
coerceThenCheckNaN(10) // false 
```

Enter fullscreen mode Exit fullscreen mode

那是大部分的隐性强制。如果我错过了什么，请在下面的评论中提出来，谢谢你一路阅读。