# 检测 ng-options 何时完成渲染

> 原文：<https://dev.to/adamkdean/detect-when-ng-options-has-finished-rendering-3i8i>

如果你想在`ng-options`完成渲染选项时触发一个事件，你可以用`scope.$watch`观察模型，然后用`scope.$evalAsync`将事件代码排队以在下一个摘要周期触发。像这样:

```
scope.$watch(model, function() {
    scope.$evalAsync(function() {
        // event
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您有一个封装了 select 和 ng-options 的指令，例如，您想在 ng-options 执行后触发一些 jQuery 代码来将 select 转换为多选框，那么您可以这样做:

```
.directive('ngMultiselect', function () {
    return {
        replace: true,
        restrict: 'E',
        scope: false,
        template: function (element, attrs) {
            console.log('template');
            return '<div><select class="multiselect" name="' + attrs.name + 
                '" ng-model="' + attrs.ngModel + '" ng-options="' + attrs.optexp + 
                '"' + ((attrs.required) ? ' required' : '') + '></select></div>';
        },
        link: function(scope, element, attrs) {
            scope.$watch(attrs.ngModel, function() {
                scope.$evalAsync(function() {
                    $('.multiselect').multiselect();
                });
            });
        }
    }   
}); 
```

Enter fullscreen mode Exit fullscreen mode

请记住，对模型的每个更改都会触发事件，所以您可能需要在其中进行某种检查，但就渲染后触发而言，效果非常好。

一种确保你只开一次枪的方法是这样的:

```
link: function(scope, element, attrs) {
    scope.$watch(attrs.ngModel, function() {
        if (!angular.isDefined(scope.multiselectInitialized)) {
            scope.multiselectInitialized = true;
            scope.$evalAsync(function() {
                $('.multiselect').multiselect();
            });
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

**更新:**以下是该指令的 HTML 示例。我也有一堆 CSS，但我不认为这是相关的。

```
<p>availableOptions: `{{availableOptions}}`</p>
<p>selectedOptions: `{{selectedOptions}}`</p>

<ng-multiselect 
    multiple="true"
    ng-model="selectedOptions"
    options="o.label for o in availableOptions"
    change="selected()">
</ng-multiselect> 
```

Enter fullscreen mode Exit fullscreen mode