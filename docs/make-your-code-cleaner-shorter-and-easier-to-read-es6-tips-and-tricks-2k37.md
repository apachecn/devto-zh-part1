# 让你的代码更干净、更简短、更易读！ES6 提示和技巧。

> 原文：<https://dev.to/samwsoftware/make-your-code-cleaner-shorter-and-easier-to-read-es6-tips-and-tricks-2k37>

## 模板文字

模板文字使得处理字符串比以前容易多了。它们以反勾号开始，可以使用${variable}插入变量。比较这两行代码:

```
var fName = 'Peter', sName = 'Smith', age = 43, job = 'photographer';
var a = 'Hi, I\'m ' + fName + '  ' + sName + ', I\'m ' + age + ' and work as a ' + job + '.';
var b = `Hi, I'm ${ fName }  ${ sName }, I'm ${ age } and work as a ${ job }.`; 
```

Enter fullscreen mode Exit fullscreen mode

这使得生活更简单，代码更容易阅读。你可以把任何东西放在花括号里:变量、方程或者函数调用。我将在本文的例子中使用这些。

## 块作用域

JavaScript 的作用域总是由函数确定的，这就是为什么将整个 JavaScript 文件包装在一个空的直接调用函数表达式(IIFE)中变得很常见。这样做是为了隔离文件中的所有变量，因此没有变量冲突。现在我们有了块作用域和两个绑定到块的新变量声明。

## 让宣言

这类似于“var ”,但有一些显著的区别。因为它是块范围的，所以可以声明同名的新变量，而不会影响外部变量。

```
var a = 'car' ;
{
    let a = 5;
    console.log(a); // 5
}
console.log(a); // car 
```

Enter fullscreen mode Exit fullscreen mode

因为它被绑定到一个块范围，所以它解决了这个经典的面试问题:
“什么是输出，你如何让它按你期望的那样工作”

```
for (var i = 1; i < 5; i++){
    setTimeout(() => { console.log(i); }, 1000);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，它输出“5 5 5 5 5 ”,因为变量 I 在每次迭代中都会改变。如果你把 var 换成 let，那么一切都变了。现在，每个循环都创建了一个新的块范围，其中 I 的值绑定到该循环。虽然你写了。

```
{let i = 1; setTimeout(() => { console.log(i) }, 1000)} 
{let i = 2; setTimeout(() => { console.log(i) }, 1000)} 
{let i = 3; setTimeout(() => { console.log(i) }, 1000)} 
{let i = 4; setTimeout(() => { console.log(i) }, 1000)} 
{let i = 5; setTimeout(() => { console.log(i) }, 1000)} 
```

Enter fullscreen mode Exit fullscreen mode

var 和 let 的另一个区别是，let 不像 var 那样被提升。

```
{ 
    console.log(a); // undefined
    console.log(b); // ReferenceError
    var a = 'car';
    let b = 5;
} 
```

Enter fullscreen mode Exit fullscreen mode

因为它有更严格的范围和更可预测的行为，一些人说你应该使用“let”而不是“var ”,除非你特别需要提升或放宽“var”声明的范围。

## 常数

如果你以前想在 JavaScript 中声明一个常量变量，习惯上用大写字母命名变量，但这不会保护变量，只是让其他开发者知道它是一个常量，不应该被改变。现在我们有了 const 声明。

```
{
    const c = "tree";
    console.log(c);  // tree
    c = 46;  // TypeError! 
} 
```

Enter fullscreen mode Exit fullscreen mode

Const 并不使变量不可变，只是锁定它的赋值。如果你有一个复杂的赋值(对象或数组)，那么这个值仍然可以被修改。

```
{
    const d = [1, 2, 3, 4];
    const dave = { name: 'David Jones', age: 32};
    d.push(5); 
    dave.job = "salesman";
    console.log(d);  // [1, 2, 3, 4, 5]
    console.log(dave);  // { age: 32, job: "salesman", name: 'David Jones'}
} 
```

Enter fullscreen mode Exit fullscreen mode

块范围函数的问题
函数声明现在被指定为绑定到块范围。

```
{
    bar(); // works
    function bar() { /* do something */ }
}
bar();  // doesn't work 
```

Enter fullscreen mode Exit fullscreen mode

当您在 if 语句中声明一个函数时，问题就来了。考虑一下这个:

```
if ( something) {
    function baz() { console.log('I passed') }
} else {
    function baz() { console.log('I didn\'t pass') } 
} 
baz(); 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 之前，这两个函数声明都会被提升，结果会是‘我没有通过’,不管是什么东西。
现在我们得到“ReferenceError ”,因为 baz 总是受到块范围的约束。

## 传播开来

ES6 引入了被称为扩展运算符的`...`运算符。它有两个主要用途:将一个数组或对象扩展到一个新的数组或对象中，以及将多个参数连接到一个数组中。第一个用例可能是你最常遇到的，所以先看看这个。

