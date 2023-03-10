# 使用 ng-repeat 排序和过滤对象

> 原文：<https://dev.to/adamkdean/ordering-and-filtering-objects-with-ng-repeat-1j62>

AngularJS 允许使用`ng-repeat`指令迭代集合。您可以对集合进行排序和过滤，但是这只适用于数组，不适用于对象。考虑到对象被当作数组对待，您可能会认为您保留了数组的功能，但是您没有。

这个问题的解决方案是使用一个`filter`将对象的内容推入一个数组。通过保持引用的完整性，我们仍然能够绑定到对象，因为它们本质上是同一个对象。

```
.filter('objectAsArray', function() {
    return function(object) {
        var array = [];
        for (item in object) {
            array.push(object[item]);
        }
        return array;
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如果我们想要排序和/或过滤一个数组，我们需要什么:

```
<p ng-repeat="item in itemArray | orderBy: 'order' | filter: {visible: true}">
  {{item}}
</p> 
```

Enter fullscreen mode Exit fullscreen mode

但是如果那是一个物体呢？嗯，我们只需将`objectAsArray`过滤器放入:

```
<p ng-repeat="item in itemObj | objectAsArray | orderBy: 'order' | filter: {visible: true}">
    {{item}}
</p> 
```

Enter fullscreen mode Exit fullscreen mode

这确实是一个非常有用的小过滤器。

点击查看[现场 plunkr 示例。](http://plnkr.co/edit/RObsrXoSSkRA271w9WJr?p=preview)