# PHP 101:使用数组

> 原文：<https://dev.to/bengreenberg/php-101-working-with-arrays-cf6>

本周，在我们关于 PHP 101 的连续系列中，我们将探索一些使用数组的有用函数。数组是存储多条信息的非常有用的数据结构。它们允许通过引用项目的*索引*来容易且相对快速地回忆信息。每个数据结构都有优点和缺点，应该使用最适合自己情况的数据结构。对每种方法的优缺点的讨论超出了本文的范围，但是有很多关于这个主题的好材料，包括奇妙的 [BaseCS](https://www.codenewbie.org/basecs) 播客。

让我们创建下面的数组:

```
$myNumbers = [2,3,4,5,6]; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有一个非常简单的数组，保存 2、3、4、5 和 6 的值。这个数组可以通过`$myNumbers`变量来访问。如果我们想创建一个新变量来保存每个数字乘以 2 的结果呢？实现这一目标的最快方法是什么？欢迎使用 PHP 中的`array_map()`函数。这个函数*将*映射到数组上，对数组中的每个值应用一个回调函数。这是它实际运行的样子:

```
$myNumbers = [2,3,4,5,6];

function multiplyThem($num) {
  return ($num * 2);
}

$multiplied = array_map("multiplyThem", $myNumbers);

print_r($multiplied); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们从变量`$myNumbers`保存原始数组开始。此时我们想要创建我们的函数，它将作为我们在`array_map()`中的回调。我们创建了一个名为`multiplyThem`的函数，它简单地接受一个参数，并将其乘以 2，然后返回新值。然后我们创建一个名为`$multiplied`的新变量，用两个参数保存运行`array_map()`的结果:回调函数和执行函数的数组。最后，我们使用`print_r()`打印出新变量中的数据。最终结果是一个变量，该变量现在包含一个值为 4、6、8、10 和 12 的数组。

另一个使用数组的有用函数是`array_filter()`，它将按照回调函数中指定的方式*过滤*数组的值。有趣的是，如果在使用`array_filter()`时没有提供回调函数，那么任何返回`FALSE`的条目都将被移除。假设我们想只过滤偶数的`$myNumbers`数组。让我们来看看 [PHP 手册](http://php.net/manual/en/function.array-filter.php) :
中提供的一个例子

```
function even($var) {
  return($var % 2 == 0);
}

$evenNumbers = array_filter($myNumbers, "even");

print_r($evenNumbers); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们创建了一个名为`even`的函数，它返回任何能被 2 整除的值，即任何偶数。然后我们提供这个函数作为对`array_filter()`的回调，以及我们想要过滤的数组。最后，我们打印出新变量`$evenNumbers`的信息，它保存了过滤后的数组，在我们的例子中，结果将是一个由 2、4 和 6 组成的数组。

我们今天要看的最后一个使用数组的函数是`array_reduce()`，顾名思义，*将数组的值减少为一个值*。这个函数和前面的函数一样，也包含一个回调函数。它使用了两个变量:一个变量保存 reduce 操作的累积结果，另一个变量存储它当前正在操作的项目。所以如果我们想把所有的数组值加在一起，我们可以这样使用:

```
function sum($accum, $item) {
  $accum += $item;
  return $accum;
}

$mySum = array_reduce($myNumbers, "sum");

print_r($mySum); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们创建了一个名为`sum`的函数，它接受两个参数:累计总数和迭代项。该项目被添加到总数`$accum`中，并对数组中的所有项目重复这一过程。然后返回`$accum`的总值。然后我们创建一个新变量`$mySum`，用两个参数保存运行`array_reduce()`的值:原始数组和我们刚刚创建的回调函数。一旦全部完成，新变量`$mySum`将保存值 20。

PHP 内有更多的内置功能来处理数组，我鼓励你访问 [PHP 手册](http://php.net/manual/en/index.php)来发现它们，包括`array_unique()`、`array_count_values()`等等。编码快乐！