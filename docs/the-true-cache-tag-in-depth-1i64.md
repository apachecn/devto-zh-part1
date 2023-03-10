# 深入制作{% cache %}标记

> 原文：<https://dev.to/gaijinity/the-true-cache-tag-in-depth-1i64>

# 飞船`{% cache %}`标记深入

### Craft CMS 有一个`{% cache %}`标签，如果使用有效的话，可以帮助性能。它是这样工作的。

安德鲁·韦尔奇

[![Craft Cms Cache Tag](img/b795fe37492b19d3474f71d7ba125756.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PPr5gpvg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/craft-cms-cache-tag.jpg)

**更新:**本文已更新，涵盖了 Craft CMS 2.x 和 Craft CMS 3.x

如果你理解并正确应用，Craft CMS 中可用的标签可以帮助你提高性能。然而，正如 [Craft CMS 模板文档](https://craftcms.com/docs/templating/cache)页面上所显示的:

<aside>If you’re suf­fer­ing from abnor­mal page load times, you may be expe­ri­enc­ing a sub­op­ti­mal host­ing envi­ron­ment. Please con­sult a spe­cial­ist before try­ing `{% cache %}`. `{% cache %}` is not a sub­sti­tute for fast data­base con­nec­tions, effi­cient tem­plates, or mod­er­ate query counts. Pos­si­ble side effects include stale con­tent, exces­sive­ly long-run­ning back­ground tasks, stuck tasks, and in rare cas­es, death. Ask your host­ing provider if `{% cache %}` is right for you.</aside>

换句话说，``{% cache %}``标签不是你应该用来掩盖性能问题的东西，不管这些问题是来自一个麦克风或配置不良的服务器，还是写得不够好的模板。你不能简单地编写高效的模板，把它们放在共享主机上，然后期望用``{% cache %}``标签来“解决”性能问题。我们称之为给猪涂口红。

然而，一旦你明白它是什么，以及如何使用它，它会是一个非常有用的工具。

## 我们为什么要缓存？

缓存背后的基本思想是，我们希望将频繁访问的数据保存在一个可以快速检索的格式中。我们在日常生活中也这样做。当你早上醒来，打开医疗网络，你需要准备的一切都在那里等着你。

我想象着，如果你的厕所散落在你的房子里，每天早上你都得去找它们，那么早上你要花多少时间去准备…

通过*将我们需要的东西都存放在一个地方，我们可以提前*安排好早晨的时间，这样我们就可以高效地完成工作。

这正是我们在电脑上缓存数据的确切原因。我们可以缓存它，而不是每次加载网页时都从海量数据集中为拉姆计算一些疯狂的复杂程序。我们在非关键时刻缓存结果，并在用户加载页面时将缓存的结果呈现给用户。

每个人都很开心。

## 标签到底是做什么的？

现在我们明白了为什么要缓存数据，让我们来看看 Craft CMS 中的``{% cache %}``标签到底是如何工作的。下面是一段比较标准的小枝代码:

```
 {% for entry in craft.entries.section('blog').relatedTo(someCategory) %}
    <article>
        <h1>{{ entry.title }}</h1>
        <p>{{ entry.summary }}</p>
        <img src="{{ entry.image.first().getUrl(myTransform) }}">
    </article>
{% endfor %} 
```

这段代码生成了许多数据库查询，以获取与`someCategory`相关的所有`blog`条目，然后循环并显示它们。``{{ entry.image.first().getUrl(myTransform) }}``实际上每次都通过循环生成几个数据库查询，因为资产是需要查找的关系，它必须查找资产转换。这被称为 N+1 查询问题，它基本上意味着我们拥有的条目越多，事情就会变得越慢。

每次有人加载我们的网页时，我们真的需要做所有这些数据库查询吗？没有。我们一周最多只发布一次新的博客条目；没有理由在每次加载页面时都要进行所有这些数据库查询。

因此，我们可以将它包装在一个``{% cache %}``标签中:

```
 {% cache %}
    {% for entry in craft.entries.section('blog').relatedTo(someCategory) %}
        <article>
            <h1>{{ entry.title }}</h1>
            <p>{{ entry.summary }}</p>
            <img src="{{ entry.image.first().getUrl(myTransform) }}">
        </article>
    {% endfor %}
{% endcache %} 
```

``{% cache %}``标签所做的就是捕获解析后的输出，并将结果作为文本存储在数据库中。因此，每次循环时，不是对我们的条目进行一系列查询，也不是对我们的`entry.image.first().getUrl(myTransform)`进行多次查询，而是对我们的缓存结果只进行一次查询。

这就是为什么我们要在复杂的小枝代码块或产生大量数据库查询的小枝代码块周围使用``{% cache %}``标签。如果您只是将普通的 HTML 文本包装在``{% cache %}``标签中，您将看不到任何收益。它甚至可能会稍微慢一点，因为我们在以前没有的地方添加了一个数据库查询。

*Tan gent:* 敏锐的读者会注意到，上面的例子对于[急切加载](https://craftcms.com/docs/templating/eager-loading-elements)来说是一个完美的例子，但这超出了本文的范围。

如果您启用了`devMode`,您可以查看您的 JavaScript 控制台来查看在一个页面上执行的数据库查询的数量。这是你现在正在阅读的博客页面的样子，没有``{% cache %}``标签:

[![Caching Off](img/ef2ae5c027d15722b72ca4119668ae44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--POOGZSvi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_768x226_crop_center-center_100_line/caching_off.png)

下面是同样的博客页面被恰当地使用了``{% cache %}``标签后的样子:

[![Caching On](img/7bc442e309e59233e6c62d40be968736.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3wAA0cLj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_768x226_crop_center-center_100_line/caching_on.png)

我们从`102`数据库查询减少到`43`数据库查询，这些查询花费的时间从`1.84s`减少到仅仅`0.49s`！对于很少的工作来说，这是一个巨大的收获！随着我们添加更多的博客条目，没有``{% cache %}``标签的页面的查询数量将会持续上升，而缓存页面的查询数量将保持不变。我们减少了许多数据库查询，从而提高了性能。

这里关键的一点是,``{% cache %}``标签所做的是存储它内部的 Twig 代码的解析输出。如果在一个``{% cache %}``标签中有像``{{ now|date("M d, Y") }}``这样的分支代码，那么保存在缓存中的不是分支代码，而是解析结果(在本例中是一个日期)。因此，只要数据被缓存，网页上显示的日期就不会改变。

所以如果我们的代码是:

```
 {% cache %}
    {{ now|date("M d, Y") }}
{% endcache %} 
```

数据库中实际保存的是:

```
 Nov 30, 2016 
```

*Tan gent:*`cacheDuration`[Gen er al Con fig Set ting](https://craftcms.com/docs/config-settings)决定了一个模板缓存将保留多长时间。这个设置默认为`P1D` (1 天或 24 小时)，这意味着你的缓存将每 24 小时更新一次。虽然这是一个敏感的缺省设置，但它有点保守，给出了 Craft 的自动 mat ic 缓存中断。我在我的`craft/config/general.php`文件中把它设置为`cacheDuration => false`,这意味着模板缓存*永远不会自己过期，它们只在``{% cache %}``标签中的内容改变时才重新生成。有关如何使用多环境配置设置的讨论，请查看[Craft CMS 的多环境配置](https://nystudio107.com/blog/multi-environment-config-for-craft-cms)文章。*

## 大爆发！

这引出了一个有趣的铜鼓。如果有人更改了我们``{% cache %}``标签中的一个博客条目，会发生什么？Craft 在这方面相当聪明:它跟踪任何*元素*(条目、资产等)。)在您的``{% cache %}``标签中，如果它们发生了变化，就会将缓存标记为陈旧。所以它会自动为你打破缓存，导致它重新生成和重新缓存数据。

这很好，但我们还必须做好自己的工作，明智地选择缓存内容和缓存方式。例如，一个典型的博客页面会有博客条目本身，以及一个“博客档案”部分，列出我们所有的旧博客条目。

现在，我们可以用``{% cache %}``标签把整个模板包起来，然后就到此为止。然而，想想当一个新的博客发布时会发生什么:它将使每个单独的博客页面缓存失效，因为我们的档案随着一个新的博客帖子的增加而改变！这看起来有点傻，因为我们其他人的博客条目一点都没变。唯一改变的是我们的博客存档部分，列出了新的博客条目。

如果从抽象的角度来考虑，在这种情况下，我们实际上有两个实体需要缓存:

1.  博客条目本身
2.  博客档案

因为它们实际上是相互独立的。我们可能会添加一篇新的博客文章，这将导致博客档案需要改变，但这不会影响我们的每个博客条目。

此外，每个单独的博客入口页面都将共享完全相同的博客存档部分。所以，我们可以这样做:

## Craft 2.x 举例

这是一个专门涵盖工艺 2.x 的例子；虽然 Craft 3.x 的大部分代码都是相似的，但是确定默认缓存键的方式是不同的。

```
 {# -- Our blog entry -- #}
{% cache globally using key craft.request.path %}
    <article>
        <h1>{{ entry.title }}</h1>
        <p>{{ entry.body }}</p>
        <img src="{{ entry.image.first().getUrl(myTransform) }}">
    </article>
{% endcache %}

{# -- Our blog archives -- #}
{% cache globally using key "blog-archives" %}
    <section>
        {% for blogArchive in craft.entries.section('blog') %}
            <h1>{{ blogArchive.title }}</h1>
            <p>{{ blogArchive.summary }}</p>
        {% endfor %}
    </section>
{% endcache %} 
```

这里我们在一个页面上有两个单独的高速缓存。第一个是我们实际的博客条目；第二个是从我们的博客档案中缓存出来的全球 T2，在数据库中存储为 T0。这意味着每个博客条目都有一个独特的缓存，但是我们的博客档案只有一个全球共享的缓存。

这很好，因为我们不希望每次添加新的博客条目时，每个博客页面都需要重新缓存。它只是重新缓存我们所有博客页面共享的博客存档缓存。

您还会注意到，我们并没有单独使用``{% cache %}``标签，而是实际上使用了``{% cache globally using key craft.request.path %}``。我们这样做是因为``{% cache %}``标签的默认行为是使用*完整路径*作为唯一标识我们缓存的一种方式(除了创建自动材质生成的唯一散列之外，因此我们每页可以有多个``{% cache %}``标签)。这个完整路径*包括查询字符串*，它是？在 URI，例如:`?utm_source=GHDJ14J`。

但是我们真的不希望查询字符串导致一个新的、唯一的缓存条目，否则我们可能会在数据库表中有数百甚至数千个条目。例如，如果我们只是单独使用``{% cache %}``标签，那么对`/blog`和`/blog?utm_source=F1GMAT`的请求指向同一个页面，但是会导致额外的`craft_templatecaches`条目。

如果您最终在`craft_templatecaches`数据库表中有大量条目，它实际上可能会妨碍而不是帮助缓存——这一开始就违背了缓存的全部意义！正是因为这个原因，Pix el & Ton ic 实际上改变了 Craft 3 中``{% cache %}``标签的默认行为，使其不包含查询字符串。

如果你正在使用[回线](https://github.com/nystudio107/retour)插入，并且你正在你的`_layout.twig`中实现``{% cache %}``标签以扩展其他模板，你可能会使用这样的模式:

```
 {% cache globally using key craft.request.path unless craft.retour.getHttpStatus != 200 %} 
```

这将导致它忽略缓存关键字的查询字符串，并且永远不会缓存任何不是**200****OK**[http sta 状态代码](https://httpstatuses.com/)的东西。如[Craft CMS](https://nystudio107.com/blog/handling-errors-gracefully-in-craft-cms)文章中所述，这是正确处理错误所需要的。

请记住，如果使用``{% cache globally using key craft.request.path %}``，每页只能有一个``{% cache %}``标签，因为唯一的*键*将是`craft.request.path`。因此，如果你需要在一个页面上有不止一个``{% cache %}``标签，给这个键添加一个描述性的名字，使它唯一，比如``{% cache globally using key "header-block" ~ craft.request.path %}``。

想想你的页面上有什么，以及如何有效地缓存它。

## Craft 3.x 举例

这是 covers Craft 3 . x specially 的一个示例；虽然 Craft 2.x 的大部分代码都是相似的，但是确定默认缓存键的方式是不同的。

```
 {# -- Our blog entry -- #}
{% cache %}
    <article>
        <h1>{{ entry.title }}</h1>
        <p>{{ entry.body }}</p>
        <img src="{{ entry.image.first().getUrl(myTransform) }}">
    </article>
{% endcache %}

{# -- Our blog archives -- #}
{% cache globally using key "blog-archives" %}
    <section>
        {% for blogArchive in craft.entries.section('blog') %}
            <h1>{{ blogArchive.title }}</h1>
            <p>{{ blogArchive.summary }}</p>
        {% endfor %}
    </section>
{% endcache %} 
```

这里我们在一个页面上有两个单独的高速缓存。第一个是我们实际的博客条目；第二个是从我们的博客档案中缓存出来的全球 T2，在数据库中存储为 T0。这意味着每个博客条目都有一个独特的缓存，但是我们的博客档案只有一个全球共享的缓存。

这很好，因为我们不希望每次添加新的博客条目时，每个博客页面都需要重新缓存。它只是重新缓存我们所有博客页面共享的博客存档缓存。

你还会注意到，与 Craft 2.x 示例不同，我们只是单独使用了``{% cache %}``标签。Pix el & Ton ic 改变了 Craft 3 中``{% cache %}``标签的默认行为，使其不包含查询字符串，这样我们就不用自己动手了。

如果你在工艺 3 中使用[回线](http://github.com/nystudio107/craft-retour)插入，并且你在你的`_layout.twig`中实现``{% cache %}``标签以扩展其他模板，你可能会使用这样的模式来控制:

```
 {% cache unless craft.retour.getHttpStatus != 200 %} 
```

这将导致它永远不会缓存任何不是**200****OK**[http status code](https://httpstatuses.com/)的东西。如[Craft CMS](https://nystudio107.com/blog/handling-errors-gracefully-in-craft-cms)文章中所述，这是正确处理错误所需要的。

想想你的页面上有什么，以及如何有效地缓存它。

## 缓存实例

一种常见的情况是，您希望缓存整个网页，但也有不希望缓存的部分。让我们称它们为*缓存豁免*。

例如，如上所述，您可能希望通过在主`layout.twig`文件中放置一个``{% cache %}``标签来缓存所有页面。但是我们不想缓存带有非`200` [http 状态码](https://httpstatuses.com/)的页面，也许我们不想缓存搜索结果页面等某些页面。

你可以使用的一个很好的模式是这样的:

**CRAFT 2.x 举例:**

```
 {# ##### Craft 2.x Cache exemption ##### #}
{% set cacheExempt = false %}
{# Exempt certain pages #}
{% set cacheExemptSegments = [
    'dont-cache-me-bro',
] %}
{% if craft.request.getSegment(1) in cacheExemptSegments %}
    {% set cacheExempt = true %}
{% endif %}
{# Also exempt pages with non-200 OK status codes #}
{% if craft.retour.getHttpStatus != 200 %}
    {% set cacheExempt = true %}
{% endif %}
{# Add any cache exemption conditions #}
...

{% cache globally using key craft.request.path unless cacheExempt %} 
```

**CRAFT 3.x 举例:**

```
 {# ##### Craft 3.x Cache exemption ##### #}
{% set cacheExempt = false %}
{# Exempt certain pages #}
{% set cacheExemptSegments = [
    'dont-cache-me-bro',
] %}
{% if craft.app.request.getSegment(1) in cacheExemptSegments %}
    {% set cacheExempt = true %}
{% endif %}
{# Also exempt pages with non-200 OK status codes #}
{% if craft.retour.getHttpStatus != 200 %}
    {% set cacheExempt = true %}
{% endif %}
{# Add any cache exemption conditions #}
...

{% cache unless cacheExempt %} 
```

使用这项技术，您可以向`cacheExemptSegments`数组中添加任意多的页面，任何匹配第一个段的请求都不会被缓存。你可以把这段代码放在一个 emp ty 数组中，让它不要在第一段中免除任何页面，以备将来需要时使用。

然后，您可以添加您可能有的任何其他条件，这使得该模式可扩展，并且它还保持您的实际标签相当干净。

## 引擎盖下的窥视

因此，让我们认真地看一看`craft_templatecaches`表中的条目实际上是什么样子的。下面是`craft_templatecaches`表的表模式:

```
 MariaDB [nystudio]> describe craft_templatecaches;
+------------+--------------+------+-----+---------+----------------+
| Field | Type | Null | Key | Default | Extra |
+------------+--------------+------+-----+---------+----------------+
| id | int(11) | NO | PRI | NULL | auto_increment |
| cacheKey | varchar(255) | NO | | NULL | |
| locale | char(12) | NO | MUL | NULL | |
| path | varchar(255) | YES | | NULL | |
| expiryDate | datetime | NO | | NULL | |
| body | mediumtext | NO | | NULL | |
+------------+--------------+------+-----+---------+----------------+
6 rows in set (0.00 sec) 
```

高速缓存条目是基于每个`locale`发送的，并且具有唯一标识它们的`cacheKey`和`path`。如果我们的页面只使用 reg u lar old ``{% cache %}``标签，表格中的数据库条目可能看起来像这样:

```
 MariaDB [nystudio]> select cacheKey,path from craft_templatecaches;
+--------------------------------------+--------------------------------------------------+
| cacheKey | path |
+--------------------------------------+--------------------------------------------------+
| blog-archives | NULL |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog/creating-optimized-images-in-craft-cms |
+--------------------------------------+--------------------------------------------------+ 
```

如您所见，Craft 为每个条目存储了一个`cacheKey`和一个`path`。这两个信息描述了`craft_templatecaches`表中的唯一条目。`cacheKey`是我们为我们的全球`blog-archives`所要求的，并且是我们刚刚使用 reg u lar old ``{% cache %}``标签的地方的一个随机的 dom hash。

如果我们改为使用``{% cache globally using key craft.request.path %}`` pat tern，我们的`craft_templatecaches`最终看起来像这样:

```
 MariaDB [nystudio]> select cacheKey,path from craft_templatecaches;
+---------------------------------------------+----------------+
| cacheKey | path |
+---------------------------------------------+----------------+
| blog-archives | NULL |
| blog | NULL |
| blog/creating-optimized-images-in-craft-cms | NULL |
+---------------------------------------------+----------------+ 
```

如您所见，它使用了`craft.request.path`作为密钥，而`path`中没有存储任何内容。如果我们不通过``{% cache globally using key craft.request.path %}``在 Craft 2.x 上去掉查询字符串(这在 Craft 3.x 上不会是一个问题)，我们最终会得到一个`craft_templatecaches`表，如下所示:

```
 MariaDB [nystudio]> select cacheKey,path from craft_templatecaches;
+--------------------------------------+--------------------------------------------------+
| cacheKey | path |
+--------------------------------------+--------------------------------------------------+
| blog-archives | NULL |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=F1GMAT |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=ADG12F |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=GS13FA |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=SM66MS |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=CMBKA4 |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=HGHAJ2 |
| yYNGYDNbbsxeRCDCWhBqbmpHbN2NBELomimv | site:blog?utm_source=24GSJ2 |
+--------------------------------------+--------------------------------------------------+ 
```

等等等等。我们最终会在`craft_templatecaches`表中找到大量实际上是同一件事的条目，这会导致性能下降，以及令人担忧的挂起*删除过时模板缓存*任务。

同样的情况也发生在我们的`_layout.twig`文件中，我们的 404 模板扩展了那个`_layout.twig`:如果我们不排除 404 Not Found http sta tus 代码，我们将为每个点击我们站点的单个 404 找到一个`craft_templatecaches`条目。每一个。单身。一个。

相信我，互联网上有大量的机器人每天都在探测您的网站，寻找漏洞，这些漏洞会产生大量的 404 错误，并可能对您的网站造成严重的影响。

所以明智地缓存。

## 但是等等，还有！

我们甚至可以去兔子洞。除了`templatecaches`表，Craft 还跟踪每个缓存条目的一些附加信息。

[![But Wait Theres More](img/9e9e2d2b3bc6d741febb5b9316bb9bc3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--r85tgRJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/but-wait-theres-more.jpg)

为了帮助打破自动 mat ic 缓存，Craft 还有一个名为`templatecacheelements`的表来跟踪任何元素(条目、类别、资产等)。)在一个给定的``{% cache %}``标签中:

```
 MariaDB [craft3]> select * from templatecacheelements;
+---------+-----------+
| cacheId | elementId |
+---------+-----------+
| 1 | 4 |
| 2 | 85 |
| 2 | 80 |
| 2 | 81 |
| 2 | 73 |
| 2 | 74 |
| 2 | 4 |
| 2 | 83 |
+---------+-----------+
8 rows in set (0.00 sec) 
```

注意，每个`templatecaches` id 都有一行。这样，如果一个``{% cache %}``标签的中的*元素被改变，Craft 可以自动破解缓存。但是请注意，这也意味着如果我们在``{% cache %}``标签中有大量的元素，我们最终会在这个表中得到大量的行:* 

```
 MariaDB [imawhale]> select * from craft_templatecacheelements;
+---------+-----------+
| cacheId | elementId |
+---------+-----------+
| 12160 | 2 |
| 12160 | 369 |
| 12160 | 370 |
| 12160 | 55 |
| 12160 | 57 |
| 12160 | 58 |
| 12160 | 56 |
| 12160 | 59 |
| 12160 | 297 |
| 12160 | 233 |
...
24647 rows in set (0.03 sec) 
```

因此，如果我们有一个网站，上面有 10，000 个博客条目，每个博客在``{% cache %}``标签中有 10 个元素，这意味着我们将在`templatecaches`表中得到 10，000 行，在`templatecacheelements`表中得到 100，000 行。仅仅为了博客。

类似地，Craft 使用另一个表来跟踪 Craft 2.x 的元素查询(`templatecachecriteria`)和 Craft 3.x 的元素查询(`templatecachequeries`)

**CRAFT 2.x ELE MENT 标准:**T2】

```
 MariaDB [imawhale]> select * from craft_templatecachecriteria;
+--------+---------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | cacheId | type | criteria |
+--------+---------+------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 113997 | 12160 | Entry | {"ancestorDist":null,"ancestorOf":null,"archived":0,"dateCreated":null,"dateUpdated":null,"descendantDist":null,"descendantOf":null,"fixedOrder":0,"id":null,"indexBy":null,"level":null,"limit":100,"locale":"en","localeEnabled":1,"nextSiblingOf":null,"offset":0,"order":"lft, postDate desc","positionedAfter":null,"positionedBefore":null,"prevSiblingOf":null,"relatedTo":null,"ref":null,"search":null,"siblingOf":null,"slug":"twitch-nav","status":"live","title":null,"uri":null,"with":null,"childField":null,"childOf":null,"depth":null,"parentField":null,"parentOf":null,"after":null,"authorGroup":null,"authorGroupId":null,"authorId":null,"before":null,"editable":0,"expiryDate":null,"postDate":null,"section":"navigation","sectionId":null,"type":null} |
| 113998 | 12160 | MatrixBlock | {"ancestorDist":null,"ancestorOf":null,"archived":0,"dateCreated":null,"dateUpdated":null,"descendantDist":null,"descendantOf":null,"fixedOrder":0,"id":null,"indexBy":null,"level":null,"limit":100,"locale":"en","localeEnabled":1,"nextSiblingOf":null,"offset":0,"order":"matrixblocks.sortOrder","positionedAfter":null,"positionedBefore":null,"prevSiblingOf":null,"relatedTo":null,"ref":null,"search":null,"siblingOf":null,"slug":null,"status":"enabled","title":null,"uri":null,"with":null,"childField":null,"childOf":null,"depth":null,"parentField":null,"parentOf":null,"fieldId":"28","ownerId":"369","ownerLocale":null,"type":null} |
...
22745 rows in set (0.04 sec) 
```

**ELE MENT 查询:**T2

```
 MariaDB [craft3]> select * from templatecachequeries;
+----+---------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | cacheId | type | query |
+----+---------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 1 | 2 | craft\elements\Entry | TzoyODoiY3JhZnRcZWxlbWVudHNcZGJcRW50cnlRdWVyeSI6NzA6e3M6ODoiZWRpdGFibGUiO2I6MDtzOjk6InNlY3Rpb25JZCI7YToxOntpOjA7czoxOiIzIjt9czo2OiJ0eXBlSWQiO047czo4OiJhdXRob3JJZCI7TjtzOjEzOiJhdXRob3JHcm91cElkIjtOO3M6ODoicG9zdERhdGUiO047czo2OiJiZWZvcmUiO047czo1OiJhZnRlciI7TjtzOjEwOiJleHBpcnlEYXRlIjtOO3M6MTc6IgAqAGRlZmF1bHRPcmRlckJ5IjthOjE6e3M6MTY6ImVudHJpZXMucG9zdERhdGUiO2k6Mzt9czoxMToiZWxlbWVudFR5cGUiO3M6MjA6ImNyYWZ0XGVsZW1lbnRzXEVudHJ5IjtzOjU6InF1ZXJ5IjtOO3M6ODoic3ViUXVlcnkiO047czoxMjoiY29udGVudFRhYmxlIjtzOjEyOiJ7eyVjb250ZW50fX0iO3M6MTI6ImN1c3RvbUZpZWxkcyI7TjtzOjk6ImluUmV2ZXJzZSI7YjowO3M6NzoiYXNBcnJheSI7YjowO3M6MjoiaWQiO047czozOiJ1aWQiO047czoxMDoiZml4ZWRPcmRlciI7YjowO3M6Njoic3RhdHVzIjthOjE6e2k6MDtzOjQ6ImxpdmUiO31zOjg6ImFyY2hpdmVkIjtiOjA7czoxMToiZGF0ZUNyZWF0ZWQiO047czoxMToiZGF0ZVVwZGF0ZWQiO047czo2OiJzaXRlSWQiO3M6MToiMSI7czoxNDoiZW5hYmxlZEZvclNpdGUiO2I6MTtzOjY6ImxlYXZlcyI7YjowO3M6OToicmVsYXRlZFRvIjtOO3M6NToidGl0bGUiO047czo0OiJzbHVnIjtOO3M6MzoidXJpIjtOO3M6Njoic2VhcmNoIjtOO3M6MzoicmVmIjtOO3M6NDoid2l0aCI7TjtzOjc6Im9yZGVyQnkiO2E6Mjp7czoyMToic3RydWN0dXJlZWxlbWVudHMubGZ0IjtpOjQ7czoxNjoiZW50cmllcy5wb3N0RGF0ZSI7aTozO31zOjEzOiJ3aXRoU3RydWN0dXJlIjtiOjE7czoxMToic3RydWN0dXJlSWQiO2I6MDtzOjU6ImxldmVsIjtOO3M6MTQ6Imhhc0Rlc2NlbmRhbnRzIjtOO3M6MTA6ImFuY2VzdG9yT2YiO047czoxMjoiYW5jZXN0b3JEaXN0IjtOO3M6MTI6ImRlc2NlbmRhbnRPZiI7TjtzOjE0OiJkZXNjZW5kYW50RGlzdCI7TjtzOjk6InNpYmxpbmdPZiI7TjtzOjEzOiJwcmV2U2libGluZ09mIjtOO3M6MTM6Im5leHRTaWJsaW5nT2YiO047czoxNjoicG9zaXRpb25lZEJlZm9yZSI7TjtzOjE1OiJwb3NpdGlvbmVkQWZ0ZXIiO047czozOToiAGNyYWZ0XGVsZW1lbnRzXGRiXEVsZW1lbnRRdWVyeQBfcmVzdWx0IjtOO3M6NDc6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3Jlc3VsdENyaXRlcmlhIjtOO3M6NDU6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3NlYXJjaFNjb3JlcyI7TjtzOjY6InNlbGVjdCI7YToxOntpOjA7czoyOiIqKiI7fXM6MTI6InNlbGVjdE9wdGlvbiI7TjtzOjg6ImRpc3RpbmN0IjtOO3M6NDoiZnJvbSI7TjtzOjc6Imdyb3VwQnkiO047czo0OiJqb2luIjtOO3M6NjoiaGF2aW5nIjtOO3M6NToidW5pb24iO047czo2OiJwYXJhbXMiO2E6MDp7fXM6MTg6InF1ZXJ5Q2FjaGVEdXJhdGlvbiI7TjtzOjIwOiJxdWVyeUNhY2hlRGVwZW5kZW5jeSI7TjtzOjI3OiIAeWlpXGJhc2VcQ29tcG9uZW50AF9ldmVudHMiO2E6MDp7fXM6MzU6IgB5aWlcYmFzZVxDb21wb25lbnQAX2V2ZW50V2lsZGNhcmRzIjthOjA6e31zOjMwOiIAeWlpXGJhc2VcQ29tcG9uZW50AF9iZWhhdmlvcnMiO2E6MTp7czoxMjoiY3VzdG9tRmllbGRzIjtPOjM2OiJjcmFmdFxiZWhhdmlvcnNcRWxlbWVudFF1ZXJ5QmVoYXZpb3IiOjE2OntzOjc6ImJsYWhTZW8iO047czoxMjoiaW1hZ2VDYXB0aW9uIjtOO3M6MTU6Im9wdGltaXplZEltYWdlcyI7TjtzOjc6InNlb0NyYXAiO047czo5OiJzb21lQXNzZXQiO047czoxMDoic29tZU1hdHJpeCI7TjtzOjEwOiJzb21lTnVtYmVyIjtOO3M6MTM6InNvbWVQbGFpblRleHQiO047czoxMDoic29tZVJlY2lwZSI7TjtzOjEyOiJzb21lUmljaFRleHQiO047czo4OiJzb21lVW5pdCI7TjtzOjU6InN0dWZmIjtOO3M6MTA6InRlc3RJTWFnZXMiO047czo0OiJ3b29mIjtOO3M6NTE6IgBjcmFmdFxiZWhhdmlvcnNcQ29udGVudEJlaGF2aW9yAF9jdXN0b21GaWVsZFZhbHVlcyI7YTowOnt9czo1OiJvd25lciI7cjoxO319czo1OiJ3aGVyZSI7TjtzOjU6ImxpbWl0IjtpOjIwO3M6Njoib2Zmc2V0IjtOO3M6NzoiaW5kZXhCeSI7TjtzOjE2OiJlbXVsYXRlRXhlY3V0aW9uIjtiOjA7fQ== |
| 2 | 2 | craft\elements\Asset | TzoyODoiY3JhZnRcZWxlbWVudHNcZGJcQXNzZXRRdWVyeSI6NzE6e3M6ODoidm9sdW1lSWQiO047czo4OiJmb2xkZXJJZCI7TjtzOjg6ImZpbGVuYW1lIjtOO3M6NDoia2luZCI7TjtzOjU6IndpZHRoIjtOO3M6NjoiaGVpZ2h0IjtOO3M6NDoic2l6ZSI7TjtzOjEyOiJkYXRlTW9kaWZpZWQiO047czoxNzoiaW5jbHVkZVN1YmZvbGRlcnMiO2I6MDtzOjE0OiJ3aXRoVHJhbnNmb3JtcyI7TjtzOjExOiJlbGVtZW50VHlwZSI7czoyMDoiY3JhZnRcZWxlbWVudHNcQXNzZXQiO3M6NToicXVlcnkiO047czo4OiJzdWJRdWVyeSI7TjtzOjEyOiJjb250ZW50VGFibGUiO3M6MTI6Int7JWNvbnRlbnR9fSI7czoxMjoiY3VzdG9tRmllbGRzIjtOO3M6OToiaW5SZXZlcnNlIjtiOjA7czo3OiJhc0FycmF5IjtiOjA7czoyOiJpZCI7TjtzOjM6InVpZCI7TjtzOjEwOiJmaXhlZE9yZGVyIjtiOjA7czo2OiJzdGF0dXMiO2E6MTp7aTowO3M6NzoiZW5hYmxlZCI7fXM6ODoiYXJjaGl2ZWQiO2I6MDtzOjExOiJkYXRlQ3JlYXRlZCI7TjtzOjExOiJkYXRlVXBkYXRlZCI7TjtzOjY6InNpdGVJZCI7aToxO3M6MTQ6ImVuYWJsZWRGb3JTaXRlIjtiOjE7czo2OiJsZWF2ZXMiO2I6MDtzOjk6InJlbGF0ZWRUbyI7TjtzOjU6InRpdGxlIjtOO3M6NDoic2x1ZyI7TjtzOjM6InVyaSI7TjtzOjY6InNlYXJjaCI7TjtzOjM6InJlZiI7TjtzOjQ6IndpdGgiO047czo3OiJvcmRlckJ5IjthOjE6e3M6MTk6InJlbGF0aW9ucy5zb3J0T3JkZXIiO2k6NDt9czoxMzoid2l0aFN0cnVjdHVyZSI7TjtzOjExOiJzdHJ1Y3R1cmVJZCI7TjtzOjU6ImxldmVsIjtOO3M6MTQ6Imhhc0Rlc2NlbmRhbnRzIjtOO3M6MTA6ImFuY2VzdG9yT2YiO047czoxMjoiYW5jZXN0b3JEaXN0IjtOO3M6MTI6ImRlc2NlbmRhbnRPZiI7TjtzOjE0OiJkZXNjZW5kYW50RGlzdCI7TjtzOjk6InNpYmxpbmdPZiI7TjtzOjEzOiJwcmV2U2libGluZ09mIjtOO3M6MTM6Im5leHRTaWJsaW5nT2YiO047czoxNjoicG9zaXRpb25lZEJlZm9yZSI7TjtzOjE1OiJwb3NpdGlvbmVkQWZ0ZXIiO047czoxNzoiACoAZGVmYXVsdE9yZGVyQnkiO2E6MTp7czoyMDoiZWxlbWVudHMuZGF0ZUNyZWF0ZWQiO2k6Mzt9czozOToiAGNyYWZ0XGVsZW1lbnRzXGRiXEVsZW1lbnRRdWVyeQBfcmVzdWx0IjtOO3M6NDc6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3Jlc3VsdENyaXRlcmlhIjtOO3M6NDU6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3NlYXJjaFNjb3JlcyI7TjtzOjY6InNlbGVjdCI7YToxOntpOjA7czoyOiIqKiI7fXM6MTI6InNlbGVjdE9wdGlvbiI7TjtzOjg6ImRpc3RpbmN0IjtOO3M6NDoiZnJvbSI7TjtzOjc6Imdyb3VwQnkiO047czo0OiJqb2luIjthOjE6e2k6MDthOjM6e2k6MDtzOjEwOiJJTk5FUiBKT0lOIjtpOjE7czoyNDoie3slcmVsYXRpb25zfX0gcmVsYXRpb25zIjtpOjI7YTo0OntpOjA7czozOiJhbmQiO2k6MTtzOjQwOiJbW3JlbGF0aW9ucy50YXJnZXRJZF1dID0gW1tlbGVtZW50cy5pZF1dIjtpOjI7YToyOntzOjE4OiJyZWxhdGlvbnMuc291cmNlSWQiO3M6MToiNCI7czoxNzoicmVsYXRpb25zLmZpZWxkSWQiO3M6MToiMiI7fWk6MzthOjM6e2k6MDtzOjI6Im9yIjtpOjE7YToxOntzOjIyOiJyZWxhdGlvbnMuc291cmNlU2l0ZUlkIjtOO31pOjI7YToxOntzOjIyOiJyZWxhdGlvbnMuc291cmNlU2l0ZUlkIjtpOjE7fX19fX1zOjY6ImhhdmluZyI7TjtzOjU6InVuaW9uIjtOO3M6NjoicGFyYW1zIjthOjA6e31zOjE4OiJxdWVyeUNhY2hlRHVyYXRpb24iO047czoyMDoicXVlcnlDYWNoZURlcGVuZGVuY3kiO047czoyNzoiAHlpaVxiYXNlXENvbXBvbmVudABfZXZlbnRzIjthOjA6e31zOjM1OiIAeWlpXGJhc2VcQ29tcG9uZW50AF9ldmVudFdpbGRjYXJkcyI7YTowOnt9czozMDoiAHlpaVxiYXNlXENvbXBvbmVudABfYmVoYXZpb3JzIjthOjE6e3M6MTI6ImN1c3RvbUZpZWxkcyI7TzozNjoiY3JhZnRcYmVoYXZpb3JzXEVsZW1lbnRRdWVyeUJlaGF2aW9yIjoxNjp7czo3OiJibGFoU2VvIjtOO3M6MTI6ImltYWdlQ2FwdGlvbiI7TjtzOjE1OiJvcHRpbWl6ZWRJbWFnZXMiO047czo3OiJzZW9DcmFwIjtOO3M6OToic29tZUFzc2V0IjtOO3M6MTA6InNvbWVNYXRyaXgiO047czoxMDoic29tZU51bWJlciI7TjtzOjEzOiJzb21lUGxhaW5UZXh0IjtOO3M6MTA6InNvbWVSZWNpcGUiO047czoxMjoic29tZVJpY2hUZXh0IjtOO3M6ODoic29tZVVuaXQiO047czo1OiJzdHVmZiI7TjtzOjEwOiJ0ZXN0SU1hZ2VzIjtOO3M6NDoid29vZiI7TjtzOjUxOiIAY3JhZnRcYmVoYXZpb3JzXENvbnRlbnRCZWhhdmlvcgBfY3VzdG9tRmllbGRWYWx1ZXMiO2E6MDp7fXM6NToib3duZXIiO3I6MTt9fXM6NToid2hlcmUiO047czo1OiJsaW1pdCI7aToxO3M6Njoib2Zmc2V0IjtOO3M6NzoiaW5kZXhCeSI7TjtzOjE2OiJlbXVsYXRlRXhlY3V0aW9uIjtiOjA7fQ== |
| 3 | 2 | craft\elements\MatrixBlock | TzozNDoiY3JhZnRcZWxlbWVudHNcZGJcTWF0cml4QmxvY2tRdWVyeSI6NjU6e3M6NzoiZmllbGRJZCI7czoxOiI2IjtzOjc6Im93bmVySWQiO3M6MToiNCI7czoxMToib3duZXJTaXRlSWQiO047czo2OiJ0eXBlSWQiO047czoxMToiZWxlbWVudFR5cGUiO3M6MjY6ImNyYWZ0XGVsZW1lbnRzXE1hdHJpeEJsb2NrIjtzOjU6InF1ZXJ5IjtOO3M6ODoic3ViUXVlcnkiO047czoxMjoiY29udGVudFRhYmxlIjtzOjI5OiJ7eyVtYXRyaXhjb250ZW50X3NvbWVtYXRyaXh9fSI7czoxMjoiY3VzdG9tRmllbGRzIjtOO3M6OToiaW5SZXZlcnNlIjtiOjA7czo3OiJhc0FycmF5IjtiOjA7czoyOiJpZCI7TjtzOjM6InVpZCI7TjtzOjEwOiJmaXhlZE9yZGVyIjtiOjA7czo2OiJzdGF0dXMiO2E6MTp7aTowO3M6NzoiZW5hYmxlZCI7fXM6ODoiYXJjaGl2ZWQiO2I6MDtzOjExOiJkYXRlQ3JlYXRlZCI7TjtzOjExOiJkYXRlVXBkYXRlZCI7TjtzOjY6InNpdGVJZCI7aToxO3M6MTQ6ImVuYWJsZWRGb3JTaXRlIjtiOjE7czo2OiJsZWF2ZXMiO2I6MDtzOjk6InJlbGF0ZWRUbyI7TjtzOjU6InRpdGxlIjtOO3M6NDoic2x1ZyI7TjtzOjM6InVyaSI7TjtzOjY6InNlYXJjaCI7TjtzOjM6InJlZiI7TjtzOjQ6IndpdGgiO047czo3OiJvcmRlckJ5IjthOjE6e3M6MjI6Im1hdHJpeGJsb2Nrcy5zb3J0T3JkZXIiO2k6NDt9czoxMzoid2l0aFN0cnVjdHVyZSI7TjtzOjExOiJzdHJ1Y3R1cmVJZCI7TjtzOjU6ImxldmVsIjtOO3M6MTQ6Imhhc0Rlc2NlbmRhbnRzIjtOO3M6MTA6ImFuY2VzdG9yT2YiO047czoxMjoiYW5jZXN0b3JEaXN0IjtOO3M6MTI6ImRlc2NlbmRhbnRPZiI7TjtzOjE0OiJkZXNjZW5kYW50RGlzdCI7TjtzOjk6InNpYmxpbmdPZiI7TjtzOjEzOiJwcmV2U2libGluZ09mIjtOO3M6MTM6Im5leHRTaWJsaW5nT2YiO047czoxNjoicG9zaXRpb25lZEJlZm9yZSI7TjtzOjE1OiJwb3NpdGlvbmVkQWZ0ZXIiO047czoxNzoiACoAZGVmYXVsdE9yZGVyQnkiO2E6MTp7czoyMDoiZWxlbWVudHMuZGF0ZUNyZWF0ZWQiO2k6Mzt9czozOToiAGNyYWZ0XGVsZW1lbnRzXGRiXEVsZW1lbnRRdWVyeQBfcmVzdWx0IjtOO3M6NDc6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3Jlc3VsdENyaXRlcmlhIjtOO3M6NDU6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3NlYXJjaFNjb3JlcyI7TjtzOjY6InNlbGVjdCI7YToxOntpOjA7czoyOiIqKiI7fXM6MTI6InNlbGVjdE9wdGlvbiI7TjtzOjg6ImRpc3RpbmN0IjtOO3M6NDoiZnJvbSI7TjtzOjc6Imdyb3VwQnkiO047czo0OiJqb2luIjtOO3M6NjoiaGF2aW5nIjtOO3M6NToidW5pb24iO047czo2OiJwYXJhbXMiO2E6MDp7fXM6MTg6InF1ZXJ5Q2FjaGVEdXJhdGlvbiI7TjtzOjIwOiJxdWVyeUNhY2hlRGVwZW5kZW5jeSI7TjtzOjI3OiIAeWlpXGJhc2VcQ29tcG9uZW50AF9ldmVudHMiO2E6MDp7fXM6MzU6IgB5aWlcYmFzZVxDb21wb25lbnQAX2V2ZW50V2lsZGNhcmRzIjthOjA6e31zOjMwOiIAeWlpXGJhc2VcQ29tcG9uZW50AF9iZWhhdmlvcnMiO2E6MTp7czoxMjoiY3VzdG9tRmllbGRzIjtPOjM2OiJjcmFmdFxiZWhhdmlvcnNcRWxlbWVudFF1ZXJ5QmVoYXZpb3IiOjE2OntzOjc6ImJsYWhTZW8iO047czoxMjoiaW1hZ2VDYXB0aW9uIjtOO3M6MTU6Im9wdGltaXplZEltYWdlcyI7TjtzOjc6InNlb0NyYXAiO047czo5OiJzb21lQXNzZXQiO047czoxMDoic29tZU1hdHJpeCI7TjtzOjEwOiJzb21lTnVtYmVyIjtOO3M6MTM6InNvbWVQbGFpblRleHQiO047czoxMDoic29tZVJlY2lwZSI7TjtzOjEyOiJzb21lUmljaFRleHQiO047czo4OiJzb21lVW5pdCI7TjtzOjU6InN0dWZmIjtOO3M6MTA6InRlc3RJTWFnZXMiO047czo0OiJ3b29mIjtOO3M6NTE6IgBjcmFmdFxiZWhhdmlvcnNcQ29udGVudEJlaGF2aW9yAF9jdXN0b21GaWVsZFZhbHVlcyI7YTowOnt9czo1OiJvd25lciI7cjoxO319czo1OiJ3aGVyZSI7TjtzOjU6ImxpbWl0IjtOO3M6Njoib2Zmc2V0IjtOO3M6NzoiaW5kZXhCeSI7TjtzOjE2OiJlbXVsYXRlRXhlY3V0aW9uIjtiOjA7fQ== |
| 4 | 2 | craft\elements\Asset | TzoyODoiY3JhZnRcZWxlbWVudHNcZGJcQXNzZXRRdWVyeSI6NzE6e3M6ODoidm9sdW1lSWQiO047czo4OiJmb2xkZXJJZCI7TjtzOjg6ImZpbGVuYW1lIjtOO3M6NDoia2luZCI7TjtzOjU6IndpZHRoIjtOO3M6NjoiaGVpZ2h0IjtOO3M6NDoic2l6ZSI7TjtzOjEyOiJkYXRlTW9kaWZpZWQiO047czoxNzoiaW5jbHVkZVN1YmZvbGRlcnMiO2I6MDtzOjE0OiJ3aXRoVHJhbnNmb3JtcyI7TjtzOjExOiJlbGVtZW50VHlwZSI7czoyMDoiY3JhZnRcZWxlbWVudHNcQXNzZXQiO3M6NToicXVlcnkiO047czo4OiJzdWJRdWVyeSI7TjtzOjEyOiJjb250ZW50VGFibGUiO3M6MTI6Int7JWNvbnRlbnR9fSI7czoxMjoiY3VzdG9tRmllbGRzIjtOO3M6OToiaW5SZXZlcnNlIjtiOjA7czo3OiJhc0FycmF5IjtiOjA7czoyOiJpZCI7TjtzOjM6InVpZCI7TjtzOjEwOiJmaXhlZE9yZGVyIjtiOjA7czo2OiJzdGF0dXMiO2E6MTp7aTowO3M6NzoiZW5hYmxlZCI7fXM6ODoiYXJjaGl2ZWQiO2I6MDtzOjExOiJkYXRlQ3JlYXRlZCI7TjtzOjExOiJkYXRlVXBkYXRlZCI7TjtzOjY6InNpdGVJZCI7aToxO3M6MTQ6ImVuYWJsZWRGb3JTaXRlIjtiOjE7czo2OiJsZWF2ZXMiO2I6MDtzOjk6InJlbGF0ZWRUbyI7TjtzOjU6InRpdGxlIjtOO3M6NDoic2x1ZyI7TjtzOjM6InVyaSI7TjtzOjY6InNlYXJjaCI7TjtzOjM6InJlZiI7TjtzOjQ6IndpdGgiO047czo3OiJvcmRlckJ5IjthOjE6e3M6MTk6InJlbGF0aW9ucy5zb3J0T3JkZXIiO2k6NDt9czoxMzoid2l0aFN0cnVjdHVyZSI7TjtzOjExOiJzdHJ1Y3R1cmVJZCI7TjtzOjU6ImxldmVsIjtOO3M6MTQ6Imhhc0Rlc2NlbmRhbnRzIjtOO3M6MTA6ImFuY2VzdG9yT2YiO047czoxMjoiYW5jZXN0b3JEaXN0IjtOO3M6MTI6ImRlc2NlbmRhbnRPZiI7TjtzOjE0OiJkZXNjZW5kYW50RGlzdCI7TjtzOjk6InNpYmxpbmdPZiI7TjtzOjEzOiJwcmV2U2libGluZ09mIjtOO3M6MTM6Im5leHRTaWJsaW5nT2YiO047czoxNjoicG9zaXRpb25lZEJlZm9yZSI7TjtzOjE1OiJwb3NpdGlvbmVkQWZ0ZXIiO047czoxNzoiACoAZGVmYXVsdE9yZGVyQnkiO2E6MTp7czoyMDoiZWxlbWVudHMuZGF0ZUNyZWF0ZWQiO2k6Mzt9czozOToiAGNyYWZ0XGVsZW1lbnRzXGRiXEVsZW1lbnRRdWVyeQBfcmVzdWx0IjtOO3M6NDc6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3Jlc3VsdENyaXRlcmlhIjtOO3M6NDU6IgBjcmFmdFxlbGVtZW50c1xkYlxFbGVtZW50UXVlcnkAX3NlYXJjaFNjb3JlcyI7TjtzOjY6InNlbGVjdCI7YToxOntpOjA7czoyOiIqKiI7fXM6MTI6InNlbGVjdE9wdGlvbiI7TjtzOjg6ImRpc3RpbmN0IjtOO3M6NDoiZnJvbSI7TjtzOjc6Imdyb3VwQnkiO047czo0OiJqb2luIjthOjE6e2k6MDthOjM6e2k6MDtzOjEwOiJJTk5FUiBKT0lOIjtpOjE7czoyNDoie3slcmVsYXRpb25zfX0gcmVsYXRpb25zIjtpOjI7YTo0OntpOjA7czozOiJhbmQiO2k6MTtzOjQwOiJbW3JlbGF0aW9ucy50YXJnZXRJZF1dID0gW1tlbGVtZW50cy5pZF1dIjtpOjI7YToyOntzOjE4OiJyZWxhdGlvbnMuc291cmNlSWQiO3M6MToiNCI7czoxNzoicmVsYXRpb25zLmZpZWxkSWQiO3M6MToiMiI7fWk6MzthOjM6e2k6MDtzOjI6Im9yIjtpOjE7YToxOntzOjIyOiJyZWxhdGlvbnMuc291cmNlU2l0ZUlkIjtOO31pOjI7YToxOntzOjIyOiJyZWxhdGlvbnMuc291cmNlU2l0ZUlkIjtpOjE7fX19fX1zOjY6ImhhdmluZyI7TjtzOjU6InVuaW9uIjtOO3M6NjoicGFyYW1zIjthOjA6e31zOjE4OiJxdWVyeUNhY2hlRHVyYXRpb24iO047czoyMDoicXVlcnlDYWNoZURlcGVuZGVuY3kiO047czoyNzoiAHlpaVxiYXNlXENvbXBvbmVudABfZXZlbnRzIjthOjA6e31zOjM1OiIAeWlpXGJhc2VcQ29tcG9uZW50AF9ldmVudFdpbGRjYXJkcyI7YTowOnt9czozMDoiAHlpaVxiYXNlXENvbXBvbmVudABfYmVoYXZpb3JzIjthOjE6e3M6MTI6ImN1c3RvbUZpZWxkcyI7TzozNjoiY3JhZnRcYmVoYXZpb3JzXEVsZW1lbnRRdWVyeUJlaGF2aW9yIjoxNjp7czo3OiJibGFoU2VvIjtOO3M6MTI6ImltYWdlQ2FwdGlvbiI7TjtzOjE1OiJvcHRpbWl6ZWRJbWFnZXMiO047czo3OiJzZW9DcmFwIjtOO3M6OToic29tZUFzc2V0IjtOO3M6MTA6InNvbWVNYXRyaXgiO047czoxMDoic29tZU51bWJlciI7TjtzOjEzOiJzb21lUGxhaW5UZXh0IjtOO3M6MTA6InNvbWVSZWNpcGUiO047czoxMjoic29tZVJpY2hUZXh0IjtOO3M6ODoic29tZVVuaXQiO047czo1OiJzdHVmZiI7TjtzOjEwOiJ0ZXN0SU1hZ2VzIjtOO3M6NDoid29vZiI7TjtzOjUxOiIAY3JhZnRcYmVoYXZpb3JzXENvbnRlbnRCZWhhdmlvcgBfY3VzdG9tRmllbGRWYWx1ZXMiO2E6MDp7fXM6NToib3duZXIiO3I6MTt9fXM6NToid2hlcmUiO047czo1OiJsaW1pdCI7aToxO3M6Njoib2Zmc2V0IjtpOjA7czo3OiJpbmRleEJ5IjtOO3M6MTY6ImVtdWxhdGVFeGVjdXRpb24iO2I6MDt9 |
+----+---------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
4 rows in set (0.00 sec) 
```

虽然名称不同，但目的是一样的:跟踪模板中可能出现类似``{% craft.entries.section('blog').limit(20) %}``的查询的地方。

这样，如果在标签中的一个查询中的*元素发生变化，缓存将会被刷新以反映新的数据。*

如果你不明白为什么 Craft 3.x 查询看起来是这样，那是因为这是二进制查询对象的一个序列化版本。

再次注意，如果在您的``{% cache %}``标签中有大量的查询，您将在数据库中得到大量的行。

如果我们有一个网站，上面有 10，000 个博客条目，每个博客在``{% cache %}``标签中有 10 个元素查询，这意味着我们将在`templatecaches`表中得到 10，000 行，在`templatecachequeries`或`templatecachecriteria`表中得到 100，000 行。仅仅为了博客。

## 缓存为缓存能

标签可以做更多的事情，你可以在[工艺模板文档](https://craftcms.com/docs/templating/cache)页面上阅读。希望这篇一般性介绍能让你思考何时以及如何使用它。

如果您想获得基于缓存的性能的终极，请查看使用 Craft CMS 进行页面缓存的文章。

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计