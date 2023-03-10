# 轻松在 vim 中居中显示内容

> 原文：<https://dev.to/vinneycavallo/easily-center-content-in-vim>

## 寻找你的中心

[![vim centering gif](img/9db84d3060f779a3462889faf73852dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--O-hXM8JJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://vcavallo.github.img/vim-center-pane.gif)

这是最初发布在我的编程博客上的[。

我尽我所能使用 vim。当编辑代码时，我通常会在两个窗格中打开一个垂直分割的文件——这样整个屏幕上都有内容。但是当我写非代码文本时，我通常只打开一个文件，我喜欢保留 80 列的空白。这意味着我在屏幕的左侧有一个相对窄的书写栏，感觉和看起来都很奇怪。

我喜欢让它在我的全屏 vim 窗口上从左到右居中。这是我如何完成的。](http://vcavallo.github.io/blog/2017/02/28/vim-function-to-visually-center-a-plaintext-file/)

查看原始帖子以了解更多信息。对于不耐烦的人，这里有一个片段:

```
" centers the current pane as the middle 2 of 4 imaginary columns
" should be called in a window with a single pane

 function CenterPane()
   lefta vnew
   wincmd w
   exec 'vertical resize '. string(&columns * 0.75)
 endfunction

" optionally map it to a key:
" nnoremap <leader>c :call CenterPane()<cr> 
```

Enter fullscreen mode Exit fullscreen mode

让我知道，如果你发现它在某些情况下被打破或认为它可以改善。或者更好的是，如果你找到了更好的方法来做同样的事情！