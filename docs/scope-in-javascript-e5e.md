# Javascript 中的范围

> 原文：<https://dev.to/murithi/scope-in-javascript-e5e>

作用域可以说是一组规则，指明我们应该在哪里寻找变量。它定义了变量可用的区域。一个变量通常属于某个执行上下文。在这种情况下，某些变量- *值和表达式*是“可见的”和/或可以被引用。除此之外，没有对变量的访问。

## 变量作用域

通常 Javascript 中的变量要么在全局范围内定义，要么在局部范围内定义。在函数外部声明的变量是全局变量。否则，变量被限制在定义它们的函数的局部范围内。

### 全球范围

在整个运行时被声明为全局变量的变量。它在任何范围内都是可访问和可更改的，因为全局范围是执行中所有范围的父级。

```
 //carname is  accessible here
            var carname="Mercedes Benz";

            function car() {
                console.log(carname); // Mercedes Benz
                //carname is accessible from within here
            }

            console.log(carname); // Mercedes Benz
            //carname is  accessible here 
```

### 局部范围

虽然全局范围在编程中很有用，但它并不总是好的实践。遵循软件设计中的“T0”最小特权原则，最好是应用范围隐藏技术。这需要声明嵌套在块或函数中的变量。这就产生了我们所说的局部范围。

在 Javascript 中，局部变量只能在定义它的函数中使用。这里的变量在运行时每次调用函数时都会重新创建它们的作用域。除非引用在函数的局部范围内，否则变量仍然不可访问。

```
 //carname is not accessible here
            function car() {
                var carname = 'Mercedes Benz';
                //carname is accessible from within here
                console.log(carname); // Mercedes Benz
            }
            //carname is not accessible here
            console.log(carname); // ReferenceError: carname is not defined 
```

正如你所看到的，在函数中声明的变量 *carname* 从函数外部是不可到达的。因此，一个函数有它的局部作用域，不能从外部访问它内部的变量。

### 功能范围

Javascript 有函数的词法作用域。在词法范围内，变量名的范围仅限于函数定义内的那个函数。它在这里生存并被绑定，在函数之外不能被引用。
*需要注意的是，Javascript 中的花括号 **{}** 并不会创建新的作用域。这是因为(在 ECMA 6 标准之前)花括号没有创建新的作用域。只有通过创建新的函数，才能创建新的作用域。*

在调用函数之前，函数作用域不存在。

```
 //Function scope
            var carname = 'Mercedes Benz';
            function car(carname) {
                console.log(carname);
            }
            //carname is not accessible here
            car('BMW'); // BMW
            console.log(carname); // Mercedes Benz
            car('Rolls Royce'); // Rolls Royce
            console.log(carname); // Mercedes Benz
            car('Volvo'); // Volvo 
```

每次调用函数 *car* 后，都会创建一个新的作用域，并打印出变量 *carname* 中的输出。因此，每次调用这个函数，一个新的作用域都会有不同的输出，如上图所示*宝马*，*奔驰*。全局变量 *carname* 一直保持它的值。

### 块范围

块作用域只是一个代码块。块被立即执行，而不是必须调用的函数。Javascript 中的块包括 if 语句、循环等。在 ECMAScript 6 (ES6/ES2015)之前，Javascript 没有块范围。在此之前的一个块的工作方式如下。

```
 // Blocks in Javascript don't create scope
            var carname="Mercedes Benz";
            if (true){
                var carname = "Volvo";
                console.log(carname); // Volvo
            }
            console.log(carname); // Volvo 
```

如您所见， *carname* 即使在块中声明，仍然引用同名的全局变量。块中的任何更新都会影响全局作用域变量，因为实际上，块中的 *carname* 仍然引用同名的全局作用域变量。显然，没有创建局部范围的变量。

以前，创建块范围的方法是使用立即调用函数表达式(IIFE)模式。

```
 //IIFE Demo
            var carname = 'Mercedes Benz';
            (function car(carname) {
                var carname = 'Volvo';
                console.log(carname);// Volvo
            })()
            //carname prints out the global scope value
            console.log(carname); // Mercedes Benz 
```

函数内 *carname* 的输出在函数表达式内改变，不影响全局变量 *carname* 。

ECMAScript 6 (ES6/ES2015)使用 *let* 和 *const* 关键字引入了轻量级块。这些可用于在块内创建新的局部范围。因此，对变量的访问被限制在定义它的块的范围内。这个作用域也只在运行时在堆栈中执行块时创建，并且只能从块内部访问。

```
 //Block Scope Demo
            var carname="Mercedes Benz";
            if (true)  {

                let carname = "Volvo";
                let vehiclename = "Volvo";
                //vehiclename is only accessible from within here
                console.log(carname); //Volvo 

            }
            console.log(carname); //Mercedes Benz
            console.log(vehiclename); //Uncaught ReferenceError: vehiclename is not defined 
```

*vehiclename* 变量只能在块范围内访问。

### 就是这样！

在这篇文章中，我们已经讨论了关于范围的基础知识。总是有更多的东西要学，但这应该足以帮助你掌握基本知识。编码快乐！