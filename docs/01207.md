# 空白:nowrap 和文本溢出:省略号的网格单元格问题

> 原文：<https://dev.to/timhecker/grid-cell-issue-with-white-space-nowrap--text-overflow-ellipsis-52g6>

**注意:**:我**解决了**，并在每个`.grid__item`上应用了`min-width: 0`，就像在 flexbox 世界里一样。

谁能帮我弄弄这个 css 网格的东西？

[https://codepen.io/timhecker/embed/YYNoMw?height=600&default-tab=result&embed-version=2](https://codepen.io/timhecker/embed/YYNoMw?height=600&default-tab=result&embed-version=2)

我有一个复杂的 HTML 结构:

```
<div class="grid">
  <div class="grid__item">
    <div>
        <a href="#">
            <div class="grid__detail">
              <h2>Lorem ipsum, dolor sit amet consectetur adipisicing elit.....very long title</h2>
            </div>
        </a>
    </div>
  </div>
  [...]
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我用的是 CSS 网格:

```
.grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    grid-gap: 2rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

我需要一个内心`div` :
的`box-shadow`

```
.grid__item div {
    box-shadow: 0 0 10px red;
} 
```

Enter fullscreen mode Exit fullscreen mode

对于一个很长的文本标题，我需要用`text-overflow`道具来截断它:

```
.grid__item h2 {
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
    max-width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

由于一些奇怪的原因，细胞长大了，直到包含整个文本，所以，我的网格崩溃了。我的网格单元解决了这个问题，但是我不能在内部的网格单元上使用阴影。

```
.grid__item {
    // overflow: hidden; // this fixes the problem, but say goodbye to the inner box-shadow
        min-width: 0; // this fixes the problem with truncate text
} 
```

Enter fullscreen mode Exit fullscreen mode

有什么想法吗？:(