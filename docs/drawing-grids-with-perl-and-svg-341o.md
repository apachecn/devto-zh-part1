# 用 Perl 和 SVG 绘制网格

> 原文：<https://dev.to/jacoby/drawing-grids-with-perl-and-svg-341o>

我的朋友 Brian 在[上写博客，用 Python 和 Pillow 制作网格，这样他就可以进行艺术创作了](https://randomgeekery.org/2017/11/24/drawing-grids-with-python-and-pillow/)。

我觉得“酷”。然后我想“但是我不喜欢 Python！”，并决定用 Perl 重新实现它。

我还决定使用 SVG，因为这符合我的用例。我更有可能想要扔给激光切割机，所以更自然地出现在 Inkscape 中的东西对我来说是好的。

我也写过输出 SVG 的 Spirograph。我以后可能会重访。这要简单得多，不需要正弦或余弦。

总之…

```
#!/usr/bin/env perl

use strict ;
use warnings ;
use utf8 ;
use feature qw{ postderef say signatures state } ;
no warnings qw{ experimental::postderef experimental::signatures } ;

use Carp ;
use Getopt::Long ;
use Pod::Usage ; # when I fix my man/help screens
use SVG ;

my $config = config() ;

...

sub config () {
    my $config = {
        dot => 0,
        height => 600,
        width => 600,
        step => 10,
        file => 'graph.svg',
        } ;
    GetOptions(
        'man' => \$config->{ man },
        'height=i' => \$config->{ height },
        'width=i' => \$config->{ width },
        'step=i' => \$config->{ step },
        'dot' => \$config->{ dot },
        'file=s' => \$config->{ file },
        ) ;

    exit if $config->{ height } < 100 ;
    exit if $config->{ width } < 100 ;
    exit if $config->{ step } < 1 ;
    exit if $config->{ man } ;
    return $config ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

Brian 使用了`sys.argv`，但是我早就认为参数顺序不可避免地会混乱，使用选项和 [Getopt::Long](https://metacpan.org/pod/Getopt::Long) 。Perl 的座右铭是*有不止一种方法去做*，CPAN 的`Getopt`部分有很多方法去做，但是有些比其他的更好，除非我只是啧啧有声地加入 ARGV，否则我已经标准化了 Getopt::Long。

`GetOptions()`中的`field=i`也确保这些是整数字段。

我还会指出 [Pod::Usage](https://metacpan.org/pod/Pod::Usage) 在里面，但是没有被使用。我确实经常使用它，这将是它告诉我们`--height 20`和`--step -10`不在的地方和方式。但是，事实上，它只是存在。

我这样做，部分是因为我知道`--help`和`--height`将争夺`-h`。我将不得不考虑旗帜。

```
my $svg = SVG->new(
    height => $config->{ height },
    width => $config->{ width },
    ) ;

...

my $output = $svg->xmlify ;
if ( open my $fh, '>', $config->{ file } ) {
    print $fh $output ;
    close $fh ;
    say 'Done' ;
    }
else {
    say 'fail' ;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果我们就此打住，我们就创建一个空的 SVG 文件。

```
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.0//EN" "http://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd">

    <!-- 
    Generated using the Perl SVG Module V2.64
    by Ronan Oger
    Info: http://www.roitsystems.com/
 -->
 
```

Enter fullscreen mode Exit fullscreen mode

我早就说过“我讨厌 XML 我爱 RSS”，下面是这个。我不必经常钻研 SVG 文件的内部结构，所以只要它保持一致，我就很满意。

回头看看 GetOptions，您会注意到一个用于`--dots`的布尔标志。从现场笔记中，我成为了一个使用点而不是线来创建图表的图表纸的粉丝。所以，在这里，我增加了这个选项。它还允许我展示在 SVG 中画圆和线。

```
if ( $config->{ dot } ) {
    my $height = $config->{ height } ;
    my $width = $config->{ width } ;
    my $step = $config->{ step } ;

    for ( my $h = 0 ; $h <= $height ; $h = $h + $step ) {
        for ( my $v = 0 ; $v <= $width ; $v = $v + $step ) {
            $svg->circle(
                cx => $v,
                cy => $h,
                r => 0.5,
                style => {
                    stroke => 'black',
                    'stroke-width' => 0.2,
                    fill => 'black',
                    }
                    ) ;
            }
        }

    say 'DOT' ;
    }
else {
    say 'LINE' ;
    my $height = $config->{ height } ;
    my $width = $config->{ width } ;
    my $step = $config->{ step } ;

    # horizontal
    for ( my $h = 0 ; $h <= $height ; $h = $h + $step ) {
        $svg->line(
            x1 => 0,
            y1 => $h,
            x2 => $width,
            y2 => $h,
            style => {
                stroke => 'black',
                'stroke-width' => 0.2,
                fill => 'none',
                }
                ) ;
        }

    # vertical
    for ( my $v = 0 ; $v <= $width ; $v = $v + $step ) {
        $svg->line(
            x1 => $v,
            y1 => $0,
            x2 => $v,
            y2 => $height,
            style => {
                stroke => 'black',
                'stroke-width' => 0.2,
                fill => 'none',
                }
                ) ;
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我复制了`$config`中的变量，以避免循环中的冗长。我对此不是 100%满意。我可以肯定地认为这违反了**干**。

我认为,`$svg->line()`和`$svg->circle()`的语法远比循环重要。我将添加描边和填充值作为练习留给读者。

[![The SVG showing dots](img/a54caeeab2d08394ce14374c5c43e9f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---u-sugCJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qprp7b71wy40k7sxy8zf.png)

[![The SVG showing lines](img/1ba59002480627da574997d0cff14806.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wj3zK6Ls--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bdf34ryk77vjrbzjn4nw.png)

完整代码可从 GitHub Gist 获得。

其他版本，如六边形图或使用 HTML5 动态创建的图，留给读者作为练习。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。