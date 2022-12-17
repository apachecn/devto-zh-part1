# 探索 PHP 中的 Lambda 函数和闭包

> 原文：<https://dev.to/sarfraznawaz2005/exploring-lambda-functions-and-closures-in-php-2fnd>

# 什么是λ函数

lambda 函数(也称为**匿名函数**)是一个函数，它具有:

1.  无签名
2.  可以赋给一个变量
3.  可以作为参数传递给类的其他函数或方法

在过去，这可能与 [`create_function`](http://php.net/manual/en/function.create-function.php) 类似:

```
$multiply = create_function('$a, $b','return $a * $b;');
echo $multiply(5, 5); // 25 
```

但是如果你需要使用`create_function`编写更长的匿名函数，那就麻烦了，因为很难在引号内编写函数体，然后转义引号之类的东西...

PHP 5.3 引入了对 lambda/匿名函数的真正支持，就像你在 Javascript 中看到的那样，所以你可以写上面这样的代码:

```
$multiply = function ($a, $b) {
    return $a * $b;
};

echo $multiply(5, 5); // 25 
```

可以看到，上面的匿名函数没有名字，已经被赋给了变量`$multiply`。还可以通过它传递其他函数:

```
function countEmails($number) {
    echo 'you have ' . $number . ' emails!';
}

$multiply = function ($a, $b) {
    return $a * $b;
};

echo countEmails($multiply(2, 5)); // you have 10 emails! 
```

如果你愿意，你也可以这样写

```
function countEmails($multiply, $a, $b) {
    echo 'you have ' . $multiply($a, $b) . ' emails!';
}

$multiply = function ($a, $b) {
    return $a * $b;
};

echo countEmails($multiply, 2, 5); // you have 10 emails! 
```

甚至这样:

```
function countEmails($multiply, $a, $b) {
    echo 'you have ' . $multiply($a, $b) . ' emails!';
}

echo countEmails(function ($a, $b) {
    return $a * $b;
}, 2, 5); // you have 10 emails! 
```

您也可以将其分配给数组:

```
$array['multiply'] = function ($a, $b) {
     return $a * $b;
};

echo $array['multiply'](2, 10); // 20 
```

甚至还有一个物体:

```
$obj = new StdClass();

$obj->multiply = function ($a, $b) {
     return $a * $b;
}; 
```

但是这里需要注意的是，不要像调用`echo $obj->multiply(2, 10)`那样调用它，而是需要做:

```
$multiply = $obj->multiply;
echo $multiply(2, 10); // 20 
```

现在让我们用匿名函数
创建一个写`Hello, World`的小程序

```
$message = 'Hello, World!';

$sayHello = function () {
    echo $message;
};

$sayHello(); 
```

糟糕，这会导致错误:

`Notice: Undefined variable: message`

这意味着匿名函数不能访问其内部的`$message`。这正是我们需要一个闭包来访问那个`$message`变量的时候。

# 什么是闭包

闭包是一个 lambda/匿名函数，它通过使用`use`关键字知道它周围的上下文。它可以访问其定义范围之外的变量。考虑最后一个`$sayHello`匿名函数，我们通过使用`use`关键字将它转换成闭包，并获得对`$message`变量:
的访问

```
$message = 'Hello, World!';

$sayHello = function () use ($message) {
    echo $message;
};

$sayHello(); // Hello, World! 
```

多亏了闭包(`use`关键字)，我们现在能够访问`$message`变量了！

* * *

如果需要改变传递给外部变量的值，当然需要使用引用:

```
$message = 'Hello, World!';

$sayHello = function () use (&$message) {
    echo $message;
    $message = 'Hello World Again!';
};

$sayHello(); // Hello, World!
$sayHello(); // Hello World Again! 
```

您也可以将常规参数传递给闭包:

```
$message = 'World!';

$sayHello = function ($arg) use ($message) {
    echo $arg . ' ' . $message;
};

$sayHello('Hello'); // Hello, World! 
```

你也可以使用闭包很容易地创建一个递归函数，下面是一个你如何创建阶乘函数的例子:

```
$factorial = function( $n ) use ( &$factorial ) {
    if( $n == 1 ) return 1;
    return $factorial( $n - 1 ) * $n;
};

echo $factorial( 5 ); 
```

请注意，`$factorial`是使用`&`通过引用传递的，否则它不会工作。

* * *

匿名函数和闭包的区别在于，闭包能够通过使用`use`关键字访问其作用域之外的变量。这就是匿名函数和闭包之间的细微差别。实际上，两者都是`Closure`类的内部实例:

```
// anonymous function
$multiply = function ($a, $b) {
    return $a * $b;
};

$message = 'Hello, World';

// closure
$sayHello = function () use ($message) {
    echo $message;
};

var_dump ($multiply instanceof Closure); // true
var_dump ($sayHello instanceof Closure); // true 
```

# 类型提示

我们知道你已经可以输入一些提示了:

*   目标
*   排列
*   连接
*   请求即付的

但是你也可以输入提示`Closure`，如下图:

```
function multiply($number, Closure $closure)
{
    return $closure($number);
}

$closure = function($x){ return $x * 2; };
echo multiply(10, $closure); 
```

这里我们让`multiply`函数要求`Closure`的类型作为第二个参数。

# 用例

匿名函数和闭包可以在多种情况下使用。

**作为回调**

你可以在你的自定义函数中使用它们作为回调或者 PHP 的一些内置函数，比如`array_map()`、`array_reduce()`、`array_filter()`、`array_walk()`等等。让我们以`array_walk()`为例。如果你[看它的定义](http://php.net/manual/en/function.array-walk.php)，这里是它的样子:

```
bool array_walk ( array &$array , callable $callback [, mixed $userdata = NULL] ) 
```

注意第二个参数`callable $callback`。这意味着它希望某个东西是可调用的函数。任何时候你在一些函数定义中看到这样的参数，这意味着你可以对它应用匿名函数。下面是例子:

```
$myArray = array(1, 2, 3, 4, 5);

// multiply each array value with 2    
array_walk($myArray, function(&$value, $index){
    $value *= 2;
});

print_r($myArray);

Array
(
    [0] => 2
    [1] => 4
    [2] => 6
    [3] => 8
    [4] => 10
) 
```

* * *

**在路由中**

您可能已经在 Laravel、Slim、Silex 等框架中看到闭包被用于路由:

```
$app = new \Slim\Slim($options);

$app->get('/', function () use ($app) {
    $app->render('home', array('content' => 'Hello, World!!!'));
}); 
```

这里`$app`是从外部作用域导入到闭包的作用域中，这样它就可以用来呈现视图`$app->render(...)`

* * *

**访问类的私有成员**

我们可以使用`Closure`类的 [`bind()`](http://www.php.net/manual/en/closure.bind.php) 或 [`bindTo`](http://www.php.net/manual/en/closure.bindto.php) 方法来访问某个类的`private`数据，例如:

```
class MyClass {
    private $variable = 'I am private variable!';
}

$closure = function() {
    return $this->variable;
};

$result = Closure::bind($closure, new MyClass(), 'MyClass');
echo $result(); // I am private variable! 
```

你可以看到，我们能够获得私有变量`$variable`的值。

类似地，也有可能**向一个类添加新的行为而不直接修改它**。很酷的匈奴人？

* * *

**延迟加载一个类**

用闭包可以做的另一件事是延迟加载一个类。比如:

```
class MyClass {
    public function __construct() {
        echo 'I am initialized!';
    }
}

$getMyClass = function() {
    $myClass = new MyClass();
    return $myClass;
}; 
```

如果您运行上面的代码，您可能会看到`I am initialized!`消息，因为有人可能会认为我们正在创建一个`MyClass`的实例。但这不是真的，因为`$getMyClass`匿名函数中的代码直到你真正调用它时才会运行:

```
$myClass = $getMyClass(); 
```

现在您会看到消息`I am initialized!`。所以这是一个非常巧妙的技巧，可以推迟一些代码，只在需要的时候使用。

[![](img/ea086352601ae58ed45ef0af70e404ef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uxDrqiP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/QI9f2vnPtWE)