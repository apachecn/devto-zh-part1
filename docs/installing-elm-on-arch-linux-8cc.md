# 在 arch linux 上安装 elm

> 原文：<https://dev.to/sigu/installing-elm-on-arch-linux-8cc>

按照[官方指南](https://guide.elm-lang.org/install.html)中的指示，通过`npm`将 elm 安装为全局

```
$ npm i -g elm 
```

此时，如果您尝试调用`elm-repl`，您可能会得到如下所示的错误

```
○ → elm-repl
/home/sigu/.nvm/versions/node/v8.6.0/lib/node_modules/elm/Elm-Platform/0.18.0/.cabal-sandbox/bin/elm-repl: error while loading shared libraries: libtinfo.so.5: cannot open shared object file: No such file or directory 
```

要在 arch Linux 中解决这个问题，您需要将`libncurses`库链接到所需的库，如下所示。

```
$ cd /usr/lib
$ sudo ln -s libncurses++w.so.6.0 libtinfo.so.5 
```

现在做`elm-repl`会像预期的那样工作