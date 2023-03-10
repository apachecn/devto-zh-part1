# 用 unite-haddock 很好地预览谷歌

> 原文：<https://dev.to/shiena/unite-haddock-hoogle-1man>

# 前言

使用 vim 和[unite-haddock](https://github.com/eagletmt/unite-haddock) 插件，可以在 hoogle 上搜索 API 并在浏览器中打开文档。
这样很方便，但是因为从 vim 切换到浏览器，所以感觉有点不好用。
于是文档也制作了在 vim 上预览的功能，顺利地获取了。

# Operation is smooth.

## 打开 hoogle 源代码

在命令行模式下执行以下命令:
默认为部分一致检索。

```
:Unite hoogle 
```

想完全一致检索时，在参数中添加`exact`。

```
:Unite hoogle:exact 
```

[![cmdline.png](img/0c00bd19e773c7fc70c127ae049b782c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KLkDY5nn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/ptkdspmm1xtwwrf5x637.png)

## 检索 API

在部分一致检索中尝试检索`google`。

[![unite.png](img/75b1202f7ef8b890287640bd0dc257f3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Leo_EZL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/zwwmetnoehsptwwsekr1.png)

## 预览文档

将光标移动到要打开文档的 API 上，按`p`在预览窗口中显示。
关闭预览窗口时执行`Ctrl-W Ctrl-Z`或`:pclose`。

[![preview.png](img/d71b91288d9254841ab33783e0e22ae8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U3OFYkIa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/pksjerrs5wikfvpy05fl.png)

## 自动显示预览

指定 unite.vim 的`-auto-preview`选项后，只需将光标移动到搜索结果一览中就可以显示预览。

```
:Unite -auto-preview hoogle 
```