```
let a = [3, 4, 5];
let b = [1, 2, ...a, 6];
console.log(b);  // [1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

这对于从数组向函数传递一组变量非常有用。

```
function foo(a, b, c) { console.log(`a=${a}, b=${b}, c=${c}`)} 
let data = [5, 15, 2];
foo( ...data); // a=5, b=15, c=2 
```

Enter fullscreen mode Exit fullscreen mode

还可以扩展一个对象，将每个键值对输入到新对象中。

```
let car = { type: 'vehicle ', wheels: 4};
let fordGt = { make: 'Ford', ...car, model: 'GT'};
console.log(fordGt); // {make: 'Ford', model: 'GT', type: 'vehicle', wheels: 4} 
```

Enter fullscreen mode Exit fullscreen mode

spread 运算符的另一个特性是创建新的数组或对象。下面的例子为 b 创建了一个新的数组，但是 c 只是引用了同一个数组。

```
let a = [1, 2, 3];
let b = [ ...a ];
let c = a;
b.push(4);
console.log(a);  // [1, 2, 3]
console.log(b);  // [1, 2, 3, 4] referencing different arrays
c.push(5);
console.log(a);  // [1, 2, 3, 5] 
console.log(c);  // [1, 2, 3, 5] referencing the same array 
```

Enter fullscreen mode Exit fullscreen mode

第二个用例是将变量收集到一个数组中。当你不知道有多少变量被传递给一个函数时，这是非常有用的。

```
function foo(...args) {
    console.log(args); 
} 
foo( 'car', 54, 'tree');  //  [ 'car', 54, 'tree' ] 
```

Enter fullscreen mode Exit fullscreen mode

## 默认参数

现在可以用默认参数定义函数。缺失或未定义的值用默认值初始化。只是要小心，因为空值和假值被强制为 0。

```
function foo( a = 5, b = 10) {
    console.log( a + b);
}

foo();  // 15
foo( 7, 12 );  // 19
foo( undefined, 8 ); // 13
foo( 8 ); // 18
foo( null ); // 10 as null is coerced to 0 
```

Enter fullscreen mode Exit fullscreen mode

默认值不仅仅是值，还可以是表达式或函数。

```
function foo( a ) { return a * 4; }
function bar( x = 2, y = x + 4, z = foo(x)) {
    console.log([ x, y, z ]);
}
bar();  // [ 2, 6, 8 ]
bar( 1, 2, 3 ); //[ 1, 2, 3 ] 
bar( 10, undefined, 3 );  // [ 10, 14, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

## 解构

析构是将等号左边的数组或对象拆开的过程。数组或对象可以来自变量、函数或方程。

```
let [ a, b, c ] = [ 6, 2, 9];
console.log(`a=${a}, b=${b}, c=${c}`); //a=6, b=2, c=9
function foo() { return ['car', 'dog', 6 ]; } 
let [ x, y, z ] = foo();
console.log(`x=${x}, y=${y}, z=${z}`);  // x=car, y=dog, z=6 
```

Enter fullscreen mode Exit fullscreen mode

在对象析构的情况下，对象的键可以列在花括号内，以提取键值对。

```
function bar() { return {a: 1, b: 2, c: 3}; }
let { a, c } = bar();
console.log(a); // 1
console.log(c); // 3
console.log(b); // undefined 
```

Enter fullscreen mode Exit fullscreen mode

有时你想提取值，但是把它们赋给一个新的变量。这是通过等号左边的“键:变量”配对来完成的。

