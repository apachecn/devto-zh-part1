# ★异步地图功能

> 原文：<https://dev.to/freekmurze/an-async-map-function-a75>

Laravel 有[一个优秀的收集类](https://laravel.com/docs/5.5/collections)，它有[许多有用的操作](https://laravel.com/docs/5.5/collections#available-methods)。这个类也是[可宏化的](https://laravel.com/docs/5.5/collections#extending-collections)。这意味着您可以在运行时通过调用它的`macro`并传递一个名称和一个闭包来添加函数。在我们的项目中，我们倾向于一遍又一遍地编写相同的宏代码。这就是为什么我们把这些宏放在[一个叫做 laravel-collection-macros](https://github.com/spatie/laravel-collection-macros) 的包中，这样我们和社区就可以重用它们。在这篇文章中，我想谈一谈[我们今天添加的一个新的宏](https://github.com/spatie/laravel-collection-macros#parallelmap)叫做`parallelMap`。

`parallelmap`与 map 相同，但集合中的每个项目将被并行处理。我们来看一个例子:

```
$pageSources = collect($urls)->parallelMap(function($url) {
    return file_get_contents($url);
}); 
```

给定的`$urls`的内容将同时被取出。这比一个接一个地获取 url 的内容要快得多。很酷的东西！

这是另一段来自[我们的测试](https://github.com/spatie/laravel-collection-macros/blob/master/tests/ParallelMapTest.php) :
的代码

```
/** @test */
public function it_can_perform_async_map_operations()
{
    $this->startStopWatch();

    $collection = Collection::make([1, 2, 3, 4, 5])->parallelMap(function (int $number) {
        sleep(1);

        return $number * 10;
    });

    $this->assertTookLessThanSeconds(2);

    $this->assertEquals([10, 20, 30, 40, 50], $collection->toArray());
} 
```

你可能想知道这种魔法是如何工作的。嗯，困难的部分是在 [Amp](https://amphp.org/) 的新包 [`parallel-functions`](https://github.com/amphp/parallel-functions) 里面完成的。下面是从[他们的文档](https://amphp.org/parallel-functions/)中摘录的简短描述:

> amphp/parallel-functions 是 amphp/parallel 之上的一个简化层。根据安装的扩展，它允许通过利用线程或进程来并行执行代码。所有发送到/接收自子进程/线程的数据必须使用 PHP 的`serialize()`函数进行序列化。

这里有一个例子，同样取自他们的文档，关于如何直接使用这个包:

```
use Amp\Promise;
use function Amp\ParallelFunctions\parallelMap;

$values = Promise\wait(parallelMap([1, 2, 3], function ($time) {
    \sleep($time); // a blocking function call, might also do blocking I/O here

    return $time * $time;
})); 
```

我们包中的`parallelMap`宏简单地使用了他们的魔法。下面是宏的[定义:](https://github.com/spatie/laravel-collection-macros/blob/b201b7a/src/macros/parallelMap.php) 

```
Collection::macro('parallelMap', function (callable $callback): Collection {
    $promises = parallelMap($this->items, $callback);

    $this->items = wait($promises);

    return $this;
}); 
```

注意，如果闭包中完成的工作非常简单，就不应该使用`parallelMap`。使用`parallelMap`会导致相当大的开销，并且是内存密集型的。不要将此用于小型操作或大型集合。

感谢[尼克拉斯凯勒](https://twitter.com/kelunik)的 [Amp](https://github.com/amphp/amp) 和那个精彩的 [`amphp/parallel-functions`](https://github.com/amphp/parallel-functions) 。