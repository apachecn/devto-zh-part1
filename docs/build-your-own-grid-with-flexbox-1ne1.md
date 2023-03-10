# 使用 flexbox 构建您自己的网格

> 原文：<https://dev.to/hybrid_alex/build-your-own-grid-with-flexbox-1ne1>

[https://www.youtube.com/embed/PfKepdRfDCw](https://www.youtube.com/embed/PfKepdRfDCw)

在网站上布局内容有很多方法。大多数都围绕着某种类型的网格设置。在这个截屏中，我将介绍一种简单有效的方法，使用 flexbox 构建您自己的网格设置。

以下是入门所需的 CSS:

```
:root {
  --grid-gutter: 20px;
}

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.o-grid {
  display: flex;
  flex-wrap: wrap;
  margin-bottom: calc(var(--grid-gutter) * -1);
  margin-right: calc(var(--grid-gutter) * -1);
  margin-left: calc(var(--grid-gutter) * -1);
}

.o-grid__cell {
  width: 100%;
  margin-bottom: var(--grid-gutter);
  padding-right: var(--grid-gutter);
  padding-left: var(--grid-gutter);
}

@media screen and (min-width: 700px) {
  .u-md-width-6of12 {
    width: 50%;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

和标记:

```
<div class="o-grid">
  <div class="o-grid__cell u-md-width-6of12">
    <p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Nihil deleniti, nesciunt magnam iusto exercitationem fuga at ipsa laborum autem. Assumenda impedit vel nam illum molestiae architecto hic commodi corrupti rem.</p>
  </div>

  <div class="o-grid__cell u-md-width-6of12">
    <p>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Nihil deleniti, nesciunt magnam iusto exercitationem fuga at ipsa laborum autem. Assumenda impedit vel nam illum molestiae architecto hic commodi corrupti rem.</p>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

有用的链接:

*   [CSS-窍门-不要过度考虑 it 网格](https://css-tricks.com/dont-overthink-it-grids/)
*   [SUITCSS -组件网格](https://github.com/suitcss/components-grid)
*   [SUITCSS - Utils 大小](https://github.com/suitcss/utils-size/)