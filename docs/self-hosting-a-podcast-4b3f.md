# 自主主持播客

> 原文：<https://dev.to/codemouse92/self-hosting-a-podcast-4b3f>

你曾经梦想过主持自己的播客吗？或者，也许你已经有了一个，但你不知道如何让它进入世界各地听众的耳塞。

当我想出“鼠爪”节目的主意时，第一个问题是如何发布它。当然，我们可以把它作为一个随机文件放在某个地方，但是我们真的想把它放在那里！我对不同的服务做了很多研究。大多数都是要花钱的，而且免费选项要么是试用版，要么过于有限，要么简直令人毛骨悚然。

然后我想起了在当今服务型经济中我们经常忘记的一件事:**你真的可以自己做**。

这是你需要的。

*   一个普通的老网站。

*   Archive.org 账户(可选，见下文)。

*   文本编辑器。

*   一点点耐心。

# 弊端

在我们讨论如何做到这一点之前，我们需要排除一些不利因素:

*   你是自己的技术支持。如果东西坏了，你负责修理。

*   您不会有高级统计数据，比如订户数量。一些目录可能会提供这些信息，但许多目录不会。

*   你不能轻易地从你的播客中赚钱，因为没有办法知道有多少人在听或订阅。

*   您的播客将是免费的；没办法收费让人听。

只要你不介意这些限制，我们继续吧！

# 选择主机

首先，我们必须有一个地方来提供实际的播客音频。主持人应该是...

*   非常可靠-你不希望听众突然发现他们不能播放播客。

*   速度快——你不希望你的播客一直“缓冲”。

*   有能力处理高需求-最终，每个人都从主机流播客。

有两种方法可以做到这一点:我们可以**自己主持**或者**上传到 Archive.org**。

自托管方法非常简单。如果您的服务器满足所讨论的要求，就像把 MP3 音频文件放在上面一样简单。别紧张，皮斯。

然而，如果你不想为此使用你自己的服务器，你可以使用**互联网存档**，这是我将在本教程中采用的方法。

通过互联网存档发布只有两个限制:

1.  有限的统计数据。

2.  材料必须是知识共享或公共领域。

第二个可能听起来很可怕，但是对于免费收听的播客来说，没有任何可以想象的理由可以说明你不能在知识共享下获得许可。[Creative Commons Attribution non commercial-NoDerivs](https://creativecommons.org/licenses/by-nc-nd/3.0/)是指任何人都可以自由收听和分享，但不能将其重新混合或用于自己的商业目的。你没有放弃自己的工作权利。

# 准备材料

在我们开始整个过程之前，让我们确保我们的播客准备好了。你应该有以下材料。如果你错过了什么，现在就去做！

*   以纯文本形式对您的 podcast 专题节目进行一段描述。你会在几个地方需要这个。

*   播客的方形图像。应该是方形的，以`jpg`格式保存。就我个人而言，我发现 400x400 的尺寸提供了大小和质量之间的完美平衡。

*   你的播客的网页。说真的，现在就去做！这应该包括一个完整的剧集索引，以及不同地方的链接来收听和下载。

*   显然，播客本身。这应该以`MP3`格式保存。如果你使用 Archive.org 来托管文件，你也可以上传`OGG`格式的文件，它会为你导出`MP3`;不管怎样，所有的播客目录都需要`MP3`。

注意:如果你在知识共享下许可播客，你应该在播客中声明该许可。你真的可以在任何地方提到它，但 outro 是最符合逻辑的地方。；)

# 上传到 Archive.org

注意:如果您选择自己托管文件，请跳过这一部分。

一旦你在 Archive.org 创建了一个账户，进入上传页面，点击`Upload Files`。点击`Select Files`，将以下内容上传至同一项目:

*   作为`MP3`或`OGG`的播客片段。**每个项目只能上传一集。**

*   播客封面图片为`JPG`。

注意:您为剧集音频文件选择的文件名很重要。我推荐像`myawesomepodcast_ep1.mp3`这样严格的命名约定。坚持每集的命名惯例。

按照我在这里使用的格式填写下面的字段，但是根据您的需要更改值。

```
Page Title: Episode 1: Cool Stuff

Page URL: MyAwesomePodcastEp1

Description: This is the one-paragraph description that you wrote in the previous step. Although the description itself needs to be plain-text in other places, you can go nuts with formatting here.

Subject Tags: podcast, topic, another topic, yet another topic, you get the idea

Creator: My Awesome Organization

Date: (The current date.)

Collection: Community Audio

Test Item: No

Language: English

License: (Choose the options for your license.) 
```

