# 设计模式和实践。Net:空对象和特例对象

> 原文：<https://dev.to/matheusrodrigues/design-patterns-and-practices-in-net-null-object-and-special-case-object-41i7>

在编程中，空值是一件很难处理的事情，当你返回一个变量并且它可能是空值时，你必须记住在你的代码中进行空值检查。

空对象和特例对象是试图减少这种类型的锅炉板代码的模式。

空对象是没有引用值的对象，是具有默认行为的对象。代表丢失对象的行为。

特例对象与空对象非常相似，但它是对空对象的改进，因为它不是创建一个通用的“缺失对象”，而是创建一个更专门化的对象，可以更好地满足您的需求。

这两种模式目标是返回对象的空版本或特殊版本，而不是返回空引用。

## **问题**

很多次我们发现自己在做这种检查:

```
public Product GetProductById(int productId)
{
    Product product = _productRepository.GetProductById(productId);
    if(product == null)
        throw new Exception("Product Not Found");
    return product;
} 
```

[继续阅读...](https://www.matheus.ro/2017/09/25/design-patterns-practices-net-null-object-special-case-object/)