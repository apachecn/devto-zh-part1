# AngularJS 1.x 与 TypeScript(或 ES6)最佳实践

> 原文：<https://dev.to/martinmcwhorter/angularjs-1-x-with-typescript-or-es6-best-practices-b9a>

在过去几年使用 AngularJS 和 TypeScript 之后，有一些最佳实践似乎在大多数教程中消失了。

无论您是在普通的旧 JavaScript (ES5)、ES6 (ES2015)还是 TypeScript 中开发，这些实践都适用。

代码永远不应该是面向未来的，相反，它应该是可扩展的。遵循这些实践应该有助于你创建易于升级到 Angular2 的代码——并且更易于维护。

### 使用外部模块

使用外部模块将为您做两件事。

1.  当捆绑您的应用程序时，外部模块将自动为您排序您的依赖关系。
2.  外部模块将不再需要使用`///<reference path="..." />`符号。

示例:

应用程序

```
import {module} from 'angular'; 

export let app = module('app', [
  require('angular-ui-router'), 
  require('angular-animate'), 
  require('angular-ui-bootstrap'), 
  require('angular-translate')
]); 
```

Enter fullscreen mode Exit fullscreen mode

PersonComponent.ts

```
import {app} from './app'; 

export class PersonComponent { 
  // . . . 
} 

app.component('PersonComponent', PersonComponent); 
```

Enter fullscreen mode Exit fullscreen mode

#### 不使用 TypeScript 内部模块/名称空间

在普遍使用外部模块之前，通常使用 TypeScript 内部模块，现在更名为名称空间。

TypeScript 命名空间基于 JavaScript 内部模块模式。这种模式的出现是因为 JavaScript 中缺乏模块封装。随着 CommonJS 和 ES6 模块和模块语法的引入，应该避免内部模块模式。

使用外部 commonJS/ES6 模块，而不是 TypeScript namespeces。

#### 不使用 IIFE(直接调用的函数表达式)

IIFEs 在 JavaScript 开发中很常见，因为它们允许您封装代码。

```
(function() { 
  // encapsulated closure protected from other code 
})(); 
```

Enter fullscreen mode Exit fullscreen mode

使用外部模块消除了在 IIFE 闭包中显式包装代码的需要。相反，构建时任务(browserify、jspm 或 webpack)和模块系统会将您的代码捆绑并加载到闭包中。

IIFEs 还会导致从模块中导出类型的问题。

#### 只为一个目的创建 AngularJS 模块

将 AngularJS 模块的数量保持在所需的最低水平。有理由创建新的 AngularJS 模块。

比如:

*   在不同的应用程序之间共享公共代码
*   让代码更易测试

AngularJS 模块的产生是因为 JavaScript 中缺乏模块系统。使用 ES6 模块语法和 commonJS 模块，AngularJS 内部模块是 AngularJS 1.x 的遗留产物。

### 将服务定义为类

将你的服务定义为类，AngularJS 的 DI 机制将实例化为单例，这将使你的代码更干净，更容易阅读，更容易维护和测试。

<figure>

最后，将您的服务定义为一个类将使您的代码对使用 TypeScript 的`static typing`更加友好。

<figcaption>userProxy.ts</figcaption>

</figure>

```
import {IHttpService} from 'angular';
import {app} from '../app'; 

export class UserProxy { 
  static $inject = ['$http']; 

  constructor(private $http: IHttpService) { } 

  login(login: UserLogin) { 
    return this.$http.post('/api/login', login); 
   } 

  logout() { 
    return this.$http.post('/api/logout', {}); 
  } 
} 

app.service('userProxy', UserProxy); 
```

Enter fullscreen mode Exit fullscreen mode

```
import {app} from '../app'; 
import {UserProxy} from './userProxy'; 

export default class LoginController { 

  static $inject = ['userProxy']; 

  constructor(private userProxy: UserProxy) {} 

  model: UserLogin = {}; 

  submit(model: UserLogin) { 
    this.userProxy.login(model).then( () => { 
      // handle success 
    }, () => { 
      // handle error 
    }); 
  } 
} 

app.controller('LoginController', LoginController); 
```

Enter fullscreen mode Exit fullscreen mode

在前面的例子中，我们演示了使用导入的干净类型封装。

