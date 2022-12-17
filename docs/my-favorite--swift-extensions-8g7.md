# 我最喜欢的❤️ Swift 扩展

> 原文：<https://dev.to/tattn/my-favorite--swift-extensions-8g7>

这是我经常使用的 Swift 便利扩展的集合。协议和运算符也包括在内。

所有的扩展都在下面的库中。
[https://github.com/tattn/SwiftExtensions](https://github.com/tattn/SwiftExtensions)

*   Swift 的版本是 4.0。
*   这是我用日语写的文章的翻译版本[这里](https://qiita.com/tattn/items/dc7dfe2fceec00bb4ff7)。

# 获取一个类名

```
public protocol ClassNameProtocol {
    static var className: String { get }
    var className: String { get }
}

public extension ClassNameProtocol {
    public static var className: String {
        return String(describing: self)
    }

    public var className: String {
        return type(of: self).className
    }
}

extension NSObject: ClassNameProtocol {} 
```

Enter fullscreen mode Exit fullscreen mode

```
UIView.className   //=> "UIView"
UILabel().className //=> "UILabel" 
```

Enter fullscreen mode Exit fullscreen mode

# 登记/出列 XIB

## UITableView

```
public extension UITableView {
    public func register<T: UITableViewCell>(cellType: T.Type, bundle: Bundle? = nil) {
        let className = cellType.className
        let nib = UINib(nibName: className, bundle: bundle)
        register(nib, forCellReuseIdentifier: className)
    }

    public func register<T: UITableViewCell>(cellTypes: [T.Type], bundle: Bundle? = nil) {
        cellTypes.forEach { register(cellType: $0, bundle: bundle) }
    }

    public func dequeueReusableCell<T: UITableViewCell>(with type: T.Type, for indexPath: IndexPath) -> T {
        return self.dequeueReusableCell(withIdentifier: type.className, for: indexPath) as! T
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
tableView.register(cellType: MyCell.self)
tableView.register(cellTypes: [MyCell1.self, MyCell2.self])

let cell = tableView.dequeueReusableCell(with: MyCell.self, for: indexPath) 
```

Enter fullscreen mode Exit fullscreen mode

## UICollectionView

```
public extension UICollectionView {
    public func register<T: UICollectionViewCell>(cellType: T.Type, bundle: Bundle? = nil) {
        let className = cellType.className
        let nib = UINib(nibName: className, bundle: bundle)
        register(nib, forCellWithReuseIdentifier: className)
    }

    public func register<T: UICollectionViewCell>(cellTypes: [T.Type], bundle: Bundle? = nil) {
        cellTypes.forEach { register(cellType: $0, bundle: bundle) }
    }

    public func register<T: UICollectionReusableView>(reusableViewType: T.Type,
                                                      ofKind kind: String = UICollectionElementKindSectionHeader,
                                                      bundle: Bundle? = nil) {
        let className = reusableViewType.className
        let nib = UINib(nibName: className, bundle: bundle)
        register(nib, forSupplementaryViewOfKind: kind, withReuseIdentifier: className)
    }

    public func register<T: UICollectionReusableView>(reusableViewTypes: [T.Type],
                                                      ofKind kind: String = UICollectionElementKindSectionHeader,
                                                      bundle: Bundle? = nil) {
        reusableViewTypes.forEach { register(reusableViewType: $0, ofKind: kind, bundle: bundle) }
    }

    public func dequeueReusableCell<T: UICollectionViewCell>(with type: T.Type,
                                                             for indexPath: IndexPath) -> T {
        return dequeueReusableCell(withReuseIdentifier: type.className, for: indexPath) as! T
    }

    public func dequeueReusableView<T: UICollectionReusableView>(with type: T.Type,
                                                                 for indexPath: IndexPath,
                                                                 ofKind kind: String = UICollectionElementKindSectionHeader) -> T {
        return dequeueReusableSupplementaryView(ofKind: kind, withReuseIdentifier: type.className, for: indexPath) as! T
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
collectionView.register(cellType: MyCell.self)
collectionView.register(cellTypes: [MyCell1.self, MyCell2.self])
let cell = collectionView.dequeueReusableCell(with: MyCell.self, for: indexPath)

collectionView.register(reusableViewType: MyReusableView.self)
collectionView.register(reusableViewTypes: [MyReusableView1.self, MyReusableView2.self])
let view = collectionView.dequeueReusableView(with: MyReusableView.self, for: indexPath) 
```

Enter fullscreen mode Exit fullscreen mode

# 实例化故事板中的 viewController

```
public enum StoryboardInstantiateType {
    case initial
    case identifier(String)
}

public protocol StoryboardInstantiatable {
    static var storyboardName: String { get }
    static var storyboardBundle: Bundle { get }
    static var instantiateType: StoryboardInstantiateType { get }
}

public extension StoryboardInstantiatable where Self: NSObject {
    public static var storyboardName: String {
        return className
    }

    public static var storyboardBundle: Bundle {
        return Bundle(for: self)
    }

    private static var storyboard: UIStoryboard {
        return UIStoryboard(name: storyboardName, bundle: storyboardBundle)
    }

    public static var instantiateType: StoryboardInstantiateType {
        return .identifier(className)
    }
}

public extension StoryboardInstantiatable where Self: UIViewController {
    public static func instantiate() -> Self {
        switch instantiateType {
        case .initial:
            return storyboard.instantiateInitialViewController() as! Self
        case .identifier(let identifier):
            return storyboard.instantiateViewController(withIdentifier: identifier) as! Self
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Class name and Storyboard name, same as Storyboard ID
final class ViewController: UIViewController, StoryboardInstantiatable {
}

ViewController.instantiate()

// Initial view controller & class name and Storyboard name are the same
final class InitialViewController: UIViewController, StoryboardInstantiatable {
    static var instantiateType: StoryboardInstantiateType {
        return .initial
    }
}

InitialViewController.instantiate() 
```

Enter fullscreen mode Exit fullscreen mode

# 实例化一个 XIB 视图

```
public protocol NibInstantiatable {
    static var nibName: String { get }
    static var nibBundle: Bundle { get }
    static var nibOwner: Any? { get }
    static var nibOptions: [AnyHashable: Any]? { get }
    static var instantiateIndex: Int { get }
}

public extension NibInstantiatable where Self: NSObject {
    public static var nibName: String { return className }
    public static var nibBundle: Bundle { return Bundle(for: self) }
    public static var nibOwner: Any? { return self }
    public static var nibOptions: [AnyHashable: Any]? { return nil }
    public static var instantiateIndex: Int { return 0 }
}

public extension NibInstantiatable where Self: UIView {
    public static func instantiate() -> Self {
        let nib = UINib(nibName: nibName, bundle: nibBundle)
        return nib.instantiate(withOwner: nibOwner, options: nibOptions)[instantiateIndex] as! Self
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// XIB name and class name are the same & 0th View
final class View: UIView, NibInstantiatable {
}

View.instantiate()

// XIB name and class name are different & & 2nd View
final class View2: UIView, NibInstantiatable {
    static var nibName: String { return "Foo" } // Foo.xib
    static var instantiateIndex: Int { return 2 }
}

View2.instantiate() 
```

Enter fullscreen mode Exit fullscreen mode

## 将 Xib 创建的自定义视图放入界面生成器

```
public protocol EmbeddedNibInstantiatable {
    associatedtype Embedded: NibInstantiatable
}

public extension EmbeddedNibInstantiatable where Self: UIView, Embedded: UIView {
    public var embedded: Embedded { return subviews[0] as! Embedded }

    public func configureEmbededView() {
        let view = Embedded.instantiate()
        insertSubview(view, at: 0)
        view.fillSuperview() // described later
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
final class EmbeddedView: UIView, NibInstantiatable {
}

@IBDesignable
final class IBEmbeddedView: UIView, EmbeddedNibInstantiatable {
    typealias Embedded = EmbeddedView

    #if TARGET_INTERFACE_BUILDER
    override func prepareForInterfaceBuilder() {
        super.prepareForInterfaceBuilder()
        configureEmbededView()
    }
    #endif

    override func awakeFromNib() {
        super.awakeFromNib()
        configureEmbededView()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

[![EmbeddedView.xib](img/85409a7115bc4a28a9c2e22032c879ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GcCYky6---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/91631/771d5873-1c95-feb9-7728-3e9bb7fd616e.png)

[![IBEmbeddedView](img/7a51b1edd999dc8ce87877a0eb359ff9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ykiPgSBh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/91631/f0d2dd2d-1e6b-9366-2803-2ec960222228.png)

# 制作一个与超级视图大小相同的视图

```
public extension UIView {
    public func fillSuperview() {
        guard let superview = self.superview else { return }
        translatesAutoresizingMaskIntoConstraints = superview.translatesAutoresizingMaskIntoConstraints
        if translatesAutoresizingMaskIntoConstraints {
            autoresizingMask = [.flexibleWidth, .flexibleHeight]
            frame = superview.bounds
        } else {
            topAnchor.constraint(equalTo: superview.topAnchor).isActive = true
            bottomAnchor.constraint(equalTo: superview.bottomAnchor).isActive = true
            leftAnchor.constraint(equalTo: superview.leftAnchor).isActive = true
            rightAnchor.constraint(equalTo: superview.rightAnchor).isActive = true
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
superView.addSubview(view)
view.fillSuperview() 
```

Enter fullscreen mode Exit fullscreen mode

# 获取最顶层的 uiview controller/UINavigationController

```
public extension UIApplication {
    public var topViewController: UIViewController? {
        guard var topViewController = UIApplication.shared.keyWindow?.rootViewController else { return nil }

        while let presentedViewController = topViewController.presentedViewController {
            topViewController = presentedViewController
        }
        return topViewController
    }

    public var topNavigationController: UINavigationController? {
        return topViewController as? UINavigationController
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
UIApplication.shared.topViewController 
```

Enter fullscreen mode Exit fullscreen mode

# 获取一个拥有视图的视图控制器

```
public extension UIView {
    public var viewController: UIViewController? {
        var parent: UIResponder? = self
        while parent != nil {
            parent = parent?.next
            if let viewController = parent as? UIViewController {
                return viewController
            }
        }
        return nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
view.viewController 
```

Enter fullscreen mode Exit fullscreen mode

# 用十六进制创建一个 UIColor

```
public extension UIColor {
    public convenience init(hex: Int, alpha: CGFloat = 1.0) {
        let r = CGFloat((hex & 0xFF0000) >> 16) / 255.0
        let g = CGFloat((hex & 0x00FF00) >> 8) / 255.0
        let b = CGFloat(hex & 0x0000FF) / 255.0
        self.init(red: r, green: g, blue: b, alpha: alpha)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let color = UIColor(hex: 0xAABBCC) 
```

Enter fullscreen mode Exit fullscreen mode

# 通过数组中的实例删除元素

```
public extension Array where Element: Equatable {
    @discardableResult
    public mutating func remove(element: Element) -> Index? {
        guard let index = index(of: element) else { return nil }
        remove(at: index)
        return index
    }

    @discardableResult
    public mutating func remove(elements: [Element]) -> [Index] {
        return elements.flatMap { remove(element: $0) }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let array = ["foo", "bar"]
array.remove(element: "foo")
array //=> ["bar"] 
```

Enter fullscreen mode Exit fullscreen mode

# 从数组中删除相同的值

```
public extension Array where Element: Hashable {
    public mutating func unify() {
        self = unified()
    }
}

public extension Collection where Element: Hashable {
    public func unified() -> [Element] {
        return Array(Set(self))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let array = [1, 2, 3, 3, 2, 1, 4]
array.unify() // [1, 2, 3, 4] 
```

Enter fullscreen mode Exit fullscreen mode

# 在安全中获得一个元素。

```
public extension Collection {
    public subscript(safe index: Index) -> Element? {
        return startIndex <= index && index < endIndex ? self[index] : nil
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let array = [0, 1, 2]
if let item = array[safe: 5] {
    print("unreachable")
} 
```

Enter fullscreen mode Exit fullscreen mode

# 获取不同范围的子字符串

```
public extension String {
    subscript (bounds: CountableClosedRange<Int>) -> String {
        let start = index(startIndex, offsetBy: bounds.lowerBound)
        let end = index(startIndex, offsetBy: bounds.upperBound)
        return String(self[start...end])
    }

    subscript (bounds: CountableRange<Int>) -> String {
        let start = index(startIndex, offsetBy: bounds.lowerBound)
        let end = index(startIndex, offsetBy: bounds.upperBound)
        return String(self[start..<end])
    }

    subscript (bounds: PartialRangeUpTo<Int>) -> String {
        let end = index(startIndex, offsetBy: bounds.upperBound)
        return String(self[startIndex..<end])
    }

    subscript (bounds: PartialRangeThrough<Int>) -> String {
        let end = index(startIndex, offsetBy: bounds.upperBound)
        return String(self[startIndex...end])
    }

    subscript (bounds: CountablePartialRangeFrom<Int>) -> String {
        let start = index(startIndex, offsetBy: bounds.lowerBound)
        return String(self[start..<endIndex])
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let string = "0123456789"
string[0...5] //=> "012345"
string[1...3] //=> "123"
string[3..<7] //=> "3456"
string[...4]  //=> "01234
string[..<4]  //=> "0123"
string[4...]  //=> "456789" 
```

Enter fullscreen mode Exit fullscreen mode

# 从字符串创建 URL/从字符串文字创建 URL

```
public extension String {
    public var url: URL? {
        return URL(string: self)
    }
}

extension URL: ExpressibleByStringLiteral {
    public init(stringLiteral value: String) {
        guard let url = URL(string: value) else {
            fatalError("\(value) is an invalid url")
        }
        self = url
    }

    public init(extendedGraphemeClusterLiteral value: String) {
        self.init(stringLiteral: value)
    }

    public init(unicodeScalarLiteral value: String) {
        self.init(stringLiteral: value)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
if let url = "https://example.com".url {
}

let url: URL = "https://example.com" 
```

Enter fullscreen mode Exit fullscreen mode

# 使用类似 Kotlin 的作用域函数

```
public protocol Appliable {}

public extension Appliable {
    @discardableResult
    public func apply(closure: (Self) -> Void) -> Self {
        closure(self)
        return self
    }
}

public protocol Runnable {}

public extension Runnable {
    @discardableResult
    public func run<T>(closure: (Self) -> T) -> T {
        return closure(self)
    }
}

extension NSObject: Appliable {}
extension NSObject: Runnable {} 
```

Enter fullscreen mode Exit fullscreen mode

```
let view = UIView().apply {
    $0.backgroundColor = .red
    $0.frame = .init(x: 0, y: 0, width: 200, height: 200)
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如果值为零，则通过`???`抛出错误

```
infix operator ???

public func ???<T>(lhs: T?,
                   error: @autoclosure () -> Error) throws -> T {
    guard let value = lhs else { throw error() }
    return value
} 
```

Enter fullscreen mode Exit fullscreen mode

```
let value: String? = nil

struct OptionalError: Error {}

do {
    let v = try value ??? OptionalError()
    print(v) // unreachable
} catch {
    print(error) //=> OptionalError
} 
```

Enter fullscreen mode Exit fullscreen mode

# 从字典中获取值时，如果没有键，则抛出错误

```
public struct DictionaryTryValueError: Error {
    public init() {}
}

public extension Dictionary {
    func tryValue(forKey key: Key, error: Error = DictionaryTryValueError()) throws -> Value {
        guard let value = self[key] else { throw error }
        return value
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
var dictionary: [String: Int] = [:]
do {
    let value = try dictionary.tryValue(forKey: "foo")
    print(value) // unreachable
} catch {
    print(error) //=> DictionaryTryValueError
} 
```

Enter fullscreen mode Exit fullscreen mode

# 创建可通过`.ex`访问的属性和方法

```
public struct TargetedExtension<Base> {
    let base: Base
    init (_ base: Base) {
        self.base = base
    }
}

public protocol TargetedExtensionCompatible {
    associatedtype Compatible
    static var ex: TargetedExtension<Compatible>.Type { get }
    var ex: TargetedExtension<Compatible> { get }
}

public extension TargetedExtensionCompatible {
    public static var ex: TargetedExtension<Self>.Type {
        return TargetedExtension<Self>.self
    }

    public var ex: TargetedExtension<Self> {
        return TargetedExtension(self)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
extension UIView: TargetedExtensionCompatible {}

private extension TargetedExtension where Base: UIView {
    func foo() {}
}

UIView().ex.foo() 
```

Enter fullscreen mode Exit fullscreen mode