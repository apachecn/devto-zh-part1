# 无头的爱与理智

> 原文：<https://dev.to/kmelve/headless-in-love-with-sanity-91h>

* * *

当 [Netlife](https://www.netlife.com) 决定重塑品牌时，我们不仅给[换了一个新的视觉形象，包括我们自己的字体](https://www.underconsideration.com/brandnew/archives/new_logo_and_identity_for_and_by_netlife.php)，我们还决定探索新的技术前沿。我们对建立在多功能的 Craft CMS(T7)之上的[以前的网站](https://retro.netliferesearch.com)非常满意，但是我们决定探索一下在网络上具有独立前端的无头 CMS 是否像宣传的那样(剧透:他们做到了)。

当来自 [Bengler](https://bengler.no) 的朋友们带着 [Sanity](https://sanity.io) 出现时，我们已经开始在另一个解决方案中点击鼠标来实现基本的信息架构，这是他们过去三年一直在开发的 CMS，并为其成立了一家新公司。我尝试了一下，很快就被说服了，只用了不到一天的时间，就通过理智地编写简单的 JavaScript 对象，从零开始重新创建了我们刚刚花了几个星期的东西。尽管它还处于测试阶段，我们还是决定把它作为我们新网站的后端，并且从未回头。

**如果你正在考虑变得没头没脑，以下是你应该考虑保持理智的 5 个理由:**

## 1。你将在 2 分钟内开始运行。ðÿ，

启动你的终端，写下`npm install -g @sanity/cli && sanity init`，遵循一些指令，你就可以开始了。如果您是第一次，我建议您从“电影数据库模式”开始。通过[遵循模式指令](https://www.sanity.io/docs/reference/schema-types)，数据类型和内容字段在简单的 JavaScript 中定义。一旦你掌握了窍门(例如，记住了大部分数据类型)，你就可以和你的编辑同事坐下来，快速地构建、测试和调整 Content Studio，使之对他们有意义。它有热模块重装，这意味着你可以立即看到你的变化。又瘦又贱！

```
$ npm install -g @sanity/cli && sanity init
updated 1 package in 14.462s
This utility walks you through creating a Sanity installation.
Press ^C at any time to quit.
Looks like you already have a Sanity-account. Sweet!
? Select project to use Create new project
? Informal name for your project test-project
? Name of your first data set: production
? Output path: ~/Sites/SanityDemo
? Select project template Clean, minimal project
âœ” Bootstrapping files from template
âœ” Resolving latest module versions
âœ” Creating default project files
âœ” Fetching packages      â–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ª 100% (0.68s)
âœ” Linking dependencies   â–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ªâ–ª 100% (14.26s)
â— Linking dependencies   â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–« 0% (0.00s)
â— Linking dependencies   â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–«â–« 0% (0.00s)
âœ” Saved lockfile
Success! You can now run "sanity start" 
```

Enter fullscreen mode Exit fullscreen mode

虽然其他 headless CMSes 提供了用于编辑内容模型和字段的控制面板，但我更喜欢用代码来完成。这意味着跨项目重用模式更容易。虽然明智的做法是保持这段代码相当冗长和简单，但是您也可以通过迭代数组等方式生成字段集。您还可以模块化常见的可疑内容，例如标题字段。挺俏皮的！

[![Starting from scratch is easy. If you put a `block` type in an `array` type you’ll get the rich text editor. Adding other types to the array gets available in the â€˜insert’ menu. Yes, rich text is just an array where one of the types happens to be block text.](img/6d1604f0c69f4b7844f8f92d254d736c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AJOdPcLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bqh1odh7f5byn8n8tym3.gif)

图:从头开始很容易。如果你把一个`block`类型放到一个`array`类型中，你将得到一个富文本编辑器。在“插入”菜单中可以向数组中添加其他类型。是的，富文本只是一个数组，其中一个类型恰好是块文本。

## 2。你可以编辑《ðÿ–‹ðÿ》

让 Sanity 脱颖而出的是它的编辑器，即[内容工作室](https://www.sanity.io/docs/content-studio)，可以根据你的需要进行调整、扩展和定制。第一步是配置内容的预览。这是通过将`preview`对象附加到您想要在预览中显示的类型来完成的。您还可以通过`prepare`功能发送您选择的任何内容，进行更多定制。

[![We use Sanity for handling registrations to workshops and other events. Here we can preview how many seats there are left on eachÂ event.](img/4d354ce40efd7edfde685fb6ace13231.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Dek4RR2z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sa72itk9xtakfqm2f1ml.png)

图:我们使用 Sanity 来处理研讨会和其他活动的注册。在这里，我们可以预览每场比赛还有多少座位。

我们已经开始对客户工作使用理智。在我们与 [U4 反腐败资源中心](https://beta.u4.no)的一个项目中(仍在开发中！)他们希望将富文本域与文本分析服务相集成，以帮助他们跟踪文本质量。我不是 React 专家，但我设法扩展了富文本编辑器，使其包含一行由 [readable.io 的 API](https://readable.io/) 提供的许多数据点的一小部分。你可以在下面看到概念证明。我们还制作了一个类似于电子表格的输入字段，用于创建和编辑表格数据，并且有传言说存在一个用于实时 3D 渲染蛋白质分子的输入字段。).

[![I set the editor to check the text every five seconds of writing. It’s also interesting to have the running estimated reading time while you write. Hopefully it will encourage brevity.](img/33e888c049774c150d33a73cc351473e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ntCjKkLI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/if2r06xg3hxg4d2xz2fo.gif)

图:我将编辑器设置为每五秒钟检查一次文本。当你写作的时候，有一个连续的阅读时间估计也是很有趣的。希望它能鼓励简洁。

## 3。GROQ 给你 API 超能力ðÿš€

选择无头 CMS 的全部原因是内容可以通过 API 访问。通常这是 RESTful APIs，管理起来有点麻烦。虽然这通常是有意义，但是在这样的 API 上构建一个稍微高级一点的网站或者任何应用程序也可能是一件麻烦的事情。特别是当您需要多次查询 API 以获得必要的 id 来获得您想要的数据时。这也是脸书制造 GraphQL 的部分原因，graph QL 现在似乎是未来无头 CMSes 和“[后端即服务](https://graph.cool/)的首选武器。

GraphQL 还没有带来理智，但可以说是更好的东西: [GROQ](https://www.sanity.io/docs/front-ends/query-cheat-sheet) 。我看到它的第一个想法是，它感觉像“GraphQL 的降价”。语法也是你必须习惯的东西，但是一旦你习惯了:ðÿš€.如果你想查询我在上面的例子中写的文章，只需输入`*[_type == 'article']`就可以了，但是你也可以疯狂地使用它:

[![We use a version of this query to generate the topic list on U4\. It is way easier to write than to read: “Fetch documents with the type â€˜topics’ and return the title in addition to the number of documents of the types â€˜publication’ and ’helpdesk’ that references each â€˜topics’-document and place them under the key â€˜relatedCount’, sort this array after this count descending from the largest, and give only the five most referenced topics-document”.](img/8d477be66035f468d26c99990d586bd6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MP9AbbFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4yx6jgmjapxdqc2n1bqn.jpeg)

图:我们使用这个查询的一个版本在 U4 上生成主题列表。写起来比读起来容易得多:“获取“主题”类型的文档，返回标题以及引用每个“主题”文档的“出版物”和“帮助台”类型的文档数，并将它们放在关键字“relatedCount”下，在此计数后从最大值开始降序排列此数组，只给出五个引用最多的主题-文档”。

## 4。这项技术超级无聊——ðÿ'

尽管您在本地配置数据模式和 content studio，但 Sanity CMS 是云托管的，所有数据都是即时同步的，这意味着 studio 也是实时的。在终端中，让其他人可以使用您的 Content Studio 配置是一件简单的事情。

有了能够托管无数文档的承诺，令人欣慰的是，在理智之下，你会发现诸如 PostgreSQL、ElasticSearch 和 JavaScript 等成熟且超级无聊的技术(好吧，秘方中也有一些令人兴奋的技术，使理智如此之快)。我们还感到安全的是，React 不会很快消失。没有 HTML 或 XML 保存在数据库中，甚至没有富文本，一切都在合理的对象结构中:例如，你不希望为了让 Alexa 从你的文本字段中读取而必须解析 HTML。

Sanity 有自己维护和维护的 JavaScript、HTML 和 PHP 客户端，因此您也可以快速启动和运行任何您想要连接的前端。通过 API 编写和更新数据也很容易，即使是事务(是的，它有事务！).你希望这些东西无聊，尤其是当你的内容和数据对你很重要的时候。健全性是为了让您更难丢失或损坏数据而构建的。

在撰写本文时，托管和 SLA 的定价尚未公布，但我们非常肯定，无论是从费用还是功能上来说，这都是明智的。它还将用于学术领域[为在线研究出版物](https://vegapublish.com/)提供解决方案。

## 5。对每个人来说，使用无头内容模型终于变得容易了，ðÿ™‡"â™€ðÿ™‡

[![With the Craft by Invision plugin we can also use Sanity to provide real content when designing new interfaces.](img/a18e3388f335480212abd0da74df02ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KiPllJas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z5vdyqi15awjvfzobm72.gif)

图:有了 Craft by Invision 插件，我们也可以在设计新界面时使用 Sanity 来提供真实的内容。

为了开始使用 Sanity，你暂时依赖于对 JavaScript 有初步了解的人。然而，作为网络上最流行的编程语言，这样的人应该不难找到。我们真正喜欢这个 CMS 的地方是，它邀请了技术专家、视觉/交互设计师和内容编辑更紧密地合作，来创建一个对它的用途有意义的信息架构。

### 我们在热恋中无头无脑！Â ðŸ'š

能够在会议或研讨会期间调整和调整内容模型、输入字段甚至预览，只是为了测试这些建议，这是非常强大的。事实上，新的内容字段一旦创建，就可以通过 API 立即访问，这为前端人员消除了很多摩擦。我们以前从未能够如此轻松地构建一个信息架构，以便前端人员和内容编辑人员可以立即进行试验。

魔鬼也存在于所有的小细节中，这些细节太多了，在这篇文章中无法探究，但要强调其中的一些:理智分析你的图像的配色方案(对图像上的文字对比问题说再见吧！)，处理来自 Microsoft Word 的粘贴，让多个用户同时编辑相同的文档，用一个终端命令导出和导入整个数据集，有 CDNs，有预览 OpenGraph-tags 的插件，让您用一个简单的`sanity init plugin`向工作室编写自己的插件，让您在富文本中制作自己的标记和注释，让您要求您想要的任何大小、裁剪或旋转的图像。这个名单每周都在增加。

也从来没有这么容易定制的编辑器，这一切都发生在-它有助于新的和令人兴奋的冒险！

* * *

应该注意的是，我们与理智没有经济联系，除了用理智来帮助我们的客户，我们按小时收费。如果你有兴趣尝试一下理智，你可以通过阅读它的[文档](https://sanity.io/docs)来了解更多。我们相信 Sanity 的人们会很感激反馈，关于 [gitter](https://gitter.im/sanity-io/sanity?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) 或各种 [github-repos](https://github.com/sanity-io/sanity) 的问题。

如果你有兴趣与 Netlife 在一个理智的项目上合作，只需给我们发一封老式的电子邮件，我们会想出办法的！

[![Knit your content together with Sanity. Illustration from sanity.io](img/98a8d3839602208d714c6a67b05d83a6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EtBKm6FD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x29txax0z0vbm2v718es.jpeg)