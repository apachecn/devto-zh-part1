# 闭包——PHP 与 Javascript

> 原文：<https://dev.to/sarfraznawaz2005/closures-php-vs-javascript-48kj>

在我之前的文章[探索 PHP 中的 Lambda 函数和闭包](http://codeinphp.github.io/post/exploring-lambda-functions-and-closures-in-php/)中，我给了你一个可以计算给定数字阶乘的函数的例子:

```
$factorial = function( $n ) use ( &$factorial ) {
    if( $n == 1 ) return 1;
    return $factorial( $n - 1 ) * $n;
};

echo $factorial( 5 ); // 120 
```

在那篇文章中，我只是说通过**引用**来传递`$factorial`是很重要的，否则它不会工作。让我们在这篇文章中探讨一下为什么会这样。

闭包在 Javascript 中很常见，所以让我们在其中创建一个简单的闭包，看看它的行为如何:

```
var addOne = function(num) {
    return function() { 
        console.log(num++); 
    };
};

var f = addOne(1);

f(); // 1
f(); // 2
f(); // 3 
```

可以看到，每次我们调用这个函数，它都会增加这个数字。现在让我们在 PHP 中进行同样的转换:

```
$addOne = function($num) {
    return function() { 
        echo $num++; 
    };
};

$f = $addOne(1);

$f();
$f();
$f(); 
```

这将导致错误:

`Notice: Undefined variable: num`

为什么会这样呢？因为变量作用域在 PHP 和 Javascript 中是不同的。在 PHP 中，默认情况下，所有变量都在局部范围内，除非你在全局范围内显式声明它们。然而，在 Javascript 中，所有变量都在全局范围内，除非你使用`var`关键字在局部范围内显式声明它们。既然我们在这里讨论函数和闭包，我们可以说在 PHP *中，在函数中声明的变量只能被声明它的函数*使用。另一方面，在 Javascript *中，变量不仅可用于声明它的函数，也可用于父函数*的内部函数。我们来看一个例子:

```
function test() {
  $variable = 'value';

  return function() {
    echo $variable;
  };
}

$f = test();
$f(); // Notice: Undefined variable: variable 
```

所以 PHP 给出了错误，因为内部函数不能访问外部变量`$variable`。我们可以通过使用关键字`use`创建闭包，或者在全局范围内声明该变量，使这个变量对内部函数可用。我们在 Javascript 中看不到同样的例子:

```
function test() {
  var variable = 'value';

  return function() {
    console.log(variable);
  };
}

var f = test();
f(); // value 
```

然而在 Javascript 中，内部函数能够从外部作用域获取变量，例如本例中的父函数。

这就是为什么上面显示的 Javascript 内部函数没有出错，而 PHP 却出错了。因此，要访问 PHP 中的外部变量，我们需要使用`use`关键字创建一个闭包。当然，在 Javascript 中，你不需要像`use`关键字这样的东西，因为正如我在 Javascript 中提到的，父函数的变量对它的所有内部函数都是可用的。由于 PHP 和 Javascript 中变量范围的不同，我们需要在 PHP 中使用`use`关键字。

因此，让我们修复之前在 PHP 中收到的错误:

```
$addOne = function($num) {
    return function() use ($num) { 
        echo $num++; 
    };
};

$f = $addOne(1);

$f(); // 1
$f(); // 1
$f(); // 1 
```

现在我们已经创建了闭包，错误消失了，`$num`变量现在可用于内部函数，但有一个问题，每次我们调用该函数时，它都会说`1`，不像我们之前看到的 Javascript 示例那样说`1`、`2`和`3`。这是因为与 Javascript 不同，PHP 在内部函数中创建了一个新的局部变量，例如通过`value`而不是`reference`传递它。从概念上讲，PHP 看到的代码是这样的:

```
$addOne = function($num) {
    return function() use ($num) { 
        $num = 1; // so PHP kind of creates a new variable and assigns to 1 each time you call this
        echo $num++; 
    };
}; 
```

因此，为了获得与 Javascript 类似的输出，我们需要使用`reference`传递变量，这可以通过使用`&`符号:
来完成

```
$addOne = function($num) {
    return function() use (&$num) { // passed by reference 
        echo $num++; 
    };
};

$f = $addOne(1);

$f();
$f();
$f(); 
```

现在我们得到了输出`1`、`2`和`3`。

* * *

这也是我在[上一篇文章](http://codeinphp.github.io/post/exploring-lambda-functions-and-closures-in-php/)中给出的阶乘例子中需要通过`reference`传递变量的原因。希望这篇文章能澄清闭包和作用域的困惑，尤其是对于从 Javascript 到 PHP 的人来说。

[![](img/ee2d37161d8bddcc5f7a71a3209a8016.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVGFUosF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/xA7G0kbIW-A)