Enter fullscreen mode Exit fullscreen mode

然后，点击`Upload and Create Your Item`。

您的 podcast 徽标应该出现在项目页面上，位于音频播放栏的左侧。如果图像*未出现*，则`Edit`该项目，并选择`change the information`。在`Files, formats, and derivations:`下，找到你上传的图像文件。确保其`Format`设置为`Item Image`。保存您的更改。

注意:Internet Archive 将自动派生您的项目，创建它们托管的其他格式的音频。如果您只上传了一个`OGG`，给它一些时间，那么`MP3`表单将会自动创建。

# 设置你的 RSS 文件

要使您的 podcast 可被发现和订阅，您需要创建一个 RSS 文件。这是让大多数人害怕的部分，但是这并不像听起来那么难！

(如果你想看到一个完整的、正确的播客 RSS 文件，可以查看一下[mouse paw’dcast](https://mousepawmedia.com/rss/mousepawdcast.xml)的 RSS 文件，然后查看源代码。)

## 显示数据

首先打开您最喜欢的支持 XML 的文本编辑器，创建一个名为`myawesomepodcast.xml`的新文件。将以下内容粘贴到:

```
<rss version="2.0" xmlns:media="http://search.yahoo.com/mrss/" xmlns:atom="http://www.w3.org/2005/Atom"
    xmlns:creativeCommons="http://backend.userland.com/creativeCommonsRssModule"
    xmlns:itunes="http://www.itunes.com/dtds/podcast-1.0.dtd">
    <channel>
    </channel>
</rss> 
```

Enter fullscreen mode Exit fullscreen mode

在`<channel>`节点中，输入以下内容，但是将 URL 改为托管 RSS *和*的地方。

```
<atom:link href="https://example.com/rss/myawesomepodcast.xml" rel="self" type="application/rss+xml" /> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将在刚刚定义的`<channel>`子节点之后的`<channel>`节点中设置节目的元数据..这个部分只需要做一次！

首先，我们定义播客节目的标题和描述，以及节目主页的链接。

```
My Awesome Podcast
<link>https://example.com/podcast</link>
<description>This is a one-sentence description of your show.</description> 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要播客编辑的电子邮件地址，以解决与节目相关的问题，以及网站管理员的电子邮件地址来解决技术问题。

```
<managingEditor>podcast@example.com (Example)</managingEditor>
<webMaster>webmaster@example.com (Example)</webMaster> 
```

Enter fullscreen mode Exit fullscreen mode

为你的播客定义语言和版权。即使这是知识共享协议，你/你的组织在技术上仍然拥有版权。

```
<language>en-us</language>
<copyright>Copyright (C) 2018 My Awesome Organization. All Rights Reserved.</copyright> 
```

Enter fullscreen mode Exit fullscreen mode

如果你选择在 Creative Commons 下许可(同样，如果你在 Archive.org 上托管文件，这是必要的)，接下来是许可。您需要使用官方网址获得**许可证契约**。完整名单见 creativecommons.org/licenses 的[。](https://creativecommons.org/licenses/) 

```
<creativeCommons:license>
    https://creativecommons.org/licenses/by-nc-nd/4.0/
</creativeCommons:license> 
```

Enter fullscreen mode Exit fullscreen mode

你*最初*发布播客的日期在`<pubDate>`节点下，而你*更新播客的最后日期*在`<lastBuildDate>`节点下。从技术上来说，这两者都是可选的，但是拥有它们是很好的。(参见[RSS 中的和有什么区别？](https://stackoverflow.com/q/1698076/472647)。我们在这里使用的是 [RFC 822](https://www.w3.org/Protocols/rfc822/) 格式。

注意:虽然您必须在格式中包含技术上正确的时间，但是时间*的准确性*在这里并不重要。如果你只是混时间没关系。

```
<pubDate>Sat, 27 May 2017 02:00:00 GMT</pubDate>
<lastBuildDate>Wed, 31 Jan 2018 02:00:00 GMT</lastBuildDate> 
```

Enter fullscreen mode Exit fullscreen mode

我们还包括我们可爱的节目图像，这应该上传到播客的网站。我们还包括播客节目的名称，以及播客网页的 URL。

```
<image>
    <url>https://example.com/img/podcast_cover.jpg</url>
    My Awesome Podcast
    <link>https://example.com/podcast</link>
</image> 
```

Enter fullscreen mode Exit fullscreen mode

出于技术原因，需要这个标签。包括它就行了。

```
<docs>http://www.rssboard.org/rss-specification</docs> 
```

Enter fullscreen mode Exit fullscreen mode

如果可能的话，我们希望在 iTunes 上托管，特别是因为许多其他服务都来自那个目录！我们必须包括几个标签来考虑包含在那里。

首先显然是作者身份。

```
<itunes:author>My Awesome Organization</itunes:author> 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要选择一些关键字和一个类别来组织我们的播客。([这里是 iTunes 类别列表](https://gist.github.com/skattyadz/814315))。我们还需要指定我们的播客是否仅供成人观看。

```
<itunes:keywords>software,programming,game development,education,open source,technology</itunes:keywords>

<itunes:category text="Technology"/>

<itunes:explicit>clean</itunes:explicit> 
```

Enter fullscreen mode Exit fullscreen mode

我们在播客网页上提供了播客封面图片的链接。

警告:请注意，我们不是在 HTTPS 提供图片网址！RSS 和 HTTPS 有一种爱恨交加的关系，主要是仇恨。有些领域与 HTTPS 不兼容，这就是其中之一。

```
<itunes:image href="http://mousepawmedia.com/img/ours/mousepawdcast_cover.jpg"/> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将自己或我们的组织定义为播客的所有者，并附上联系电子邮件。

```
<itunes:owner>
<itunes:name><![CDATA[My Awesome Organization]]></itunes:name>
    <itunes:email>podcast@example.com</itunes:email>
</itunes:owner> 
```

Enter fullscreen mode Exit fullscreen mode

## 剧集数据

现在来加一集！这只是低于我们的显示数据，仍然在`<channel>`节点内。

每一集你都需要这些`<item>`节点中的一个，你应该按降序排列它们(最新的最先)。

```
<item>

</item> 
```

Enter fullscreen mode Exit fullscreen mode

在`<item>`节点中，让我们定义我们的剧集名称，以及到 Archive.org 页面的链接(或者，我们网站上的一个专门的剧集页面)。注意，我们需要`<link>`和`<guid>`T3 下的链接

```
Episode 1: Cool Stuff
<link>https://archive.org/details/MyAwesomePodcastEp1</link>
<description>
    This is the one-paragraph description that you wrote in the previous step. Although the description itself needs to be plain-text in other places, you can go nuts with formatting here.
</description>
<guid isPermaLink="true">https://archive.org/details/MyAwesomePodcastEp1</guid> 
```

Enter fullscreen mode Exit fullscreen mode

我们以 RFC 822 的格式宣布了这一集的出版日期。

```
<pubDate>Fri, 26 Jan 2018 04:00:00 GMT</pubDate> 
```

Enter fullscreen mode Exit fullscreen mode

这就是事情变得棘手的地方。如果你篡改这一部分，播客目录将无法看到你的剧集！

我们实际上定义了剧集信息*两次*，遵循两种主要格式。所有的播客目录都使用其中之一。

首先是`<media:`格式(这里定义的)。我们从定义内容本身开始。这可以完全像你在这里看到的那样使用，只是有所改变...

*   直接`url`到剧集的`MP3`，以及
*   播客的确切时间为*秒*。

```
<media:content
    medium="audio"
    url="https://archive.org/download/MyAwesomePodcastEp1/myawesomepodcast_ep1.mp3"
    type="audio/mpeg"
    isDefault="true"
    expression="full"
    duration="3850">

</media:content> 
```

Enter fullscreen mode Exit fullscreen mode

剩下的`<media:`点点头就去下的*节点`<media:content>`了。*

现在我们指定标题和描述。

```
<media:title type="plain">Episode 1: Cool Stuff</media:title>

<media:description>
    This is the one-paragraph description that you wrote in the previous step. Although the description itself needs to be plain-text in other places, you can go nuts with formatting here.
</media:description> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要指定该集是仅面向成人(`adult`)还是面向所有观众(`nonadult`)。如果你愿意，你也可以使用 ICRA 或美国电影协会格式(参见[规范](http://www.rssboard.org/media-rss#media-rating))。

```
<media:rating scheme="urn:simple">nonadult</media:rating> 
```

Enter fullscreen mode Exit fullscreen mode

指定显示封面图像的路径...

```
<media:thumbnail url="https://example.com/img/podcast_cover.jpg"/> 
```

Enter fullscreen mode Exit fullscreen mode

我们还应该为我们的剧集设置几个关键词。

```
<media:keywords>
    programming, writing, creative writing, software, development, mousepaw media
</media:keywords> 
```

Enter fullscreen mode Exit fullscreen mode

`<media:content>`节点到此为止！

现在，我们可以通过第二种格式`<enclosure>`包含到媒体的路径。(使用它的服务将获取在我们的`<media:`节点上方*定义的剧集数据。)记住，这发生在`<media:content>`节点*之后的*。*

同样，您可以完全按照我的方式使用这个标记，只需更新两个字段...

*   `url`，其中**必须使用 HTTP，而不是 HTTPS！**如果您使用 HTTPS 链接到`MP3`，某些服务将无法观看该剧集。

*   *字节*中的`length`。很奇怪，我知道。

```
<enclosure url="http://archive.org/download/MyAwesomePodcastEp1/myawesomepodcast_ep1.mp3" length="73312022" type="audio/mpeg"/> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们需要用另外三个领域让 iTunes 满意。

首先，我们告诉 iTunes 在哪里可以找到我们的播客封面图片。

```
<itunes:image href="https://example.com/img/podcast_cover.jpg"/> 
```

Enter fullscreen mode Exit fullscreen mode

我们还需要指定评级(`clean`或`explicit`)...

```
<itunes:explicit>clean</itunes:explicit> 
```

Enter fullscreen mode Exit fullscreen mode

...我们以`HH:MM:SS`格式列出播客的持续时间。

```
<itunes:duration>1:04:10</itunes:duration> 
```

Enter fullscreen mode Exit fullscreen mode

确保您已经关闭了所有这些下面的`</item>`节点。

现在，将您的 RSS 上传到您在 XML 文件顶部指定的 URL 处的播客网站。

在我们继续之前，您应该验证您的文件！我使用 [W3C 提要验证服务](https://validator.w3.org/feed/)来检查语法错误。我已经测试过了——它不会*而不是*给出假阴性！如果它抱怨，就解决问题。

注意:如果你收到关于无效 URL 的投诉，你应该使用`HTTP`而不是`HTTPS`来处理。

# 进入播客目录

太好了，你可以开始直播了！现在有趣的部分来了:提交播客目录！

以下是我推荐的(提交说明链接)。在大多数情况下，你提交你的播客供考虑，他们决定是否包括它。因此，质量很重要！

*   SubscribeOnAndroid :它并没有列出你的播客，而是为你提供了一个方便的“订阅”链接，可以在 12 个以上基于 Android 的播客应用上使用。
*   苹果播客:如果你只想提交到一个目录，就选这个吧！如果你在 iTunes 上，你还会自动被 covery . FM、PocketCasts、PodcastAddict、iCatcher、PlayerFM 和许多其他服务选中。
*   Google Play:Android 上的一项主要播客服务，毫无疑问！在这里上车很容易。
*   iHeartRadio 是另一个流行的服务，提交就像网页表单一样简单。
*   [调谐](https://help.tunein.com/customer/portal/emails/new?ticket%5Blabels_new%5D=podcast&t=764630)
*   戴夫:说真的，还有哪里？如果你有一个技术播客，只要发邮件给这里的好人考虑一下。

注意:我知道 Stitcher 是一个主要的播客目录，但我不喜欢他们的播客服务条款。

当你被添加到每个目录时，一定要添加链接到你的播客网页，以鼓励人们订阅！

# 继续走！

就是这样！你有一个真正的直播播客，全世界的人都可以欣赏！现在，你只需要做几件显而易见的事情来让它继续下去:

*   显然，录制新的剧集。

*   每当你录制一个新的剧集并上传时，将其添加到自己的`<item>`节点上的 RSS 提要中，在所有其他剧集之上。重新验证你的 RSS 源！

*   向你所有的朋友、家人和社交媒体关注者宣布，他们可以在所有这些令人敬畏的服务上收听你的节目。

*   坐下来微笑。你的声音可以在世界各地所有主要的播客服务中听到。太棒了！

哦对了，看看我自己公司的播客， [**MousePaw 的播客**](https://dev.to/mousepaw) ！`</cursory_plug>`