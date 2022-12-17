# 在 Swift 中检查通用类型

> 原文：<https://dev.to/onmyway133/checking-generic-type-in-swift>

原帖[https://github.com/onmyway133/blog/issues/74](https://github.com/onmyway133/blog/issues/74)

当处理泛型时，你不应该关心类型。但是如果你需要，你可以

```
 func isPrimitive<T>(type: T.Type) -> Bool {
  let primitives: [Any.Type] = [
    Bool.self, [Bool].self,
    String.self, [String].self,
    Int.self, [Int].self,
    Float.self, [Float].self,
    Double.self, [Double].self
  ]

  return primitives.contains(where: { $0.self == type.self })
} 
```

Enter fullscreen mode Exit fullscreen mode