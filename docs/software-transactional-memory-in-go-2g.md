# Go 中的软件事务内存

> 原文：<https://dev.to/decillion/software-transactional-memory-in-go-2g>

我最近写了 [decillion/go-stm](https://github.com/decillion/go-stm/) ，这是一个针对 go 的软件事务内存(stm)实现。它为 Go 程序员提供了一种新的同步方式，尽管它仍处于试验阶段。

该包使人们能够在没有显式锁定的情况下对共享变量自动执行一系列操作。下面我举一个简单的例子。请查看知识库或[博客文章](https://qiita.com/decillion/items/d5da905e28b54dc769cd)(日语)了解更多信息。

```
 // Initialize two transactional variables.
x := New(0)
y := New(0)

// Define a transaction that transfer 100 from x to y.
transfer := func(rec *TRec) interface{} {
    currX := rec.Load(x).(int) // Read the value of x.
    currY := rec.Load(y).(int) // Read the value of y.
    rec.Store(x, currX-100)    // Write currX-100 to x.
    rec.Store(y, currY+100)    // Write currY-100 to y.
    return nil
}

// Execute the transaction atomically.
Atomically(transfer) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想更多地了解 STM 本身，我推荐你阅读西蒙·佩顿·琼斯写的文章[美丽并发](https://www.microsoft.com/en-us/research/publication/beautiful-concurrency/)。

已经有[Luke champine 写的另一个 STM 包](https://github.com/lukechampine/stm)。它提供了更丰富的 STM 接口，但性能不如目前的软件包。下面是一个非常简单的基准测试的结果，其中两个事务性变量被自动递增或读取。基准测试是在 32 核的 DigitalOcean 的高 CPU Droplet 上在 Ubuntu 17.10 上进行的。

```
Benchmark_Read90Write10_decillion-4         10000000           156 ns/op
Benchmark_Read90Write10_decillion-8         10000000           144 ns/op
Benchmark_Read90Write10_decillion-16        10000000           214 ns/op
Benchmark_Read90Write10_decillion-32         5000000           289 ns/op

Benchmark_Read90Write10_lukechampine-4       2000000           761 ns/op
Benchmark_Read90Write10_lukechampine-8       2000000           822 ns/op
Benchmark_Read90Write10_lukechampine-16      2000000           912 ns/op
Benchmark_Read90Write10_lukechampine-32      2000000           966 ns/op 
```

Enter fullscreen mode Exit fullscreen mode