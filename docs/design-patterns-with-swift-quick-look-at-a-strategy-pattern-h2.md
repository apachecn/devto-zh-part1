# 用 Swift 设计模式:快速浏览策略模式

> 原文：<https://dev.to/brightdevs/design-patterns-with-swift-quick-look-at-a-strategy-pattern-h2>

让我们快速地看一下其中一个设计模式，它应该可以帮助我们编写一个好的面向对象的代码。策略模式的基本假设是你可以定义许多符合协议的实现。

看一个可以在 iOS 应用程序上使用的简单示例。

首先，创建一个包含方法的`protocol`。在我们的案例中，它将是:

## 定义协议

```
protocol ImageDataRepresentation {
    func dataRepresentationFrom(image: UIImage) -> Data?
} 
```

## 创建策略

好的，大多数 iOS 应用程序都使用一个`UIImage`来表示应用程序中的图像。`UIImage`实例可以用来产生图像`UIImagePNGRepresentation`和`UIImageJPEGRepresentation`的两种不同的数据表示。让我们创建处理这些东西的类。

```
class JPEGImageRepresentation: ImageDataRepresentation {
    func dataRepresentationFrom(image: UIImage) -> Data? {
        print("JPEGImageRepresentation strategy called")
        guard let data = UIImageJPEGRepresentation(image, 1.0) else {
            return nil
        }
        return data
    }
}

class PNGImageRepresentation: ImageDataRepresentation {
    func dataRepresentationFrom(image: UIImage) -> Data? {
        print("PNGImageRepresentation strategy called")
        guard let data = UIImagePNGRepresentation(image) else {
            return nil
        }
        return data
    }
} 
```

现在，正如你所看到的——两个类都符合`ImageRepresentation`协议，但是它们在实现上有所不同。每一类代表一种不同的**策略**。

## 创建客户端

最后一件事——创建一个使用`ImageRepresentation`策略之一的客户端。

```
class ImageRepresenter {
    var strategy: ImageDataRepresentation

    init(strategy: ImageRepresentation) {
        self.strategy = strategy
    }

    func imageDataRepresentation(image: UIImage) -> Data? {
        return strategy.dataRepresentationFrom(image: image)
    }
} 
```

## 用法

```
let image = UIImage(named: "i_am_super_sure_that_image_exist")!
let imageRepresenter = ImageRepresenter(strategy: PNGImageRepresentation())
let pngData = imageRepresenter.imageDataRepresentation(image: image)

imageRepresenter.strategy = JPEGImageRepresentation()
let jpegData = imageRepresenter.imageDataRepresentation(image: image) 
```

## 结论

策略模式的酷之处在于我们可以在运行时改变我们的策略。
在使用策略模式时，我们绝对遵循“打开-关闭”的坚实原则。我们的客户端通过改变策略而不改变客户端实现，对扩展是开放的(对修改关闭)。此外，包含策略模式的`ImageRepresenter`将是最容易测试的。

让我们想想如果没有策略模式，上面的代码会是什么样子:

### 使用开关

```
enum ImageRepresentation {
    case jpeg
    case png
}

class ImageRepresenter {
    func imageDataRepresentation(_ representation: ImageRepresentation) {
            switch representation {
            case .jpeg:
                return UIImageJPEGRepresentation(image)
            case .png:
                return UIImagePNGRepresentation(image)
            }
        }

} 
```

### 或使用多种功能

```
class ImageRepresenter {
    func pngDataRepresentation(image: UIImage) -> Data? {
        return UIImagePNGRepresentation(image)
    }

    func jpegDataRepresentation(image: UIImage) -> Data? {
        return UIImageJPEGRepresentation(image, 1.0)
    }
} 
```

这两种解决方案肯定与 CleanCode 不在一条线上。此外，可能很难维护这种代码。switch 语句可以随着下一个案例而增长——如果我们必须处理 10 个、20 个或 100 个策略呢？使用多个函数的第二种方法也不好，因为我们将继续复制相似的方法来处理每种情况。这几个论点应该说服你使用策略模式。最后但同样重要的是，这两个例子打破了开闭原则。

最初发表于 2017 年 10 月 9 日 [brightinventions.pl](https://brightinventions.pl/blog/) 。

由 Kamil Wysocki，软件工程师@光明发明
[博客](https://wysockikamil.com)，