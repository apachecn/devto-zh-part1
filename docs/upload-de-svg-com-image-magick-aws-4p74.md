# 上传 de SVG com 图像 Magick + AWS

> 原文：<https://dev.to/debborafernandess/upload-de-svg-com-image-magick-aws-4p74>

[![](img/5e651fda74349e4bba8e71aea21ec4ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dG1SYLIh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/500/1%2AiiBd1DD7NaAkjL9cOCgYaQ.png)

这又是其中一天*【我的机器】*，在那里文件上传在 DEV 环境和旧生产环境中工作正常，但在我们在 AWS 中认可的新实例中失败了。

已经想象到在生产中安装了 ImageMagick，但是由于上传图像不会发生:

```
convert -background none some\_image.svg some\_image.jpg 
convert: not authorized `some\_image.svg’ @ error/constitute.c/ReadImage/454.
convert: no images defined `some\_image.jpg’ @ error/convert.c/ConvertImageCommand/3046. 
```

> # What the hell

我想也许是诗句

```
$ convert -version

Version: ImageMagick 6.7.8–9 2016–06–22 Q16 http://www.imagemagick.org
Copyright: Copyright © 1999–2012 ImageMagick Studio LLC
Features: OpenMP 
```

> 好吧，我有更新的版本！
> 
> 至少到 2018 年 1 月

然后开始了蚂蚁的工作，以便更好地了解 ImageMagick 的工作原理。

ImageMagick 除其他帮助外，还具有 convert -list delegate 命令，该命令显示了针对每种类型的图像处理所执行的命令。SVG 转换使用 rsvg-convert 命令-“% o”% I。

由于该命令在服务器操作系统中无法识别，因此很容易看出有必要安装该***【lib】及其依赖项*** 。

变得更容易了，pq 依靠发布这一 gist[【install-GM-w-libvg . sh】](https://gist.github.com/tavinus/2265625d3e323737211e)的@tavinus 的帮助。😃

很酷吧？但在此之后，仍无法将文件从 *svg* 转换为 *png*

<figure>[![](img/bd4cd5973dd0e844531435d029892e08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gE6rafuA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/748/1%2APHzmIie9PDr1RrYI6KtlFA.jpeg) 

<figcaption>不是=/#给我！</figcaption>

</figure>

通过进一步搜索，我看到默认情况下，ImageMagick 策略设置中的某些. svg、. txt 等文件类型和某些字体(如 web 图像)已阻止上载，该策略通常位于/etc/ImageMagick/policy.xml 中，可以在 convert -list policy 命令的 rightsdo 中查看

于是解决办法是将这些权限更改为该类文件的行:

```
\<policy domain=”coder” rights=”none” pattern=”MVG” /\> 
```

最后，安装了 SVG 转换所需的 lib 并调整了该文件类型的规则后，我的上载终于成功了！