# 都检查过了吗？

> 原文：<https://dev.to/mahmoudelmahdi/are-they-all-checked-83m>

检查所有复选框输入字段是否都被选中的最简单方法；通过使用 ES6 `...` spread 操作符将节点列表转换为数组，这样我们就可以将`every()`方法附加到它上面；

他们都检查过了吗？[js fiddle 上的 Demo](https://jsfiddle.net/elmahdim/bp8gu9j8/1/)

```
[...document.querySelectorAll('input')].every(checkbox => checkbox.checked) 
```

Enter fullscreen mode Exit fullscreen mode