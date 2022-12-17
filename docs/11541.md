# 使用 jQuery 查看元素是否存在

> 原文：<https://dev.to/adamkdean/use-jquery-to-see-if-an-element-exists-25ip>

您可以使用 jQuery 通过检查 jQuery 对象的长度是否大于零来查看元素是否存在。

```
var exists = $('.your-selector-here').length > 0; 
```

Enter fullscreen mode Exit fullscreen mode

可能是目前为止最短的片段。