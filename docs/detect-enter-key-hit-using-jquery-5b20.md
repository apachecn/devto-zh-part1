# 使用 jQuery 检测回车键点击

> 原文：<https://dev.to/adamkdean/detect-enter-key-hit-using-jquery-5b20>

今天只是一个小片段，供以后参考。

```
$('.textbox').bind('onEnter', function(e) { 
    // your code here
});
$('.textbox').keyup(function(e) {
    if (e.keyCode == 13) 
        $(this).trigger('onEnter');
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是不言自明的。当按下回车键时，上面的函数触发。