```
function baz() { 
    return {
        x: 'car',
        y: 'London',
        z: { name: 'John', age: 21}
    }; 
}
let { x: vehicle, y: city, z: { name: driver } } = baz();
console.log(
    `I'm going to ${city} with ${driver} in their ${vehicle}.`
); // I'm going to London with John in their car. 
```

Enter fullscreen mode Exit fullscreen mode

对象析构允许的另一件事是给多个变量赋值。

```
let { x: first, x: second } = { x: 4 };
console.log( first, second ); // 4, 4 
```

Enter fullscreen mode Exit fullscreen mode

## 对象文字和简明参数

当您从变量创建对象文字时，ES6 允许您忽略与变量名相同的键。

```
let a = 4, b = 7;
let c = { a: a, b: b };
let concise = { a, b };
console.log( c, concise ); // { a: 4, b: 7 },  { a: 4, b: 7 } 
```

Enter fullscreen mode Exit fullscreen mode

这也可以和析构结合使用，使你的代码更加简单和整洁。

```
function foo() {
    return {
        name: 'Anna', 
        age: 56,
       job: { company: 'Tesco', title: 'Manager' }
    };
} 
// pre ES6
let a = foo(), name = a.name, age = a.age, company = a.job.company;
// ES6 destructuring and concise parameters 
let { name, age, job: {company} } = foo(); 
```

Enter fullscreen mode Exit fullscreen mode

它还可以用来析构传递给函数的对象。方法 1 和 2 是你在 ES6 之前应该做的，方法 3 使用析构和简洁参数。

```
let person = {
    name: 'Anna', 
    age: 56,
    job: { company: 'Tesco', title: 'Manager' }
};
// method 1
function old1( person) {
    var yearOfBirth = 2018 - person.age;
    console.log( `${ person.name } works at ${ person.job.company } and was born in ${ yearOfBirth }.`);
}
// method 2
function old1( person) {
    var age = person.age,
        yearOfBirth = 2018 - age, 
        name = person.name,
        company = person.job.company;
    console.log( `${ name } works at ${ company } and was born in ${ yearOfBirth }.`);
} 
// method 3
function es6({ age, name, job: {company} }) {
    var yearOfBirth = 2018 - age;
    console.log( `${ name } works at ${ company } and was born in ${ yearOfBirth }.`);
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 ES6，我们可以提取年龄、姓名和公司，而无需额外的变量声明。

## 动态属性名称

ES6 增加了使用动态分配的键创建或添加属性的能力。

```
let  city = 'sheffield_';
let a = {
    [ city + 'population' ]: 350000
};
a[ city + 'county' ] = 'South Yorkshire';
console.log(a); // {sheffield_population: 350000, sheffield_county: 'South Yorkshire' } 
```

Enter fullscreen mode Exit fullscreen mode

## 箭头功能

箭头函数有两种主要方式:它们的结构和它们的“this”绑定。与传统函数相比，它们可以有更简单的结构，因为它们不需要“function”关键字，而且它们会自动返回箭头后面的内容。

```
var foo = function( a, b ) {
    return a * b;
} 
let bar = ( a, b ) => a * b; 
```

Enter fullscreen mode Exit fullscreen mode

如果函数需要的不仅仅是简单的计算，可以使用花括号
，函数返回从块作用域返回的任何内容。

```
let baz = ( c, d ) => {
    let length = c.length + d.toString().length;
    let e = c.join(', ');
    return `${e} and there is a total length of ${length}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

箭头函数最有用的地方之一是在数组函数中，如`.map`、`.forEach`或`.sort`。

```
let arr = [ 5, 6, 7, 8, 'a' ];
let b = arr.map( item => item + 3 );
console.log(b); // [ 8, 9, 10, 11, 'a3' ] 
```

Enter fullscreen mode Exit fullscreen mode

除了具有更短的语法之外，它还修复了围绕`this`绑定行为经常出现的问题。对 ES6 之前的函数的修正是存储`this`引用，通常作为一个`self`变量。

```
var clickController = {
    doSomething: function (..) {
        var self = this;
        btn.addEventListener(
            'click', 
            function() { self.doSomething(..) }, 
            False
       );
   } 
}; 
```

Enter fullscreen mode Exit fullscreen mode

这必须作为完成，因为`this`绑定是动态的。这意味着事件侦听器中的 This 和 doSomething 中的 this 不是指同一件事。在 arrow 函数中，this 绑定是词法的，而不是动态的。这是 arrow 函数的主要设计特征。
虽然词法上的这种绑定可能很棒，但有时这并不是我们想要的。

```
let a = {
    oneThing: ( a ) => {
         let b = a * 2;
         this.otherThing(b);
    }, 
    otherThing: ( b ) => {....} 
};
a.oneThing(6); 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用`a.oneThing(6)`时，`this.otherThing( b )`引用失败，因为它没有指向 a 对象，而是指向周围的范围。如果您使用 ES6 语法重写遗留代码，这是需要注意的。

## 为…的循环

ES6 增加了迭代数组中每个值的方法。这不同于现有的在键/索引上循环的`for ... in`循环。

```
let a = ['a', 'b', 'c', 'd' ];
// ES6 
for ( var val of a ) {
    console.log( val );
} // "a" "b" "c" "d"
// pre-ES6 
for ( var idx in a ) {
    console.log( idx );
}  // 0 1 2 3 
```

Enter fullscreen mode Exit fullscreen mode

使用新的`for … of`循环节省了在每个循环中添加一个`let val = a[idx]`的时间。
数组、字符串、生成器和集合在标准 JavaScript 中都是可迭代的。普通对象通常不能被迭代，除非你为它定义了一个迭代器。

## 数字文字量

ES5 代码很好地处理了十进制和十六进制数字格式，但是没有指定八进制格式，并且在严格模式下是不允许的。ES6 增加了一种新的格式，在 0 后面加一个“o”来声明这个数字是八进制的。他们还增加了二进制格式。

```
Number( 29 )  // 29
Number( 035 ) // 35 in old octal form. 
Number( 0o35 ) // 29 in new octal form 
Number( 0x1d ) // 29 in hexadecimal 
Number( 0b11101 ) // 29 in binary form 
```

Enter fullscreen mode Exit fullscreen mode

## 还有更多…

ES6 为我们提供了更多的东西，让我们的代码更简洁、更易读、更健壮。我的目标是写这篇文章的续篇，涵盖 ES6 不太为人所知的部分。
如果你等不了那么久，可以在 ES6 上看看凯尔·辛普森的[你不知道的 JS 书，或者看看这个](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md)[辉煌的小网站](http://es6-features.org/#Constants)！

### 如果你喜欢这篇文章，请在评论中告诉我你的想法！