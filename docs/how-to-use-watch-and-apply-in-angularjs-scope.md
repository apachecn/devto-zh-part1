# 如何在 AngularJS $scope 中使用手表和应用

> 原文：<https://dev.to/luispa/how-to-use-watch-and-apply-in-angularjs-scope>

好吧，我用 Angular 1.x，为什么要学这个？

为了理解 AngularJS，你需要知道它是如何工作的。:)

## **消化周期和$范围**

首先，AngularJS 定义了一个所谓的**消化周期**的概念。这个循环可以被认为是一个循环，在此期间 AngularJS 检查所有`$scopes`观察到的所有变量**是否有任何变化。因此，如果您在控制器中定义了`$scope.myVar`，并且这个变量被**标记为被监视**，那么您就是在隐式地告诉 AngularJS 在循环的每次迭代中监视`myVar`上的变化。**

## **$手表有助于监听$范围的变化**

有两种方法可以声明一个`$scope`变量被监视。

1.  通过表达式`<span>{{myVar}}</span>`在你的模板中使用它
2.  通过`$watch`服务手动添加

Ad 1)这是最常见的情况，我相信您以前也见过，但您不知道这在后台创建了一个手表。是的，它有！使用 AngularJS 指令(比如`ng-repeat`)也可以创建隐式手表。

Ad 2)这就是你如何打造自己的**手表**的方法。当附加到`$scope`的一些值已经改变时，服务帮助你运行一些代码。它很少使用，但有时很有帮助。例如，如果你想在每次‘myVar’改变时运行一些代码，你可以这样做:

```
function MyController($scope) {

    $scope.myVar = 1;

    $scope.$watch('myVar', function() {
        alert('hey, myVar has changed!');
    });

    $scope.buttonClicked = function() {
        $scope.myVar = 2; // This will trigger $watch expression to kick in
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

## $apply 允许将更改与摘要周期集成

您可以将`$apply` **功能视为一种集成机制**。你看，每次你改变一些**被观察变量直接附加到** `$scope`对象上，AngularJS 就会知道变化已经发生。这是因为 AngularJS 已经知道如何监控这些变化。因此，如果它发生在由框架管理的代码中，那么摘要循环将继续下去。

然而，有时你想要**改变 AngularJS 世界**之外的一些值，并看到这些改变正常传播。考虑一下——您有一个将在 jQuery 的`$.ajax()`处理程序中修改的`$scope.myVar`值。这将在未来的某个时候发生。AngularJS 不能等待这种情况发生，因为它还没有被指示等待 jQuery。

为了解决这个问题，引入了`$apply`。它让你明确地开始消化循环。但是，您应该只使用这种方法将一些数据迁移到 AngularJS(与其他框架集成)，而不要将这种方法与常规的 AngularJS 代码结合使用，因为 AngularJS 将会抛出一个错误。

## 所有这些与 DOM 有什么关系？

好吧，既然你知道了所有这些，你真的应该再跟着教程走一遍。只要没有任何变化，摘要循环将通过评估连接到 all `$scopes`的每个观察器来确保 UI 和 JavaScript 代码保持同步。如果在摘要循环中没有发生更多的变化，那么它就被认为是结束了。

您可以在控制器中显式地将对象附加到`$scope`对象，或者通过在视图中以`{{expression}}`的形式直接声明它们。

我希望这有助于澄清一些关于这一切的基本知识。

进一步阅读:[制作你自己的 AngularJS，第 1 部分:范围和摘要](http://teropa.info/blog/2013/11/03/make-your-own-angular-part-1-scopes-and-digest.html)