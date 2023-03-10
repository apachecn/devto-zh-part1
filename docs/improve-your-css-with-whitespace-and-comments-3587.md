# 用空白和注释改进你的 CSS

> 原文：<https://dev.to/hybrid_alex/improve-your-css-with-whitespace-and-comments-3587>

如果我可以分享 CSS 开发人员可以做的两件事来开始改进他们的代码，那就是创建更多有意义的空白和在必要时添加注释。空白是记录 CSS 文件不同部分的简单而有效的方法。看看下面的两个例子，让我知道哪个更容易消化。

```
.o-table {
  width: 100%;
}
.o-table--fixed {
  table-layout: fixed;
}
.o-table--tiny {
  th, td {
    padding: $inuit-global-spacing-unit-tiny;
  }
}
.o-table--small {
  th, td {
    padding: $inuit-global-spacing-unit-small;
  }
}
.o-table--large {
  th, td {
    padding: $inuit-global-spacing-unit-large;
  }
}
.o-table--huge {
  th, td {
    padding: $inuit-global-spacing-unit-huge;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个有据可查的 CSS 文件

```
/* ==========================================================================
   #TABLE
   ========================================================================== */

/**
 * A simple object for manipulating the structure of HTML `table`s.
 */

.o-table {
  width: 100%;
}

/* Equal-width table cells
   ========================================================================== */

/**
 * `table-layout: fixed` forces all cells within a table to occupy the same
 * width as each other. This also has performance benefits: because the browser
 * does not need to (re)calculate cell dimensions based on content it discovers,
 * the table can be rendered very quickly. Further reading:
 * https://developer.mozilla.org/en-US/docs/Web/CSS/table-layout#Values
 */

.o-table--fixed {
  table-layout: fixed;
}

/* Size variants
   ========================================================================== */

.o-table--tiny {

  th,
  td {
    padding: $inuit-global-spacing-unit-tiny;
  }

}

.o-table--small {

  th,
  td {
    padding: $inuit-global-spacing-unit-small;
  }

}

.o-table--large {

  th,
  td {
    padding: $inuit-global-spacing-unit-large;
  }

}

.o-table--huge {

  th,
  td {
    padding: $inuit-global-spacing-unit-huge;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码示例来自于 [inuitcss 项目](https://github.com/inuitcss/inuitcss)。

* * *

这篇文章最初发表在我的博客[这里](https://alexcarpenter.me/articles/improve-your-css-with-whitespace-and-comments)。