# 带 Swift 的 genstrings

> 原文：<https://dev.to/masakihori/genstrings-with-swift-11me>

第一步
Localizable . swft 中的可本地化协议

```
import Foundation

protocol Localizable {

    var key: String { get }
    var bundle: Bundle { get }
    var comment: String { get }

    var string: String { get }
}

extension Localizable {

    var string: String {

        return NSLocalizedString(key, tableName: nil, bundle: bundle, comment: comment)
    }
}

struct LocalizedString: Localizable {

    let key: String
    let bundle: Bundle = .main
    let comment: String

    init(_ string: String, comment: String) {
        self.key = string
        self.comment = comment
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第二步
localized strings . swift 中的本地化字符串结构

这个文件是实际本地化设置。

```
struct LocalizedStrings {

    static let hoge = LocalizedString("Hoge", comment: "Hoge Comment")
    static let foo = LocalizedString("Foo", comment: "Foo Comment")
} 
```

Enter fullscreen mode Exit fullscreen mode

第三步
运行带有“s”选项的 genstrings 到“localized strings . swift”

```
genstrings -s LocalizedString LocalizedStrings.swift 
```

Enter fullscreen mode Exit fullscreen mode

第四步
在你的代码中使用本地化的字符串。

```
let foo = LocalizedStrings.foo.string  // getting localized string. 
```

Enter fullscreen mode Exit fullscreen mode