# 使用 CSS 隐藏数字输入微调器

> 原文：<https://dev.to/designer023/hide-number-input-spinners-using-css-2i95>

HTML 5 数字选择器非常棒，因为它们有助于将用户限制在特定情况下所需的输入范围内。一些浏览器添加了小的上下箭头来帮助用户浏览数字。

在一些浏览器上，即使表单输入没有被聚焦，选择器也会默认显示，这有点难看。您可以使用 CSS 来关闭数字输入箭头。

显然你想把它们放回焦点上，因为默认情况下它们应该在那里。

```
// Webkit:
// Hide number picker 
input[type=number]::-webkit-inner-spin-button, 
input[type=number]::-webkit-outer-spin-button { 
    -webkit-appearance: none; 
    margin: 0; 
} 

// Show number picker on focus 
input[type=number]:focus::-webkit-inner-spin-button, 
input[type=number]:focus::-webkit-outer-spin-button, 
input[type=number]:hover::-webkit-inner-spin-button, 
input[type=number]:hover::-webkit-outer-spin-button { 
    -webkit-appearance: inner-spin-button; 
    margin: 0 2px 0 0 ; 
} 

// Firefox: 
// Hide number picker 
input[type=number] { 
    -moz-appearance:textfield; 
} 

// Show number picker on focus 
input[type=number]:focus, 
input[type=number]:hover { 
    -moz-appearance:number-input; 
} 
```

Enter fullscreen mode Exit fullscreen mode