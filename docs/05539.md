# Swift 中的数字分组

> 原文：<https://dev.to/onmyway133/digit-grouping-in-swift>

原帖[https://github.com/onmyway133/blog/issues/26](https://github.com/onmyway133/blog/issues/26)

当在[标度](https://github.com/onmyway133/Scale)上工作时，我认为有一种将数字分组的方法是好的，这样更容易推理

幸运的是，Swift 已经支持这一点。参见[Swift 编程语言-数字文字](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309)

> 数字文本可以包含额外的格式，以便于阅读。整数和浮点数都可以用额外的零填充，并且可以包含下划线以提高可读性。两种格式都不会影响文本的底层值

```
let paddedDouble = 000123.456
let oneMillion = 1_000_000
let justOverOneMillion = 1_000_000.000_000_1 
```

Enter fullscreen mode Exit fullscreen mode

说到小数点后的数字分组，小数点后的数字分组的[约定也很有意思](http://math.stackexchange.com/questions/182775/convention-of-digit-grouping-after-decimal-point)

所以现在我们有了

```
public enum MetricUnit: Double {
    case nano = 0.000_000_001
    case micro = 0.000_001
    case milli = 0.001
    case centi = 0.01
    case deci = 0.1
    case base = 1
    case deka = 10
    case hecto = 100
    case kilo = 1_000
    case mega = 1_000_000
    case giga = 1_000_000_000
    case tera = 1_000_000_000_000
    case peta = 1_000_000_000_000_000

    static var defaultScale: Double {
        return MetricUnit.base.rawValue
    }
} 
```

Enter fullscreen mode Exit fullscreen mode