# 如何在 bootstrap 中使用 Glyphicons？

> 原文：<https://dev.to/moreonfew/how-to-use-glyphicons-in-bootstrap-207m>

像许多其他有用的组件一样，Bootstrap 也提供了许多易于使用的 Glyphicon“图标”。简单来说，这些包含了一系列有用的字体图标，你可以很容易地在你的网页上使用。这些字体图标允许你在你的网页上使用超过 250 个字形或图标，使用简单的 CSS 类的字体格式。

## bootstrap 中的 Glyphicons 是什么？

简单来说，Glyphicons 就是图标字体。所以基本上，字形是嵌入字体中的图标。所以字体不是显示字母，而是显示图标。还记得 Windows 上的 Windows 和 Webdings 字体吗？用那些用来给图标而不是字母。象形文字也非常相似。然而，要使用一个特定的图标，你必须使用 CSS 类来代替。

## 使用它们的好处不多

雕刻图标真的很有帮助，建议尽可能使用，但是你应该根据你的要求来接电话。首先，这里有一些使用引导图形图标的好处。

1.  颜色和大小可以随时改变，不像图像图标的情况。
2.  背景颜色，不透明度等也可以很容易地改变。
3.  重量轻，因为只需加载一个字体文件，而不是许多图标图像。
4.  不像素化，可以跨设备和分辨率使用。
5.  可以使用 CSS3 轻松制作动画。
6.  可以很容易地改变，因为它只需要改变一个类。如果使用 LESS 或 SASS/SCSS，会更容易。
7.  仅在一个字体文件中就有超过 250 个图标。这意味着你可以使用超过 250 个图标乘以任何数量的变化，你需要使用 CSS。那是大的！

## Bootstrap 中的象形文字入门——简单教程

正如我提到的，在 Bootstrap 中使用 Glyphicons 非常简单。就像添加一个 CSS 类一样简单！但是，考虑到您是第一次使用它，我将带您完成这些步骤。

**以下是使用 Bootstrap Glyphicons 的步骤:**如果你的网页上已经有 Bootstrap，请跳到步骤 2。

**第一步。**

首先在你的网页上包含自举 CSS。如果您已经下载了引导文件，您可以从本地路径链接 CSS。或者，您也可以使用引导 CDN。所以现在你的代码可能看起来像下面这样:

```
<!-- Latest CSS from local path -->
<link rel="stylesheet" src="my/path/bootstrap.css">

<!-- OR -->

<!-- Latest compiled and minified CSS from CDN -->
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css"
integrity="sha384-1q8mTJOASx8j1Au+a5WDVnPi2lkFfwwEAa8hDDdjZlpLegxhjVME1fgjWPGmkzs7" crossorigin="anonymous"> 
```

Enter fullscreen mode Exit fullscreen mode

或者

**你可以使用 Nodejs npm 命令** :
安装 Bootstrap

```
$  npm  install  bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

第二步:

现在你的网页上已经有了引导程序，你可以开始使用图形图标了。出于性能方面的考虑，Bootstrap 将所需的属性分成了两个类， **glyphicon** 和**glyphicon-*icon name***。下面是一个关于象形文字用法的例子:

```
<span class="glyphicon glyphicon-search"> </span> 
```

Enter fullscreen mode Exit fullscreen mode

对，就是这么简单！如果你注意到，如果你需要改变图标或者使用不同的图标，唯一会改变的类是第二个类。因此，如果我想使用删除图标，我需要做的不是搜索图标，而是用 **glyphicon-remove** 替换 **glyphicon-search** 。所以我的类看起来会像`glyphicon glyphicon-remove`。你可以在 Bootstrap 的[官方网站找到支持图标的完整列表。](http://getbootstrap.com/components/#glyphicons-glyphs)

### 要记住的几件事:

既然你已经看到使用象形文字真的很简单，那么让我补充一点，在使用象形文字时，有几件事你应该考虑:

*   总是在其中没有任何文本或子元素的空元素上使用 Glyphicon 类。
*   不要将这些类与其他组件混合。简单地说，不要将 glyphicon 类与其他组件结合使用。例如，如果在一个按钮中需要一个图标，不要在同一个元素中组合按钮相关类和 glyphicon 类。相反，在按钮中使用嵌套的 or，并将 glyphicon 类分配给它。
*   使用 LESS 或 SASS 发行版时，Bootstrap 假设 glyphicon 字体文件位于与编译的 CSS 文件相关的目录`../fonts/`下。所以如果你改变了它，你需要更新无源文件中的`@icon-font-path`和/或`@icon-font-name`变量(通常在 variables.less 文件中)。

### Bootstrap glyphicon 不工作错误

有时你会注意到，即使在确保你使用的类、字体路径等都是正确的之后，你的引导图形图标可能也不会出现。在这种情况下，可能是以下情况之一:

*   请确保 Glyphicon 字体可以直接访问，并且不会引发 404 错误。这可能是“引导图形图标不显示”错误的原因之一。
*   如果您确定文件存在于正确的文件夹中，但仍然抛出 404 错误，那么请检查字体的 mime 类型是否正确。你可以在 Chrome 的开发工具->网络标签下查看 mimetype。找到字体文件的请求并点击它。您应该能够看到标题信息的详细信息。在这些文件中，您可以找到服务器字体的 mimetype。理想情况下应该是`application/x-font-woff`。您必须为 glyphicon 字体设置正确的 mime 类型。这可以在服务器级别完成。
*   有时字体文件损坏，并抛出错误或显示框而不是图标，在这种情况下，请从 Bootstrap 的网站下载字体文件的新副本，并替换您现有的字体文件。在大多数错误情况下，这一步通常非常有用。

总而言之，我想说使用引导雕刻非常简单。我相信如果你打算在你的项目中使用 Bootstrap，这是一个更好的选择。然而，如果你只是需要图标而不是引导框架，那么你也可以考虑使用 [Font Awesome](https://moreonfew.com/how-to-use-font-awesome-icons/) ，它也是一个基于字体的图标。如果你在想什么在“字体大战”中得分更高，那么我只能说这取决于你的使用和需求。你也可以使用由[官方雕刻图标网站](http://glyphicons.com/)提供的雕刻图标，用法保持不变。

如何在引导程序中使用象形文字？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。