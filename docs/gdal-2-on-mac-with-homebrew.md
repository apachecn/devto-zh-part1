# 带有自制软件的 Mac 上的 GDAL 2

> 原文：<https://dev.to/bhaskar_vk/gdal-2-on-mac-with-homebrew>

*最初发表于我的[个人博客](https://www.karambelkar.info/2016/10/gdal-2-on-mac-with-homebrew/)。*

这是一篇关于在 Mac 上使用家酿升级到 [gdal 2.x](https://trac.osgeo.org/gdal/wiki/Release/2.1.3-News) 的小帖子。

## 假设

*   你已经安装了自制软件。
*   您已经通过 homebrew 的默认“gdal”公式安装了 gdal 1.x。

## 安装 gdal 2.x

*   使用
    `brew unlink gdal`
    解除 gdal 1.x 的链接

*   轻点进入 [osgeo4mac](https://github.com/OSGeo/homebrew-osgeo4mac)
    `brew tap osgeo/osgeo4mac && brew tap --repair`

*   安装 gdal2。

```
brew install gdal2 --with-armadillo \
  --with-complete --with-libkml --with-unsupported 
```

Enter fullscreen mode Exit fullscreen mode

*   链接 gdal 2
    T0】

*   验证

```
 $> gdal-config --version
2.1.1

$> gdal-config --libs
-L/usr/local/Cellar/gdal2/2.1.1/lib -lgdal

$> gdal-config --cflags
-I/usr/local/Cellar/gdal2/2.1.1/include 
```

Enter fullscreen mode Exit fullscreen mode

## 安装后

如果你用 R 来处理地理空间的东西，一定要重新安装`rgdal`库。现在你也可以使用`devtools::install_github('edzer/sfr')`在 R 中安装 [sf](https://github.com/edzer/sfr) 。