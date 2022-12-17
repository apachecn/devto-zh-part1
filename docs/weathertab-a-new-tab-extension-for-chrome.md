# Chrome 的新标签扩展

> 原文：<https://dev.to/peiche/weathertab-a-new-tab-extension-for-chrome>

在过去的一周里，我完成了一个新的 Chrome 扩展，我想我应该写一写它。它被称为 WeatherTab，它用今天的天气信息和接下来四天的天气预报取代了新的标签页。

我很喜欢编写这个扩展，除非有任何重大突破，否则我认为它真的不需要任何更新。(既然已经说了，当然……)

## 关于 WeatherTab

既然我已经添加完了功能，我想谈谈是什么让它达到这一步的。我希望这个扩展有几样东西:1)一个简单的演示，2)天气描述和匹配的图标，3)一个大的图像来匹配当前的天气和大概的位置，类似于雅虎的天气应用程序。

当我开始构建扩展时，我在 Mashape 上找到了一个免费使用的天气 API。API 实际上充当了一个中介，它输出雅虎天气数据。从那以后，我就开始直接使用雅虎的 API 了，不过我会说到这一点的。

## 材料设计

因为这是为 Chrome 设计的，我认为使用材质设计来构建界面是有意义的。很久以前，我曾用过 Material Design Lite，这是 Google 自己的 web 实现，但现在已经停止使用了，取而代之的是 Web 的 Material 组件。(伙计，谷歌在给东西命名方面是不是很烂。)由于它似乎处于一个相当稳定的变化状态，我决定使用一个存在时间更长(似乎更稳定)的东西:Materialize，一个基于材料设计的第三方 CSS 和 JavaScript 框架。

旁注:让我感到奇怪的是，谷歌花了这么长时间才推出官方的东西(而且他们还在对它进行修改)，因为他们是首先引入材料设计的人。但是我跑题了。

## 天气图标

我原本想在自己的应用程序中使用与谷歌显示的彩色图标类似的图形，但我找不到任何超过 20 个图标的免费资源。雅虎天气 API 总共有 49 个状态代码，我不想在多个天气描述中重复使用图标。

我偶然发现了一个很棒的图标字体，叫做天气图标，但不幸的是现在已经不再使用了。不仅有超过 200 个独特的天气图标(绰绰有余)，而且作者实际上创建了别名来匹配常用的天气 API 的状态代码。

## 获取天气数据

正如我提到的，我使用的是一个免费的第三方 API，它返回雅虎天气数据。我决定放弃它，转而使用雅虎自己的 API。

雅虎有一个统一的接口来访问他们所有的数据，他们称之为 YQL(雅虎查询语言)。语法几乎与 SQL 相同(作为一个全栈开发人员，我在这方面有丰富的经验)，所以很容易掌握。

奇怪的是，你不能直接用经纬度坐标得到天气预报。相反，你必须使用地球上的 ID。下面是一个获取经度和纬度的 woeid 的示例查询(用“1，1”替换实际值)。

```
SELECT woeid FROM geo.places WHERE text="(1,1)" 
```

Enter fullscreen mode Exit fullscreen mode

将它嵌套在天气预报查询中可以获得精确位置的天气数据:

```
SELECT * FROM weather.forecast WHERE woeid IN (SELECT woeid FROM geo.places WHERE text="(1,1)") 
```

Enter fullscreen mode Exit fullscreen mode

就 API 的使用而言，这相当于两次调用来获得一个数据集。不过，这真的不是问题，因为雅虎的公共 API 允许每个 IP 在一天内最多 2000 个请求。

我还想让用户在华氏温度和摄氏温度之间切换。这可以很容易地通过在 u 参数中指定“F”或“C”的值来实现。(扩展使用“存储”权限来记住您选择的内容。)

```
SELECT * FROM weather.forecast WHERE woeid IN (SELECT woeid FROM geo.places WHERE text="(1,1)") AND u="C" 
```

Enter fullscreen mode Exit fullscreen mode

为了将 YQL 查询传递给 API，您需要调用 https://query.yahooapis.com/v1/public/yql 的 URL。查询需要编码为参数`q`的值，如果您希望 JSON 数据出现在响应中(默认为 XML)，您需要包含参数`format=json`。所以，把这一切放在一起:

```
https://query.yahooapis.com/v1/public/yql?q=SELECT%20*%20FROM%20weather.forecast%20WHERE%20woeid%20IN%20%28SELECT%20woeid%20FROM%20geo.places%20WHERE%20text%3D%22%281%2C1%29%22%29%20AND%20u%3D%22C%22&format=json 
```

Enter fullscreen mode Exit fullscreen mode

将其放入 GET 请求中，JSON 响应将包含您的小心脏需要的所有天气数据:最高和最低温度、10 天预报、湿度等等。由于我不想在收到响应后手工编写大量的 HTML 代码，所以我将数据传递到 Handlebars 模板中，并在那里进行渲染。

## 使用模板文字

这对我来说是新的。在过去，我会用字符串连接插入纬度和经度值，就像这样:

```
'SELECT woeid FROM geo.places WHERE text="(' + lat + ',' + long + ')"' 
```

Enter fullscreen mode Exit fullscreen mode

