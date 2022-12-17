# Golang 的变函数

> 原文：<https://dev.to/trknhr/variadic-functions-of-golang-dlc>

# 三个圆点

可变函数可以用任何参数调用。三个点可以取任意数量的 T(T 是同一类型)。

在内部函数中，它可以用作切片。在调用可变函数方面，用户使用非常灵活的方式。不管怎样，论点只有一个，多个。下面是一个例子。

```
func sum(numbers ...int){
    sum := 0
    for _, num := range numbers {
        sum += num
    }
    return sum
}
sum(1,2,3) //6
sum([]int{1,2,3}...) //6
sum(1) //1 
```

Enter fullscreen mode Exit fullscreen mode

# 语法简单

如果你创建的函数想要接收任意数量的参数。你最好用它。有两种方法可以解决这种情况。

```
func f(ids []int){
//
}
func service(id int){
    f([]int{id})
}
func service2(id []int){
    f(id)
} 
```

Enter fullscreen mode Exit fullscreen mode

在`service`中，需要新的切片来调整`f`的参数。
这有点麻烦

让我们用变函数代替切片。

```
func f(ids ...int){
//
}
func service(id int){
    f(id)
}
func service2(id []int){
    f(id...)
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，一个切片参数需要在名称后面有三个点。这意味着应用可变参数。

可变函数不仅是取切片参数的语法糖，而且无助于保持代码库的简单性，以及函数的易用性。