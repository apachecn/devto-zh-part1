# 带有 ES6 类的角度依赖注入注释

> 原文：<https://dev.to/avivby/angular-dependency-injection-annotations-with-es6-classes>

在之前的[文章](http://www.codelord.net/2017/05/08/moving-anuglar-factories-to-services-with-classes/)中介绍了 ES6 类如何用于定义 Angular 中的服务之后，有人问我这些如何与 Angular 的[依赖注入注释](http://www.codelord.net/2015/11/18/the-deal-with-angular-and-minification/)配合使用。

概括地说，使用依赖注入注释是为了让 Angular 知道它应该注入什么，即使代码被混淆/缩小。
这是 ES5 中注入的常规服务:

```
 angular.module('app').service('Service', function($http) {
      this.get = function() { ... };
    }); 
```

Enter fullscreen mode Exit fullscreen mode

带有显式注释的示例如下:

```
 angular.module('app').service('Service', ['$http', function($http) {
      this.get = function() { ... };
    }]); 
```

Enter fullscreen mode Exit fullscreen mode

这和 ES6 类有什么关系？

上面的例子是这样的:

```
 angular.module('app').service('Service', Service);

    class Service {
      constructor($http) {
        this.$http = $http;
      }
      get() { ... }
    } 
```

Enter fullscreen mode Exit fullscreen mode

并且为了给它添加适当的注释，只需在底部添加这一行:

```
 Service.$inject = ['$http']; 
```

Enter fullscreen mode Exit fullscreen mode

如果您坚持手动添加这些内容，这就是全部内容。

但是，请不要像动物一样做这件事，加入一些像 T2 一样的东西。
假设你使用的是 ES6，你很可能已经把代码移植到了 ES5 中，ng-annotate 可以简单地检查你的代码，并在必要时添加这些代码。

保持优雅！`</dadpun>`