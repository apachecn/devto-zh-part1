# PHP 代码，关于可读性和性能

> 原文：<https://dev.to/sebastianr1982/php-code-about-readability-and-performance>

我的很多编码时间都花在了提高性能和查找 bug 上，PHP 给了编码很大的自由，这往往会养成坏习惯，因此需要注意写出快速高效的代码。

对我来说，编写快速代码意味着有时我必须在可读性、代码简短、简单性和性能之间做出选择。

作为例子，我可以报告我使用[递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))的经验。递归，非正式地说，当一个函数调用它自己时，可以用几行代码来解决问题和创建算法。

这很好，但是用 PHP 可能是件坏事，因为从时间角度来看,**调用一个用户定义的函数是很昂贵的。**

## 问题

编写依赖注入解析器。我需要一个方法来创建一个依赖关系的地图，并将其存储在一个数组中。

首先，我使用递归方法作为最快的实现方法。当我进行概要分析时，我发现大部分执行时间都用于解决类依赖关系。递归太昂贵，需要重写代码。

#### 解

在互联网上快速搜索后，我决定使用**迭代**方法，相比**递归**需要更多代码，但速度更快。

作为递归:

```
<?php

/**
 * Create a dependencies map for a class.
 *
 * @param int $level Depth of dependency
 * @param string $class Class to build dependencies
 */
private function buildTree(int $level, string $class)
{
    //initialize array
    $this->tree[$level][$class] = [];

    //get parameter from constructor
    $param = (new \ReflectionClass($class))->getConstructor()->getParameters();

    //loop parameter
    foreach ($param as $key => $value) {

        //if there is parameter with callable type
        if ($value->hasType() === true && class_exists((string) $value->getType())) {

            //store dependency
            $this->tree[$level][$class][] = $value->getClass()->name;

            //call recursive
            $this->buildTree($level + 1, $value->getClass()->name);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

又如迭代:

```
<?php

/**
 * Create a dependencies map for a class.
 *
 * @param string $class Class to build dependencies
 */
private function buildTree(string $class)
{
    //set start level
    $level = 0;

    //create stack
    $stack = new \SplStack();

    //iterate
    while (true) {

        //initialize array if not already initialized
        if (!isset($this->tree[$level][$class])) {
            $this->tree[$level][$class] = [];
        }

        //get parameter from constructor
        $parameters = (new \ReflectionClass($class))->getConstructor()->getParameters();

        //loop parameter
        foreach ($parameters as $param) {

            //check if argument is already stored
            $notAlreadyStored = !in_array($param, $this->tree[$level][$class]);

            //if there is parameter with callable type
            if (class_exists((string) $param->getType()) && $notAlreadyStored) {

                //push values in stack for simulate later recursive function
                $stack->push([$level, $class]);

                //store dependency
                $this->tree[$level][$class][] = $param;

                //update values for simulate recursive function
                $level++;
                $class = (is_object($param->getClass())) ?
                    $param->getClass()->name : null;

                //return to main while
                continue 2;
            }

            if ($notAlreadyStored) {
                //store dependency
                $this->tree[$level][$class][] = $param;
            }
        }

        //if stack is empty break while end exit from function
        if ($stack->count() === 0) {
            break;
        }

        //get last value pushed into stack;
        list($level, $class) = $stack->pop();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，乍一看，迭代代码比递归代码更长更复杂。具体来说，主要区别只是一个 while 循环和一个用于模拟调用堆栈的 [LIFO](https://en.wikipedia.org/wiki/Stack_(abstract_data_type)) 数据结构。

#### 基准

回到代码的概要分析和性能上来，前面几段代码在执行时间上的差异非常有意义。速度差距随着函数调用的数量而增长。

我用 php microtime()做了一点基准测试，结果如下:

| 打电话 | 执行时间迭代 | 执行时间递归 | 最快的 |
| --- | --- | --- | --- |
| one | 100% | 30% - 50% | 递归的 |
| Ten | 100% | 180% - 210% | 交互的 |
| One hundred | 100% | 300% - 450% | 交互的 |
| One thousand | 100% | 700% - 1100% | 交互的 |
| ten thousand | 100% | 600% - 700% | 交互的 |

在 benchmark 中，我模拟了一个依赖于其他类的类。例如，A 类依赖于 B，B 依赖于 C，C 依赖于 D，D 依赖于 E 等等。总共九个嵌套类。

Benchmark 是用 Virtual Box，Ubuntu Server 16.04.2，Apache/2.4.18，php 7.1.7 用 mod-fpm 执行的。虚拟机:英特尔酷睿 2 至尊 CPU X9100，2 个 CPU 最大 40%内存和 512Mb 内存，固态硬盘。基准测试运行了 10 次，递归值被平均。

## 结论

总之，当需要在“如何编写代码”和性能之间做出选择时，对我来说最好是提高执行速度，降低代码的简单性/可读性。在这种情况下，留下更详细的注释(什么和因为，不仅仅是因为)会有所帮助，以免忘记代码做了什么。

当然，前面的考虑并不意味着您不必遵循良好的代码实践。