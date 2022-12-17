# 设计模式和实践。net:c#中的选项函数类型

> 原文：<https://dev.to/matheusrodrigues/design-patterns-and-practices-in-net-option-functional-type-in-c-5g3l>

在函数式编程中，选项类型被广泛使用，比如 Haskell、F#、Scala 等等。在这种语言中，当函数失败时返回值是一种惯例。以下示例是一个用 F#编写的函数，展示了选项类型的工作方式:

```
(* This function uses pattern matching to deconstruct Options *)
let compute = function
  | None -> "No value"
  | Some x -> sprintf "The value is: %d" x

printfn "%s" (compute <| Some 42)(* The value is: 42 *)
printfn "%s" (compute None) (* No value *) 
```

在 C#语言中，我们没有 option 类型的对等物，但是我们可以尝试模拟它。

在我的上一篇[文章](https://wp.me/p9d6L8-1h)中，我讨论了两种设计模式，空对象和特例对象，这些模式的目标是返回一个特殊对象，而不是返回空。如果你没有读这篇文章，[去查看一下](https://wp.me/p9d6L8-1h)然后回到这篇文章。

这篇文章的目的是展示 C#中选项类型的实现。

## **问题**

这个问题和我的上一篇文章非常相似，事实上是同一个问题的延续。

```
public IProduct GetProductById(int productId)
{
    Product product = _productRepository.GetProductById(productId);

    if(product == null)
        return new ProductNotFound();

    return product;
} 
```

[继续阅读...](https://www.matheus.ro/2017/09/26/design-patterns-and-practices-in-net-option-functional-type-in-csharp/)