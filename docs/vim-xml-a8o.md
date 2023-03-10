# 用 vim 轻松地整形 XML

> 原文：<https://dev.to/shiena/vim-xml-a8o>

这是用 vim 轻松地对因开发等而没有换行的 XML 进行整形的命令。

```
:%s/></>\r</g | filetype indent on | setf xml | normal gg=G 
```

或

```
:%s/></>^M</g | filetype indent on | setf xml | normal gg=G 
```

`^M`不是`^`和`M`两个字，而是在`Ctrl-V`之后输入`Enter`的一个字。

如果不需要缩进，或者 XML 很大，需要时间，只进行以下置换的话会很快。

```
:%s/></>\r</g 
```

```
:%s/></>^M</g 
```

## Plastic example

```
<foo><bar><qiita>example</qiita></bar></foo> 
```

这样的文本是

```
<foo>
    <bar>
        <qiita>example</qiita>
    </bar>
</foo> 
```

就这样被整形。