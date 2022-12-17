# 变量声明和范围规则

> 原文：<https://dev.to/simplymichael/variable-declaration-and-scoping-rules-c0k>

## 介绍

在程序设计中，变量的作用域决定了该变量在程序中的使用位置，以及哪些函数和对象可以访问该变量。

通常，变量可以有局部或全局范围。在代码块中声明的变量具有局部范围，只能由同一代码块中的其他代码访问。一旦声明该变量的块退出，该变量就超出了范围。另一方面，全局变量可以从当前正在执行的脚本(或程序)中的任何地方访问，并且通常持续整个程序的生命周期。

在这篇文章中，我们希望研究在 JavaScript 中声明变量的各种方法，我们将看到 JavaScript 中变量的作用域受其声明位置和方式的影响。

## 声明变量

JavaScript 中有三个关键字可用于声明变量。它们是 var、let 和 const 关键字。JavaScript 是一种动态的、松散类型的语言，它也允许您在没有预先声明变量的情况下使用变量，但是这被认为不是一种好的做法，并且是非常不鼓励的。

要声明一个变量，我们使用上面列出的任何一个关键字，后跟变量名，如下所示:

哪里是 a；

设 b；

const c = 5；

当你用 const 声明一个变量时，你必须用一个值来初始化它——就像我们上面所做的那样——这个值以后不能改变。当用 var 和 let 声明变量时，我们可以选择在声明时初始化变量:

其中 a = 2；

其中 b = 3；

### 用 var 关键字声明变量

使用 var 关键字声明变量时，作用域如下:

*   如果变量在任何函数之外声明，则该变量在全局范围内可用。

*   如果变量是在函数中声明的，那么从声明开始到函数定义结束，变量都是可用的。

与其他语言不同，用 var 关键字声明的变量没有块范围。具体来说，这意味着如果在 for 或任何非函数块中使用 var 声明一个变量，该变量的作用域将从该块扩展到该块的父作用域的末尾。另一方面，如果使用 var 关键字在函数内部声明变量，则该变量只能在函数定义中使用，而不能在函数外部访问。因此，我们说用 var 声明的变量是函数作用域的。

让我们看一些例子来帮助澄清我们所说的。

```
function varScope() {
   var a = 2;
   console.log(a); // outputs  2
}

console.log(a); // ReferenceError, a is not accessible outside the function.

```

让我们看另一个例子。

```
function varScope() {
   var a = 2;
   if(true) {
      var a =  "Jamie"; //change the value of a inside the "if" block
      console.log(a); //prints "Jamie"
   }
   console.log(a); //prints "Jamie": outside the "if" block, a still maintains the updated value 
   //a being function scoped, the (re-) declaration inside the if statement overwrote the previous value of a
   //when we assigned it a new value inside the conditional statement
}
console.log(a); // ReferenceError, again, a is not accessible outside the function.

```

最后，我们来看这一个。

```
function forScope() {
   for(var i = 0; i < 5; i++) {
      console.log(i); //prints the values 0 through 4;
   }
   console.log(i); //prints 5;
}

```

刚刚发生了什么？在 for 头中，我们声明并初始化 I 变量。然后在循环内部，当 I 的值小于 5 时，我们从 0 开始迭代，在每次迭代中碰撞 I。当 I 的值等于 5 时，条件 i < 5 评估为假，终止我们的循环。但是，由于 I 是用 var 声明的，所以它的作用域从声明点一直延伸到函数的末尾。因此，即使在循环之后，我们也可以访问 I 的最新值，在本例中是 5。

### 用 let 关键字声明变量

使用 let 关键字声明的变量有三个重要特征。

*   它们是**块范围的**
*   它们在被分配之前是不可访问的
*   它们不能在同一个范围内被重新声明

让我们用一些例子来看看这意味着什么。

```
function  letScope() {
   let a = 5;

   if  (true) {
      let a = "Jamie";  // using let creates a new a variable inside the "if" block
      console.log(a); //  prints "Jamie"
   }

   console.log(a); // 5,  outside the if block, the outer a shines through
}
console.log(a); // ReferenceError, a is not accessible outside the function.

```

这个函数是这样的。

1.  在函数内部，我们用 let 创建了一个 a 变量，这个变量存在于这个函数的范围内。

2.  在 if 块中，我们创建了另一个 let 声明的变量。由于是块范围的，我们刚刚创建了一个新的 a 变量。

3.  这个变量完全不同于，并且独立于外部变量 a。

4.  该变量仅在 if 块内可用，在该块外不可访问。

此外，您不能重新声明一个字母变量:

```
let a = 2;
let a = 3// SyntaxError, cannot re-declare the a variable

```

### 用 const 关键字声明变量

用 const 关键字声明的变量具有用 let 关键字声明的变量的所有特征，还有一个重要的区别特征:

*   他们不能被重新分配

```
const a = 2;
a = 3 // Error, reassignment is not allowed

```

```
const a = 2;
const a = 3 // Error, re-declaration is not allowed

```

## 可变可变性

不管你如何声明一个变量，使用我们讨论过的任何一个关键字，这个变量都是可变的。可变性不能与再分配混淆。当处理数组或对象时，这种差异会更加突出。举一两个例子就能说明这是什么意思。

### 对象示例:

```
const person = {
  name: 'Michael'
};
person.name = 'Jamie' // OK! person variable mutated, not completely re-assigned
console.log(person.name); // "Jamie"
person = "Newton" // Error, re-assignment is not allowed with const declared variables

```

### 数组示例:

```
const person = [];
person[0] = 'Michael'; // OK! person variable only mutated, not completely re-assigned
console.log(person[0]) // "Michael"
person = "Newton" // Error, re-assignment is not allowed with const declared variables

```

## 在声明变量之前访问变量

在关于用 let 声明变量的[一节中，我们注意到 let 声明变量的一个特征是它们在声明之前是不可访问的。这是什么意思？让我们看看。](#declaring-variables-with-the-let-keyword)

考虑这段代码:

```
console.log(a); // undefined, but no error raised
var a = 2;

```

在上面的代码片段中，我们试图在声明之前读取变量 a 的值。我们得到的不是错误，而是未定义。这是为什么呢？答案是 var 声明的变量在执行时被移到了作用域的顶部。

在运行时，此代码被解释为:

```
var a;
console.log(a); // undefined: a is declared, but hasn't been assigned a value, hence no errors raised
a = 2;

```

这种现象就是所谓的提升。

如果我们试图对使用 let 或 const 声明的变量做类似的事情，我们将会抛出一个引用错误。

```
console.log(a); // ReferenceError
let a = 2;

```

## 总结想法

掌握 JavaScript 中的作用域似乎很棘手，可能需要一些时间来适应。但是通过练习，在 JavaScript 中声明变量的各种方法以及这些方法如何影响作用域成为了第二天性。

## 进一步阅读

1.  [MDN let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
2.  [现代 Javascript 备忘单](https://github.com/mbeaudru/modern-js-cheatsheet#variable-declaration-var-const-let)