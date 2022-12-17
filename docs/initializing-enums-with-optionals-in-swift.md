# 在 Swift 中使用选项初始化枚举

> 原文：<https://dev.to/onmyway133/initializing-enums-with-optionals-in-swift>

原帖[https://github.com/onmyway133/blog/issues/49](https://github.com/onmyway133/blog/issues/49)

今天有人向我展示了 https://medium . com/@ \ _ Easy \ _ E/initializing-enums-with-optionals-in-swift-BF 246 ce 20 e4c，它试图用可选值初始化 enum。

```
enum Planet: String {
  case mercury
  case venus
  case earth
  case mars
  case jupiter
  case saturn
  case uranus
  case neptune
}

extension RawRepresentable {
  init?(optionalValue: RawValue?) {
    guard let value = optionalValue else { return nil }
    self.init(rawValue: value)
  }
}

let name: String? = "venus"
let planet = Planet(optionalValue: name) 
```

Enter fullscreen mode Exit fullscreen mode

关于可选的一个有趣的事实是，它是一个单子，所以它有`map`和`flatMap`。由于`enum`T3 返回一个可选，我们需要使用`flatMap`。看起来是这样的

```
let name: String? = "venus"
let planet = name.flatMap({ Planet(rawValue: $0) }) 
```

Enter fullscreen mode Exit fullscreen mode

她在哪里