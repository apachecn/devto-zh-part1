# 已尝试引入 vim 的 statusline 装饰插件 lightline.vim

> 原文：<https://dev.to/shiena/vim-statusline-lightline-vim-3e9l>

vim 的 statusline 装饰插件是

*   [vim-电力线](https://github.com/Lokaltog/vim-powerline)
*   [电力线](https://github.com/Lokaltog/powerline)
*   [vim-航空公司](https://github.com/bling/vim-airline)

如上所述有几个。 在这种情况下，最近又出现了新的插件。

*   [做了 lightline.vim 关于插件的正交性](http://d.hatena.ne.jp/itchyny/20130824/1377351527)

作为特征列举了以下事项。

*   只使用 vim script 编写，重量很轻
*   可以轻松扩展
*   注意不要干扰其他插件

因为 powerline 似乎引进起来很麻烦，所以就直接放弃了 vim-airline，但是这样的话好像没有麻烦，所以也喜欢[landscape theme](https://github.com/itchyny/lightline.vim#landscape-theme-with-the-patched-font) ，所以试着引进了。

虽然只设定了颜色主题，但是默认情况下会显示各种各样的内容，所以即使没有使用同类插件的人也值得导入。

```
let g:lightline = {
    \ 'colorscheme': 'landscape'
    \ } 
```

[![lightline.vim](img/c0cbeb769d034acc04b3a0975b10cde4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h2AL23DQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/mwxlsr2bzugj5tgqgoj4.png)