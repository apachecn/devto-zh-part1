# 表单输入类型范围元素(滑块控件)

> 原文：<https://dev.to/mandarbadve/form-input-type-range-element-slider-control-1154>

HTML5 给了我们非常有用的滑块控件。让我们看看语法和如何使用滑块控件。

```
<input type="range" name="price" min="1" max="10" value="1" /> 
```

Enter fullscreen mode Exit fullscreen mode

以下是可用于范围输入的不同属性。

*   min:指定允许的最小值。
*   max:指定允许的最大值。
*   步骤:指定间隔值。
*   值:指定滑块的默认值。

您可以使用 event *onchange* 来跟踪选定的值。

```
<input type="range" max="10" min="1" name="quantity" value="1" onchange="alert('Selected value: '+ this.value);" /> 
```

Enter fullscreen mode Exit fullscreen mode

### 演示

选择产品的数量:

检查输入类型颜色的浏览器兼容性

[http://caniuse.com/#feat=input-range](http://caniuse.com/#feat=input-range)

*本帖最初发表于[blog.mandarbadve.com](http://blog.mandarbadve.com/2014/09/27/form-input-type-range-element-slider-control/)T3】*