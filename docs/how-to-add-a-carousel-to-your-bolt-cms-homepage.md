# 如何添加一个旋转木马到你的螺栓 CMS 主页

> 原文：<https://dev.to/martin_betz/how-to-add-a-carousel-to-your-bolt-cms-homepage>

在本教程中，我们将使用基于 jQuery 的 [Slick](http://kenwheeler.github.io/slick/) 库向我们的 [Bolt CMS](https://bolt.cm/) 的主页添加一个 carousel。我们将首先定义我们的目标是什么，设置基本系统，创建一个基本的 HTML 版本的转盘，最后将其纳入我们的螺栓 CMS 页面。

## 用户故事

轮播是网站上展示的图像集合。在这个场景中有两个角色:网站访问者和网站编辑。

1.  作为一个访问者，我想看一些关于我正在访问的网站的服务的图片。
2.  作为一个游客，我只想一次看一个图像。
3.  作为一名访问者，我希望能够用后退(`<<`)和前进(`>>`)按钮手动浏览图像。
4.  作为一个访问者，我希望网站的加载时间越少越好。
5.  作为一名编辑，我想在后端创建一个旋转木马。
6.  作为一名编辑，我想在轮播中添加/删除图像。
7.  作为一名编辑，我想改变图片在轮播中的顺序。

(注意:用户故事可能看起来有点精细，但是它们将帮助你保持正轨，并且可以检查许多待办事项并保持积极性)

## 设置

(注意:如果您熟悉设置开发环境和 Bolt CMS 本身，可以跳过这一节和下一节。请注意我在这里介绍的命名约定。)

你需要一个本地开发环境，使用命令行并运行 *PHP 5.6/7* 、 *Composer* 。

我使用 Mac 进行开发，我使用非常方便的开发工具 [Laravel Valet](https://laravel.com/docs/5.3/valet) 。请使用你和你的机器最适合的，无论是 *MAMP* 、*桌面服务器*还是*对接机*。如果你不在 Mac 上，也没有偏好，我强烈推荐 [Laravel Homestead](https://laravel.com/docs/5.3/homestead) 给你。

如果终端中的`php -v`显示出`PHP 7.1.0`或任何高于 5.6 的版本号，`composer`会向你显示一个命令列表。

## 安装螺栓

(注意:我总是为我的编辑添加行号。这些来自于普通的设置，所以如果你在文件中添加了额外的行，这些数字就不会正确。但即使在这些情况下，他们也会指引你到大概的位置。)

我们将使用 *Composer* 来安装 CMS 本身。`composer create-project bolt/composer-install:^3.2 carousel --prefer-dist`

`carousel`是应用程序的名称——您可以随意命名，只需记下这个名称，以后可能会用到。请耐心等待，安装程序会下载相当多的库(大约 100 MB)。安装人员会问你一些关于位置的问题，我会用`Enter`来确认。当然，你可以在这里根据自己的喜好修改细节。

Bolt 现在准备好进行设置了。你只需要告诉你的本地网络服务器服务于`public`文件夹。最简单的方法是在`public`文件夹中用`php -S localhost:8888`启动 PHP 的内部开发服务器。我更喜欢使用*代客*，它在引擎盖下使用 *Nginx* 。该命令在`public`文件夹中时为`valet link conference`。您现在可以在浏览器中打开`carousel.dev`并添加一个管理员用户。

表单下方有一条注释，通知您正在使用 *SQLite* 。SQLite 是“文件中的数据库”，这使得它比 MySQL 数据库更具移动性。对于编辑人数少(=编辑数据库的人)和访问者人数多(=从数据库和缓存中请求数据)的网站来说，它已经可以生产了。我们的会议应用程序可能会成为许多用户通过他们的条目访问和修改数据库的例子，所以对于一个生产网站，我可能会选择一个成熟的 MySQL 数据库。对于开发来说，SQLite 很好，如果你愿意，你可以稍后从 SQLite[迁移到 MySQL 或 MariaDB。](https://www.symfony.fi/entry/migrating-bolt-cms-from-sqlite-to-mysql-mariadb)

## 设置螺栓

你已经创建了你的管理员用户了吗？我给了我的用户名`carousel-24st3`(这个名字不那么容易得到，有利于防止暴力登录企图)。点击保存，你将被重定向到`carousel.dev/bolt`并看到后端。

## 创建内容类型

让我们首先从管理数据结构开始:

你的网站上可能有不止一个转盘。因此，为它创建一个新的 *ContentType* 是有意义的。轮播由两个字段组成:标题(主要用于参考)和 *Imagelist* 。一个*图像列表*是图像的集合。

因此，从`carousel/app/config/contenttypes.yml`打开您的配置文件并添加一个新的*内容类型* :

```
# pages-contenttype just for orientation
pages:
    …
    recordsperpage: 100

# carousel contenttype
carousels:
    name: Carousels
    singular_name: Carousel
    fields:
        title:
            type: text
          slug:
            type: slug
            uses: title
        gallery:
            type: imagelist
    record_template: carousel.twig 
```

Enter fullscreen mode Exit fullscreen mode

`fields`下的设置是什么意思？

1.  每个`field`都有名字，比如`title`和`gallery`。
2.  稍后，我们将通过模板中的这些名称来访问内容。
3.  字段的类型是从[这个列表](https://docs.bolt.cm/3.2/fields)中选择的，其中`type: text`、`type: textarea`和`type: html`是最常用的。最后一个相当于 Wordpress 中的内容区域，您可以在其中添加和格式化自由格式的文本。

我们需要一个 *slug* 来获取一个单独的传送带的 URL。而*画廊*只是图片列表的名称。最后，`record_template: carousel.twig`将使我们能够为所有转盘创建一个模板部件，然后我们可以将它包含在网站的任何部分。

我们前往[我们的后端](http://carousel.dev/bolt)并更新数据库。在左侧面板中，我们现在有一个新的部分，称为“旋转木马”。

注意:当你在 [carousel.dev](http://carousel.dev) 访问你的站点时，你会看到一条错误消息。我们没有创建页面，所以 Bolt 在这里没有任何东西可以显示。

## 打造我们的第一个旋转木马

从后端左侧面板的菜单中创建一个新的转盘。让我们把它命名为`My Carousel`并通过`Select from server`添加一些图片。我们可以在那里看到一些示例图像，幸运的是它们都有相同的大小。通过`Adding selected`添加四幅图像足以展示旋转木马。将*状态*设置为`Published`。

一旦我们添加了图像，我们可以简单地通过点击、按住和移动它们来改变它们的顺序。我们可以通过点击右边的`x`将它们从转盘中删除。

## 准备模板

我们将`carousel.twig`定义为转盘的模板文件。它还不存在，所以我们将从文件夹`public/theme/base-2016/`中复制`index.twig`。我们需要改变一些事情:

1.  删除`{{ record.teaser }}`、`{{ record.body }}`，因为我们没有这些字段
2.  循环播放我们拥有的图像

```
{% for images in record.gallery %}
    {{ image|showimage() }}
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

保存，我们现在可以查看预览。我们可以直接访问的预览网址是[carousel.dev/preview/carousel](http://carousel.dev/carousel/my-carousel)。我们看到四幅图像，每幅都在一条线上。

好吧，我们需要什么来得到一个移动的旋转木马？

1.  轮播 Javascript 和 CSS 资产
2.  启动轮播的 Javascript 脚本

### 加载 JS 和 CSS 资产

我们可以在基本模板(`public/theme/base-2016/partials/_master.twig`)中硬连接资产，但是让我们三思。

我们只需要包含转盘的页面中的转盘资产。将源文件添加到`_master.twig`会将它们加载到每个页面上。我们不想这样。我们能做些什么呢？

我们来开`_master.twig`。在第 19 和 20 行有一个标签`{% block headincludes %}{% endblock headincludes`。这是一个[小木块](#)。它使我们能够用每一个使用主模板的`% extends ‚partials/_master.twig‘ %}`来扩展主模板——就像我们的`carousel.twig`一样。通过这种方式，我们可以为这个页面包含自定义标题。

下面是`carousel.twig` :
的情况

```
{% block headincludes %}
    <link rel="stylesheet" type="text/css" href="//cdn.jsdelivr.net/jquery.slick/1.6.0/slick.css" />
    <!-- Add the slick-theme.css if you want default styling -->
    <link rel="stylesheet" type="text/css" href="//cdn.jsdelivr.net/jquery.slick/1.6.0/slick-theme.css" />
    <style>
.slick-prev:before, .slick-next:before { 
    color:red !important;
}
</style>
{% endblock headincludes %} 
```

Enter fullscreen mode Exit fullscreen mode

注意:我们必须一个接一个地定义块，所以确保将`headinclude`添加到`main`块之外。块的顺序并不重要，但是遵循逻辑顺序会更容易阅读代码。

这将从远程 CDN 服务器加载切片 CSS 文件，因此我们不必安装额外的软件。

Javascript 资产呢？最佳做法是将它们放在页面的末尾，就在关闭`</html>`之前。博尔特的`base-2016`模板对此也有一个块`bottomincludes`。这就是为我们加载资产的样子(同样在`carousel.twig` :

```
<script type=" text/javascript " src="//code.jquery.com/jquery-1.11.0.min.js "></script>
    <script type="text/javascript " src="//code.jquery.com/jquery-migrate-1.2.1.min.js "></script>
    <script type="text/javascript " src="//cdn.jsdelivr.net/jquery.slick/1.6.0/slick.min.js "></script> 
```

Enter fullscreen mode Exit fullscreen mode

保存`carousel.twig`并在 Chrome 中重新加载预览。然后呢。没什么。在窗口中右键单击并查看源代码。这里你可以看到 CSS 和 JS 确实被加载了。但是缺少了什么呢？

### 启动轮播的 Javascript 脚本

当然，我们需要启动 *slick* 并告诉它在 carousel 中包含哪些元素。我们必须将循环包装在一个 div 中，并给它一个惟一的 ID，这样我们就可以访问它:

```
 <div id="my-carousel">
        {% for image in record.gallery %}
            {{ image|showimage() }}
        {% endfor %}
    </div> 
```

Enter fullscreen mode Exit fullscreen mode

正如在 [slick 的网站](#)上所记录的，我们现在可以在`bottomincludes`块:
中启动(或定义)这样的传送带

```
 <script type="text/javascript ">
        $(document).ready(function(){
          $('#my-carousel').slick({
            slidesToShow: 1,            
            lazyLoad: 'ondemand',
            autoplay: true,
            accessibility: true
          });
        });
    </script> 
```

Enter fullscreen mode Exit fullscreen mode

我们用`#my-carousel`对 div 进行寻址，并定义了一些设置。我们得到了开箱即用的延迟加载，这基本上意味着不是所有的图片都将在页面加载时加载，而是在它们被查看时加载。这加速了初始加载，尤其是在移动连接上。

保存并再次加载[转盘](http://carousel.dev/carousel/my-carousel)。是啊，现在看起来像旋转木马。但是等等:在图像之间切换的按钮在哪里？

摆弄页面源代码会发现有后退和前进按钮——但它们是白色的。有一个简单的解决方法:只要把这个添加到你的`headincludes`块:

```
 <style>
        .slick-prev:before, .slick-next:before { 
            color:red !important;
        }
    </style> 
```

Enter fullscreen mode Exit fullscreen mode

保存、刷新并查看按钮。

我们现在有一个功能齐全的旋转木马。我们可以[参观并享受它](http://carousel.dev/carousel/my-carousel)

## 包括表头区域的轮播

现在，我们必须访问一个特殊的网址来查看旋转木马。然而，有时我们希望在每个页面上都包含轮播。例如:在博尔特的`base-2016`主题中，我们可能想把旋转木马放在我们现在看到城市全景的地方。

我们做什么呢

1.  将资产移动到全局`_master.twig`文件(我们现在在任何页面上都需要它们)(如上所述，将 CSS 放在标题中，将 JS 放在`</html>`之前
2.  在`base-2016/partials/_header.twig`中，定义轮播的内容并加载一个新的部分，因为通用的`record`变量总是被当前页面的值填充。然而，我们需要来自一个页面的值，所以我们使用`setcontent`来获取轮播的内容。

```
{% setcontent mix = '/carousel/my-carousel' %}
{{ include('partials/_carousel.twig') }} 
```

Enter fullscreen mode Exit fullscreen mode

1.  在`base-2016/partials`中创建新的分部`_carousel.twig`，并将循环粘贴到其中:

```
<div class="large-12 columns headerphoto" id="my-carousel">
    {% for image in mix.gallery %}
        {{ image|showimage() }}
    {% endfor %}
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

保存所有内容，在后台创建一些页面(我创建了一个`about`和`contact`页面，并用一些虚拟内容填充它们。

当我们加载[关于](#)和[联系](#)的时候，我们看到了旋转木马。我们没有微调尺寸，标题和正文可能会被传送带覆盖，但您可以检查页面源代码，看看它们是否真的在那里。

干得好！