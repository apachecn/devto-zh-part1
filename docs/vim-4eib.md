# 在 vim 中突出显示光标所在的行和列

> 原文：<https://dev.to/shiena/vim-4eib>

在 vim 上搜索可能会丢失光标位置。
那个时候强调显示行和列的话会变得容易看。

```
" 行を強調表示
set cursorline

" 列を強調表示
set cursorcolumn 
```

[![カーソル位置の行と列を強調表示](img/dec23bd7d7467a763ec33423b5462312.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TN-AZ9OE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/85vh333xok3c9ie3ba6q.png)

实际使用后就知道了，常用的话强调显示反而会成为障碍，描绘也很慢，会产生压力，所以用 map 切换 ON/OFF 的话，使用起来会很方便。

```
nnoremap <Leader>c :<C-u>setlocal cursorline! cursorcolumn!<CR> 
```