但是在 ES2015 中，我们现在可以使用模板文字。用反斜线(`)而不是单引号或双引号将字符串值括起来，并且可以在美元符号和花括号中使用变量，如下所示:

`从 geo.places 中选择 woeid，其中 text="(${lat}，${long})"`

 ``正如你所想象的，浏览器支持是…缺乏的。如果这是一个 web 应用程序，我需要担心 Internet Explorer 或其他旧浏览器，并且我只是坚持使用模板文字，我将需要使用 Babel 这样的 transpiler 来将我漂亮的现代 JavaScript 转换成旧浏览器可以理解的东西。

幸运的是，这段代码存在于为 Chrome 编写的扩展中，我不需要借助 transpiler，这段 JavaScript 按原样执行*sans probl me*。

## 从 Flickr 获取图片

雅虎天气应用程序，根据它自己的描述，有“惊人的 Flickr 照片”，“符合你的位置，一天中的时间和当前的条件。”我想我已经非常接近了，但是我不知道雅虎到底是怎么做到的。因此，我的解决方案已经足够好了。

我在 YQL 中有两个主要的 Flickr 搜索查询，如果第一个失败，就退回到第二个:首先是按纬度和经度，然后是按地区(在美国，这意味着按州)。一般查询语法是相同的:

`SELECT * FROM Flickr . photos . search WHERE API _ key = " XXX " AND group _ id = " 1463451 @ N25 " AND has _ geo = " true "[位置资料在此处] AND tags="${weather}、${timeOfDay}、$ { season } " AND tag _ mode = " all "`

 ``要使用 Flickr API，你必须注册一个密钥。它是免费的，既然你可以通过查看源代码来弄清楚，我想我是否在这里删除它真的没有关系。随便啦。

群组 ID 属于 Project Weather，这是雅虎自己在 2010 年建立的一个群组。该组中的所有照片都可供公众使用，并且几乎所有照片都有地理标记，这使得按位置查找照片相对容易。(我猜雅虎后来被威瑞森收购了？因此，该组没有添加任何新照片，但这仍然是您找到有用天气图片的最佳选择。)

我不是在照片上搜索文本(如姓名、描述等)，而是进行标签搜索。Project Weather 中的每张照片都被广泛标记，因此我可以搜索天气状况(晴天、多云等)。)、一天中的一般时间(上午、下午、晚上或夜间)和季节(春季、夏季、秋季或冬季)。我还在搜索中指定所有标签必须匹配。

第一次搜索包括所有三种标记类型:天气、时间和季节。如果失败，搜索包括天气和季节。如果搜索失败，那么在进入第二种查询类型之前，搜索只查找天气情况。

我的两个查询在上面的部分有所不同，这里显示的是位置信息。第一个查询进行径向搜索，从纬度和经度开始，搜索半径最大为 20 英里(Flickr 搜索 API 允许的最大值)。

 ``lat="${lat}" AND lon="${lon}" AND radius="20" AND radius_units="mi"`` 

 ``第二个查询是在没有找到天气和精确位置的图像时运行的，它通过地区代码进行更广泛的搜索。weather API 调用返回的数据中包含了地区，但是我不能直接使用它。我必须使用 Yahoo API 称为地点 ID 的东西，所以我需要从另一个数据表中获取该值。(因为这个查询是一个单独的 API 调用，所以我还需要在子查询中传递 Flickr API 键。)

 ``place_id IN (SELECT place_id FROM flickr.places WHERE query="${search.region}" AND api_key="xxx")`` 

 ``还有一个最后的回退查询，只有在所有基于位置的查询都没有返回匹配时才运行。它只是搜索天气状况，而不考虑地点或时间:

`SELECT * FROM Flickr . photos . search 其中 API _ key = " XXX " AND group _ id = " 1463451 @ N25 " AND tags = " $ { weather } "`

 ``因此，有可能针对 Flickr API 运行总共七个查询，都是为了找到与用户的位置、时间、季节和天气条件相关的照片。(雅虎天气应用在描述“令人惊叹的 Flickr 照片”时没有提到季节，但它对我来说似乎很重要。毕竟我不想在盛夏的时候展示一个阳光明媚的冬天画面。幸运的是，这都是雅虎公共 API 的一部分，并且在每天每个 IP 2000 个请求的限制下。我不认为扩展会达到那个上限。(既然我已经说过了……)

## 显示图像

多亏了雅虎 API 的速度，天气数据几乎可以立即返回(和渲染)。根据获取相关照片之前需要处理的查询数量，可能需要几秒钟才能显示照片。出于这个原因，我不会让用户在显示天气信息之前等待一张照片。所以最初，一旦天气显示出来，用户看到的是灰色背景。

然而，一旦照片准备好了，只是把它插入位置是不和谐的。相反，我用一种类似材质设计的过渡来制作它的可见性动画。请点击这里查看:

[https://youtu.be/OiKV3rHt5Wc](https://youtu.be/OiKV3rHt5Wc)

这是通过 CSS 属性 clip-path 实现的，它没有最好的浏览器支持。幸运的是，它在 Chrome 中有足够的支持，我可以用它来显示一个简单的形状(在本例中，是一个圆)。

默认情况下，包含图像(作为背景)的元素具有 clip-path: circle(0%)的样式，本质上使其不可见。我没有直接插入图像，而是使用以下代码预加载它:

 ``$('<img />').attr('src', img).on('load', function() {
$(this).remove();
$('.weather-container__bg').css('background-image', img).addClass('open');
});`` 

 ``此处创建的图像不会附加到文档正文，因此不会呈现，但会下载源代码。一旦 load 事件触发(意味着浏览器已经下载并缓存了图像)，我就可以删除原始图像并将源作为背景图像附加到真实元素上。因为图像已经被下载了，它是立即可用的。然后，我将 open 类添加到元素中，该元素的样式为 clip-path: circle(100%)。这与元素上定义的过渡相结合，将图像动画化到视图中。

点击这里查看扩展！