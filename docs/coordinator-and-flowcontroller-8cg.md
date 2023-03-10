# 协调员和流量控制器

> 原文：<https://dev.to/onmyway133/coordinator-and-flowcontroller-8cg>

原帖[https://github.com/onmyway133/blog/issues/106](https://github.com/onmyway133/blog/issues/106)

每一个新出现的架构，无论是 iOS T1 还是 T2 Android T3，都让我非常兴奋。我一直在寻找以更好的方式构建应用程序的方法。但是过了一段时间后，我发现我们在创建架构时太有创造力了，也就是说太受约束了，这离我们正在构建的平台太远了。

我喜欢拥抱系统的事物。其中一个是[协调器](http://khanlou.com/2015/10/coordinators-redux/)，它帮助封装和导航。感谢我的朋友[瓦迪姆](https://github.com/vadymmarkov/)给我展示了`Coordinator`的行动。

但是在看了[一个更好的 MVC](https://davedelong.com/blog/2017/11/06/a-better-mvc-part-3-fixing-massive-view-controller/) 之后，我认为我们仅仅依靠`UIViewController`就可以拥抱 MVC。

既然我倾向于称视图控制器为`LoginController, ProfileController, ...`和术语`flow`来分组那些相关的屏幕，那么我们应该如何称呼从`UIViewController`继承而来的`Coordinator`🤔姑且称之为`FlowController`😎

让我们看看`FlowController`如何更好地融入`MVC`

## 流量控制器作为容器视图控制器

> 一般来说，视图控制器应该管理序列或 UI，但不能同时管理两者。

基本上，`FlowController`只是一个解决`sequence`的容器视图控制器，基于一个叫做`composition`的简单概念。它在其流程中管理许多子视图控制器。假设我们有一个`ProductFlowController`，它将与展示产品相关的流程组合在一起，`ProductListController`，`ProductDetailController`，`ProductAuthorController`，`ProductMapController`，...每一个都可以委托给`ProductFlowController`来表达它的意图，就像`ProductListController`可以委托说“产品已点击”,这样`ProductFlowController`就可以基于其内部嵌入的`UINavigationController`来构造和呈现流中的下一个屏幕。

通常，一个`FlowController`一次只显示一个孩子`FlowController`，所以通常我们可以只更新它的帧

```
final class AppFlowController: UIViewController {
  override func viewDidLayoutSubviews() {
    super.viewDidLayoutSubviews()

    childViewControllers.first?.view.frame = view.bounds
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 作为依赖容器的流量控制器

流中的每个视图控制器可以有不同的依赖关系，所以如果第一个视图控制器需要携带所有的东西，以便能够传递给下一个视图控制器，这是不公平的。以下是一些依赖项

*   product list controller:ProductNetworkingService
*   ProductDetailController:ProductNetworkingService，ImageDowloaderService，ProductEditService
*   ProductAuthorController:AuthorNetworkingService，ImageDowloaderService
*   ProductMapController:location service，MapService

相反，`FlowController`可以携带整个流程所需的所有依赖项，因此如果需要的话，它可以传递给视图控制器。

```
struct ProductDependencyContainer {
  let productNetworkingService: ProductNetworkingService
  let imageDownloaderService: ImageDownloaderService
  let productEditService: ProductEditService
  let authorNetworkingService: AuthorNetworkingService
  let locationService: LocationService
  let mapService: MapService
}

class ProductFlowController {
  let dependencyContainer: ProductDependencyContainer

  init(dependencyContainer: ProductDependencyContainer) {
    self.dependencyContainer = dependencyContainer
  }
}

extension ProductFlowController: ProductListController {
  func productListController(_ controller: ProductListController, didSelect product: Product) {
    let productDetailController = ProductDetailController(
      productNetworkingService: dependencyContainer.productNetworkingService,
      productEditService: dependencyContainer.productEditService,
      imageDownloaderService: dependencyContainer.imageDownloaderService
    )

    productDetailController.delegate = self
    embeddedNavigationController.pushViewController(productDetailController, animated: true)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 添加或删除子流量控制器

**协调人**

对于`Coordinator`，你需要保存一个子`Coordinators`的数组，并且可能使用地址(`===`操作符)来标识它们

```
class Coordinator {
  private var children: [Coordinator] = []

  func add(child: Coordinator) {
    guard !children.contains(where: { $0 === child }) else {
      return
    }

    children.append(child)
  }

  func remove(child: Coordinator) {
    guard let index = children.index(where: { $0 === child }) else {
      return
    }

    children.remove(at: index)
  }

  func removeAll() {
    children.removeAll()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**流量控制器**

对于`FlowController`，因为它是`UIViewController`子类，所以它有`viewControllers`来保存所有这些子`FlowController`。只需添加这些扩展来简化您添加或删除子`UIViewController`T5】

```
extension UIViewController {
  func add(childController: UIViewController) {
    addChildViewController(childController)
    view.addSubview(childController.view)
    childController.didMove(toParentViewController: self)
  }

  func remove(childController: UIViewController) {
    childController.willMove(toParentViewController: nil)
    childController.view.removeFromSuperview()
    childController.removeFromParentViewController()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

看看`AppFlowController`是如何工作的

```
final class AppFlowController: UIViewController {
  func start() {
    if authService.isAuthenticated {
      startMain()
    } else {
      startLogin()
    }
  }

  private func startLogin() {
    let loginFlowController = LoginFlowController(
    loginFlowController.delegate = self
    add(childController: loginFlowController)
    loginFlowController.start()
  }

  fileprivate func startMain() {
    let mainFlowController = MainFlowController()
    mainFlowController.delegate = self
    add(childController: mainFlowController)
    mainFlowController.start()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### AppFlowController 不需要知道 UIWindow

**协调人**

通常你有一个由`AppDelegate`持有的`AppCoordinator`，作为你的`Coordinator`链的根。根据登录状态，它将确定哪个`LoginController`或`MainController`将被设置为`rootViewController`，为了做到这一点，需要注入一个`UIWindow`

```
window = UIWindow(frame: UIScreen.main.bounds)
appCoordinator = AppCoordinator(window: window!)
appCoordinator.start()
window?.makeKeyAndVisible() 
```

Enter fullscreen mode Exit fullscreen mode

你可以猜到在`AppCoordinator`的`start`方法中，它必须在`window?.makeKeyAndVisible()`被调用之前设置`rootViewController`。

```
final class AppCoordinator: Coordinator {
  private let window: UIWindow

  init(window: UIWindow) {
    self.window = window
  }

  func start() {
    if dependencyContainer.authService.isAuthenticated {
      startMain()
    } else {
      startLogin()
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**流量控制器**

但是对于`AppFlowController`，你可以像对待普通的`UIViewController`一样对待它，所以只要把它设置为`rootViewController`T3 就可以了

```
appFlowController = AppFlowController(
window = UIWindow(frame: UIScreen.main.bounds)
window?.rootViewController = appFlowController
window?.makeKeyAndVisible()

appFlowController.start() 
```

Enter fullscreen mode Exit fullscreen mode

### LoginFlowController 可以管理自己的流量

假设我们有基于`UINavigationController`的登录流，可以显示`LoginController`、`ForgetPasswordController`、`SignUpController`

**协调人**

在`LoginCoordinator`的`start`方法中我们应该怎么做？构造初始控制器`LoginController`并设置为`UINavigationController`的`rootViewController`？`LoginCoordinator`可以在内部创建这个内嵌的`UINavigationController`，但是之后它就不附属于`UIWindow`的`rootViewController`了，因为`UIWindow`是在父`AppCoordinator`内部私有保存的。

我们可以将`UIWindow`传递给`LoginCoordinator`，但是它知道的太多了。一种方法是从`AppCoordinator`构造`UINavigationController`，并将其传递给`LoginCoordinator`

```
final class AppCoordinator: Coordinator {
  private let window: UIWindow

  private func startLogin() {
    let navigationController = UINavigationController()

    let loginCoordinator = LoginCoordinator(navigationController: navigationController)

    loginCoordinator.delegate = self
    add(child: loginCoordinator)
    window.rootViewController = navigationController
    loginCoordinator.start()
  }
}

final class LoginCoordinator: Coordinator {
  private let navigationController: UINavigationController

  init(navigationController: UINavigationController) {
    self.navigationController = navigationController
  }

  func start() {
    let loginController = LoginController(dependencyContainer: dependencyContainer)
    loginController.delegate = self

    navigationController.viewControllers = [loginController]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**流量控制器**

`LoginFlowController`利用`container view controller`,所以它非常适合`UIKit`的工作方式。这里的`AppFlowController`可以只添加`LoginFlowController`，而`LoginFlowController`可以只创建自己的`embeddedNavigationController`。

```
final class AppFlowController: UIViewController {
  private func startLogin() {
    let loginFlowController = LoginFlowController(
      dependencyContainer: dependencyContainer
    )

    loginFlowController.delegate = self
    add(childController: loginFlowController)
    loginFlowController.start()
  }
}

final class LoginFlowController: UIViewController {
  private let dependencyContainer: DependencyContainer
  private var embeddedNavigationController: UINavigationController!
  weak var delegate: LoginFlowControllerDelegate?

  init(dependencyContainer: DependencyContainer) {
    self.dependencyContainer = dependencyContainer
    super.init(nibName: nil, bundle: nil)

    embeddedNavigationController = UINavigationController()
    add(childController: embeddedNavigationController)
  }

  func start() {
    let loginController = LoginController(dependencyContainer: dependencyContainer)
    loginController.delegate = self

    embeddedNavigationController.viewControllers = [loginController]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 流量控制器和响应器链

**协调人**

有时我们想要一种快速的方法来将消息发送给父节点`Coordinator`，一种方法是使用`associated object`和协议扩展来复制`UIResponder`链，比如[与协调器](http://aplus.rs/2017/highly-maintainable-app-architecture/)互连

```
extension UIViewController {
    private struct AssociatedKeys {
        static var ParentCoordinator = "ParentCoordinator"
    }

    public var parentCoordinator: Any? {
        get {
            return objc_getAssociatedObject(self, &AssociatedKeys.ParentCoordinator)
        }
        set {
            objc_setAssociatedObject(self, &AssociatedKeys.ParentCoordinator, newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
}

open class Coordinator<T: UIViewController>: UIResponder, Coordinating {
    open var parent: Coordinating?  

    override open var coordinatingResponder: UIResponder? {
        return parent as? UIResponder
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**流量控制器**

由于`FlowController`是从`UIResponder`继承而来的`UIViewController`，响应器链是开箱即用的

> Responder 对象(即 UIResponder 的实例)构成了 UIKit 应用程序的事件处理主干。许多关键对象也是响应者，包括 UIApplication 对象、UIViewController 对象和所有 UIView 对象(包括 UIWindow)。当事件发生时，UIKit 会将它们分派给应用程序的 responder 对象进行处理。

### 流量控制器和特性集合

**流量控制器**

我非常喜欢 [Kickstarter](https://github.com/kickstarter/ios-oss/blob/master/Kickstarter-iOS.playground/Sources/playgroundController.swift#L43) 在测试中使用特征收集的方式。好吧，因为`FlowController`是一个父视图控制器，我们可以覆盖它的特征集合，这将影响流中所有视图控制器的大小类。

如在[一个更好的 MVC，第 2 部分:修复封装](https://davedelong.com/blog/2017/11/06/a-better-mvc-part-2-fixing-encapsulation/)

> 这种方法的巨大优势是系统特性是免费的。性状收集繁殖是免费的。视图生命周期回调是免费的。安全区布局边距一般是自由的。响应链和首选 UI 状态回调是免费的。未来对 UIViewController 的添加也是免费的。

```
loginFlowController.setOverrideTraitCollection 
```

Enter fullscreen mode Exit fullscreen mode

### 流量控制器和返回按钮

**协调人**

`UINavigationController`的一个问题是点击默认的`back button`会将视图控制器弹出导航栈，所以`Coordinator`并不知道。与`Coordinator`你需要保持`Coordinator`和`UIViewController`同步，并尝试连接`UINavigationControllerDelegate`以便清理。像是在[后退按钮和](http://khanlou.com/2017/05/back-buttons-and-coordinators/)协调器

```
extension Coordinator: UINavigationControllerDelegate {    
    func navigationController(navigationController: UINavigationController,
        didShowViewController viewController: UIViewController, animated: Bool) {

        // ensure the view controller is popping
        guard
          let fromViewController = navigationController.transitionCoordinator?.viewController(forKey: .from),
          !navigationController.viewControllers.contains(fromViewController) else {
            return
        }

        // and it's the right type
        if fromViewController is FirstViewControllerInCoordinator) {
            //deallocate the relevant coordinator
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

或者创建一个名为`NavigationController`的类，在里面管理一个子协调器列表。就像在[导航协调员](http://irace.me/navigation-coordinators)T3】

```
final class NavigationController: UIViewController {
  // MARK: - Inputs

  private let rootViewController: UIViewController

  // MARK: - Mutable state

  private var viewControllersToChildCoordinators: [UIViewController: Coordinator] = [:]

  // MARK: - Lazy views

  private lazy var childNavigationController: UINavigationController =
      UINavigationController(rootViewController: self.rootViewController)

  // MARK: - Initialization

  init(rootViewController: UIViewController) {
    self.rootViewController = rootViewController

    super.init(nibName: nil, bundle: nil)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**流量控制器**

由于`FlowController`只是普通的`UIViewController`，所以不需要手动管理子`FlowController`。孩子`FlowController`在你弹出或者解散的时候就没了。如果我们想监听`UINavigationController`事件，我们可以在`FlowController`
中处理它

```
final class LoginFlowController: UIViewController {
  private let dependencyContainer: DependencyContainer
  private var embeddedNavigationController: UINavigationController!
  weak var delegate: LoginFlowControllerDelegate?

  init(dependencyContainer: DependencyContainer) {
    self.dependencyContainer = dependencyContainer
    super.init(nibName: nil, bundle: nil)

    embeddedNavigationController = UINavigationController()
    embeddedNavigationController.delegate = self
    add(childController: embeddedNavigationController)
  }
}

extension LoginFlowController: UINavigationControllerDelegate {
  func navigationController(_ navigationController: UINavigationController, willShow viewController: UIViewController, animated: Bool) {

  }
} 
```

Enter fullscreen mode Exit fullscreen mode