1.  我们导入类型`import {UserProxy} from './userProxy';`
2.  然后我们在构造函数注入器中使用导入的类型，`constructor(private userProxy: UserProxy) {}`
3.  最后，我们演示了导入的静态类型的使用`this.userProxy.login(model).then(`

与使用 TypeScript 内部模块/命名空间访问类型相比，这是一种更易于管理的方法。

#### 仅在需要时使用出厂方法

在大多数情况下，你的服务将是单身。使用`service`方法将这些与 AngularJS 的 DI、`app.service('userProxy', UserProxy)`进行注册。

`factory`方法的明显用例是在使用工厂模式时。我们以之前的`UserProxy`类为例。对于这个例子，我们假设有不止一个 JSON/HTTP 端点实现同一个 API。我们可以通过工厂重用这个类。

让我们将类更新成这样:

```
import {app} from '../app'; 

export class UserProxy { 

  constructor(private $http: ng.IHttpService, private basePath: string) {}

  login(login: UserLogin) { 
    return this.$http.post(this.basePath + '/login', login); 
  } 

  logout() { 
    return this.$http.post(this.basePath + '/logout', {}); 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以使用工厂来创建该类的实例:

用户代理系数. ts

```
import {IHttpService} from 'angular';
import {app} from '../app'; 
import {UserProxy} from './userProxy'; 

export UserProxy; 
export type userProxyFactory = (basePath: string) => UserProxy; 

userProxy.$inject = ['$http']; 
function userProxy($http: IHttpService): userProxyFactory { 
  return (basePath: string) => { 
    return new UserProxy($http, basePath);
  } 
} 

app.factory('userProxy', userProxy); 
```

Enter fullscreen mode Exit fullscreen mode

这可以在控制器中用作:

```
import {app} from '../app'; 
import {userProxyFactory, UserProxy} from './userProxyFactory'; 

export default class LoginController { 
  private userProxy: UserProxy; 

  static $inject = ['userProxy']; 
  constructor(userProxyFactory: userProxyFactory) { 
    this.userProxy = userProxyFactory('/api1'); 
  } 

  model: UserLogin = {}; 

  submit(model: UserLogin) { 
    this.userProxy.login(model).then(
      () => { 
        // handle success 
      }, 
      () => { 
        // handle error 
      }); 
  } 
} 

app.controller('LoginController', LoginController); 
```

Enter fullscreen mode Exit fullscreen mode

### 直接绑定到控制器属性和方法

许多例子仍然将方法和属性绑定到控制器中注入的`$scope`。

```
// DON'T DO THIS 
export class PersonController { 

  static $inject = ['$scope']; 
  constructor(private $scope: ng.IScope) { 

    $scope.name = "Person's Name"; 

    $scope.save = () => { 
      // . . . 
    } 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

由于一些原因，这是一种不好的做法。

> 1.  This has an effect on memory. Each instance of the controller has its own copy of each method bound to the scope. If these methods are defined as instance methods, then the implementation will be shared among instances.
> 2.  The API of this class cannot be exported or used in unit tests. For larger nested applications, you will encounter scope inheritance conflicts. It seems unreasonable that these collisions will lead to strange behaviors. They are usually hard to find.
> 3.  If the expression contains the attribute parent object, `person.name` is easier to pass the value by reference than `name`.

相反，可以这样定义上面的控制器:

```
export class PersonController { 
  name = "Person's Name";

  save() { 
    // . . . 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`ng-router`和`ui-router`，你只需要用`controllerAs`配置属性来命名你的控制器实例。

### 使用 TypeScript 进行单元测试

使用 TypeScript 的一个主要优点是您想要测试的类的类型注释。害怕破坏测试不应该阻止你重构腐烂的代码。在测试中使用类型将有助于保持测试的整洁和可读性。

### 求和

TypeScript 可能是保持您的代码基础的一个很好的工具，并且更容易重构。您的服务将拥有您的控制器和组件将使用的可靠 API。bug 会在编译时被发现，而不是在 QA 或生产中。

即使没有 TypeScript，这些实践中的许多也可以应用于 ES6 和 ES5。在 ES5 中，你只需要使用 commonJS `require`语法来代替 ES6`import`systax——以及 JavaScript 原型模式来代替`class`关键字。

只是一些最后的想法:

> *   Build a system-swallow or purr.
> *   Use NPM-don't use the gazebo. All you need is a JavaScript package manager system. Bauer is redundant.