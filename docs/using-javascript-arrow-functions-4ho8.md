# 使用 Javascript 箭头函数

> 原文：<https://dev.to/chuksfestus/using-javascript-arrow-functions-4ho8>

[![](img/7a37189bfd96aa3254ccb04d9f5f4a4a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AwyfOM82--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9s_UE0CM6cz2XJq8CTf29w.jpeg)

传统的 JavaScript 函数语法不提供任何灵活性，不管是一个语句函数还是一个不幸的多页函数。每次你需要一个函数的时候，你都必须打出可怕的函数(){}。对我来说，这是一种严重的痛苦，尤其是在使用 coffeescript 之后。但是感谢上帝！！Javascript 的维护者决定力挽狂澜，给了我们

 *箭头函数也称为“胖箭头”函数，是一种用于编写函数表达式的更简洁的语法。他们使用了一个新的令牌= >，看起来像一个粗箭头。箭头函数是匿名的，它改变了函数绑定的方式。

通过使用箭头函数，我们避免了键入函数关键字、返回关键字(它隐含在箭头函数中)和花括号。

### 使用箭头功能

两个因素影响了 arrow 函数的引入:更短的函数和它的非绑定性。

#### 较短的函数

让我们比较一下 ES5 代码和函数表达式现在如何用箭头函数在 ES6 中编写。

```
//ES5
add = function(x, y) {
    return x + y;
}
console.log(add(1,2)); // prints 3

//ES6
add = (x,y) => x + y
console.log(add(1,2)); // prints 3 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧？上面的 arrow 函数示例允许开发人员用更少的代码行和大约一半的输入完成相同的结果。箭头函数的语法有很多种，这取决于你要完成的任务，也就是说，参数和主体可以根据用途采用不同的形式。例如，下面的 arrow 函数接受一个参数，并简单地返回它:

```
// ES6:

let arrowFunc = value => value;

// ES5:

var reflect = function(value) {
    return value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

当一个 arrow 函数只有一个参数时，这个参数可以直接使用，不需要任何进一步的语法。类似地，没有任何命名参数的函数必须使用空括号来开始箭头函数声明:

```
// ES6:

let add = () => 1 + 2;

// ES5:

let add = function() {
    return 1 + 2;
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 无约束力的本

在 arrow 函数之前，每个新函数都定义了自己的[这个](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)值，这被证明对于面向对象风格的编程来说是令人讨厌的。因为这个值可以在一个函数内部根据调用它的上下文而改变，所以当你想影响一个对象时，可能会错误地影响另一个对象。考虑这个例子:

```
function Person() {
  // The Person() constructor defines `this` as an instance of itself.
  this.age = 0;

  setInterval(function growUp() {

    /\* In non-strict mode, the growUp() function defines `this` 
       as the global object, which is different from the `this`
       defined by the Person() constructor.\*/

    this.age++;
  }, 1000);
}

let p = new Person(); 
```

Enter fullscreen mode Exit fullscreen mode

arrow 函数不会创建自己的 this 上下文，因此它具有来自封闭上下文的原始含义。因此，下面的代码按预期工作:

```
function Person(){
  this.age = 0;

  setInterval(() => {
    this.age++; // |this| properly refers to the person object
  }, 1000);
}

let p = new Person(); 
```

Enter fullscreen mode Exit fullscreen mode

### 箭头功能的陷阱

新的 arrow 函数为 ECMAScript 带来了有用的函数语法，但是与任何新特性一样，它们也有自己的缺陷和不足。这里有一些使用箭头函数时需要注意的事情。

#### 更多关于这个

因为这在 arrow 函数中没有绑定，所以方法 call()或 apply()只能传入参数。这个就忽略了。函数内部的这个值是不能改变的——它将和函数被调用时的值相同。如果需要绑定到不同的值，就需要使用函数表达式。

#### 没有约束力的论据

箭头函数不绑定 arguments 对象。因此，在本例中，arguments 只是对封闭范围中相同名称的引用:

```
let arguments = 42;
let arr = () => arguments;

arr(); // 42

function foo() {
  let f = (i) => arguments[0] + i; // foo's implicit arguments binding
  return f(2);
}

foo(1); // 3 
```

Enter fullscreen mode Exit fullscreen mode

然而，使用 [rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)是使用 arguments 对象的一个很好的替代方法。

```
function foo() { 
  let f = (...args) => args[0]; 
  return f(2); 
}

foo(1); // 2 
```

Enter fullscreen mode Exit fullscreen mode

#### 构造函数

箭头函数不能像其他函数一样作为[构造函数](https://msdn.microsoft.com/en-us/library/c1hcx253(v=vs.94).aspx?WT.mc_id=16547-DEV-sitepoint-article83)使用。不要像使用其他函数一样使用它们来创建类似的对象。如果尝试将 new 与 arrow 函数一起使用，将会引发错误。箭头函数，像[内置函数](https://msdn.microsoft.com/en-us/library/c6hac83s(v=vs.94)?WT.mc_id=16547-DEV-sitepoint-article83)(又名方法)，没有原型属性或其他内部方法。因为构造函数通常用于在 JavaScript 中创建类对象，所以您应该使用新的 [ES6 类](https://medium.com/papdit/understanding-es6-classes-ada7c14e0213#.yfek9qnac)来代替。

### 结论

箭头函数是 ECMAScript 6 中一个有趣的新特性，也是目前已经非常成熟的特性之一。随着将函数作为参数传递变得越来越流行，拥有一个定义这些函数的简洁语法对于我们一直以来的做法来说是一个受欢迎的改变。词法 this 绑定解决了开发人员的一个主要痛点，并通过 JavaScript 引擎优化提高了性能。*