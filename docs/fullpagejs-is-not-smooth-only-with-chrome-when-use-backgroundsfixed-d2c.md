# 在使用 backgroundsFixed 时，fullPage.js 不仅仅与 chrome 一起流畅。

> 原文：<https://dev.to/matsuikazuto/fullpagejs-is-not-smooth-only-with-chrome-when-use-backgroundsfixed-d2c>

[full page . js](https://github.com/alvarotrigo/fullPage.js)T2】/examples/backgrounds fixed . html

应用位置:绝对；到内部元素，使用 position:relative；因为包装类在滚动时不会平滑过渡。(仅在 chrome 上)

```
.section {
    // not smooth
    position: relative;

    // smooth
    position: static;
} 
```

Enter fullscreen mode Exit fullscreen mode