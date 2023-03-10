# PoC: CSS 网格布局编辑器和 Web GUI 编辑器？

> 原文：<https://dev.to/mizchi/poc-css-grid-layout-editor-and-web-gui-editor-3lk>

## 概念

我认为今天的 web 应用程序开发需要 GUI 编辑器本身，并具有有效的语义。

不是`Home Page Builder`。

## 实现

为了进行这项研究，我首先编写了 css 网格布局生成器。

试试这里。[https://mizchi-sandbox.github.io/grid-generator/](https://mizchi-sandbox.github.io/grid-generator/)

[![](img/8716332082dc83ac357d17ce4bc0f42b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DN54vI7O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://gyazo.com/97e5ffe65f34c2ec5d11e79d9d2756b5.gif)

[![](img/74629dc42c52cdef3fa65da9b3b1436f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7FMwd0iI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.gyazo.com/b0a5edd1966a18c31e71666178eb02b2.png)

是的，它非常粗糙，但很有效。

这个工具生成的 css 是这样的。

```
.gridContainer {
  width: 100%;
  height: 100%;
  display: grid;
  grid-template-columns: 120px 4fr 1fr;
  grid-template-rows: 60px 1fr 40px;
  grid-template-areas: 'header header header' 'left content right' 'footer footer footer';
}
.headerArea {
  grid-area: header;
}
.leftArea {
  grid-area: left;
}
.contentArea {
  grid-area: content;
}
.rightArea {
  grid-area: right;
}
.footerArea {
  grid-area: footer;
} 
```

Enter fullscreen mode Exit fullscreen mode

代码在这里。[https://github.com/mizchi-sandbox/grid-generator](https://github.com/mizchi-sandbox/grid-generator)