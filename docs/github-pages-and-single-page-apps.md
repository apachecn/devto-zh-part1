# GitHub 页面和单页应用

> 原文：<https://dev.to/_evansalter/github-pages-and-single-page-apps>

*这篇文章最初出现在我的[个人博客](http://www.evansalter.com/blog/github-pages-and-single-page-apps)上。*

单页应用程序现在非常流行。它们使得构建功能丰富、高性能的 web 应用程序变得极其容易。所以你继续为自己建一个温泉浴场，但还有一个问题。你在哪里举办？

本文分为两部分。首先，概述一下我用过的不同的水疗托管方法，以及它们的优缺点。第二，我在 GitHub 页面上主持 SPA 的经历，包括我遇到的任何麻烦。我希望帮助读者做出明智的决定，在哪里托管他们的应用程序，如果他们选择使用我谈到的任何方法，从我的错误中吸取教训。

如果您希望专注于 GitHub 页面，跳过其他方法，您可以跳到第 4 部分:GitHub 页面。

#### 1。谷歌应用引擎

直到我开始目前的工作，我才真正进入 web 开发领域。是的，我知道一些 HTML 和 CSS，我还上过大学网页开发课。然而，直到我开始在工作场所使用这些技能，我才开始感兴趣。

在我的工作中，我们所有的核心应用程序都是使用 App Engine 构建的。我们使用 Python 版本，Jinja2 作为我们的模板语言，Knockout.js 作为前端代码。过了一段时间，我开始觉得在 App Engine 中开发非常舒服。这很熟悉，我已经可以相对快速和容易地组装一个新的应用程序了。

现在，回到温泉。2016 年夏天，我开始着手[卷收追踪器](https://www.rolluptracker.com/)。Roll Up Tracker 是一个 Angular2 网络应用程序，允许您在 Tim Hortons 的 Roll Up The Rim To Win 赛季期间跟踪您的输赢。自然地，当我开始这个项目时，我决定把它放在 App Engine 上。仅仅为了提供一些 HTML 和 Javascript，就必须启动整个应用引擎项目，这似乎有点奇怪。然而，我认为后端是用 Python 编写的，使用 Google Cloud Datastore 作为数据库。让前端和它一起托管是有意义的。

实现非常简单。

1.  为前端文件添加几条规则，并为您的`app.yaml`添加一个后端总括规则
2.  创建一个通配符路由到仅服务于`index.html`的处理程序
3.  在您的通配符之上创建您的 API 路由，以便它们首先匹配

```
# app.yaml
- url: /assets/(.*)
  static_files: dist/assets/\1
  upload: dist/assets/.*

- url: /(.*\.(js|map))$
  static_files: dist/\1
  upload: dist/(.*\.(js|map))

- url: .*
  script: main.app
  secure: always 
```

Enter fullscreen mode Exit fullscreen mode

```
class MainHandler(BaseHandler):

    def get(self, *args, **kwargs):
        context = {}
        self.response.write(template.render(os.path.join(TEMPLATE_DIR, 'index.html'), context)) 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样！继续第二种方法...

#### 2。谷歌应用引擎 Flex

Oooooh“应用引擎灵活”！听起来又新又亮又完美又棒。一开始是的。

我用 Vue.js 建立了一个非常小的 SPA，名为[scrobbbling ' With Friends](https://scrobblin-friends.appspot.com/)，试图将 Last.fm 带回到我的生活中。这是一个简单的应用程序，允许你输入你的 Last.fm 用户名，它会显示你所有的朋友都在听什么。这是一个很小的应用程序，完全没有后端。它所做的就是接收一个用户名，每 10 秒钟调用一次 API，然后显示一些数据。引导一个应用引擎项目肯定是没有意义的。

然后我发现了 Flex，发现他们有 Node.js 环境。它非常容易安装:

1.  `app.yaml`有 2 行:`runtime: nodejs`和`env: flex`
2.  `package.json`用一个`start`脚本
3.  一个简单的快速服务器

```
// server.js
var express = require('express');
var app = express();

const PORT = process.env.PORT || 8080;

app.get('/static/*', function(request, response){
     console.log('static file request : ' + request.params[0]);
     response.sendFile( __dirname + '/static/' + request.params[0]);
});

app.get('*', function(request, response) {
    response.sendFile(__dirname + '/index.html');
});

app.listen(PORT); 
```

Enter fullscreen mode Exit fullscreen mode

我很快完成了设置，部署了它，生活是美好的。直到我收到这个月的账单。

你会注意到标准应用引擎环境和 Flex 环境之间的一个很大的区别，那就是你的项目永远不会完全停止。标准来说，如果没有人点击你的网站，你不需要付费。即使你有一点点的流量，他们对免费层还是很慷慨的。据我所知，“自由层”不是 Flex 上的东西。在我注意到之前的几天里，我积累了大约 30 美元。

如果你有一个愿意花大价钱来主持的项目，我认为 Flex 是一个可行的选择。这不是那些项目中的一个。所以回到应用引擎标准我去了。

#### 3。现在

如果你现在还没听说过，现在就去看看吧(呵)。真的很酷。在你的电脑上安装好之后，只需在命令行上输入`now`，接下来你知道的就是，你的应用程序已经在网络上了。

唯一的要求是你有一个带脚本的 T0。`now`其他的就交给他了。

尽管部署过程很酷，但还是有一些我不喜欢的地方。

1.  免费层为您的项目提供这些 janky URLs，例如:coolproject-glkqdjsslm.now.sh。当我不支付托管费用时，我通常不介意有奇怪的 URL，但问题是每次部署时，您都会获得一个新的 URL。你基本上不能托管任何其他人会免费使用的东西
2.  他们最便宜的付费层是每月 1000 次部署(而不是 20 次)，私有代码库等，每月 14.99 美元。比我愿意为愚蠢的副业付出的要多得多。

#### 4。github pages-github 页面

最后。你坐在我的水疗中心主持经历的痛苦细节中。我们终于到达了文章的要点，那就是概述我在 GitHub 页面上主持 SPA 的经历。

这似乎是提供水疗服务的合理地点，对吧？你可以免费托管。部署轻而易举。免费 SSL。另外，你的代码很可能会保存在 GitHub 上！

我最近决定是时候更换我的个人网站了，它以前是一个免费的 Wordpress 网站，我没有投入任何精力(最终我用密码保护了它，因为我对此感到尴尬)。我的新网站，[http://evansalter.com](http://evansalter.com)，是用 Vue.js 写的，没有后台，所以我真的可以选择任何我想要的提供商。app Engine(Standard 和 Flex)都是多余的和/或过于昂贵的，`now`也是不可行的，因为我上面提到的原因。所以我们在这里。GitHub Pages 是被选中的。

它并非没有缺点。当然，这里或那里有一些您可能需要解决的怪癖，但是我没有遇到任何阻碍，我相信您也不会遇到。也就是说，如果 GitHub Pages 是一项付费服务，我遇到的一些问题可能会让我放弃并转向其他服务。因为它是免费的，我愿意花时间来解决这些问题。

我想介绍一下整个事情是如何工作的，包括我在这个过程中遇到的任何“陷阱”。

### 第 1 部分:什么是 GitHub Pages？

首先，[到这里](https://pages.github.com/)。它拥有 Pages 的所有基础知识，如果您想要更详细的文稿，您可以点按右上角的“Pages 帮助”。

有两种类型的页面。第一个是组织/个人页面。它位于一个名为`<YOUR_USERNAME>.github.io`的 repo 中，有一个与 repo 名称匹配的 URL，是一个个人或组织的通用登录页面(duh)。该站点的代码存在于该项目中。

另一种是项目页面，通常位于`gh-pages`分支中，或者位于`master`的`/docs`文件夹中。然后，您从`<YOUR_USERNAME>.github.io/<PROJECT_NAME>`访问页面。它允许您将所有代码保存在一个地方。我觉得很方便。

为了这篇文章的目的，我将谈论个人页面。

### 第二部分:我的回购应该是什么样的？

这是我遇到的第一个问题。个人网站由`master`分支提供服务。这意味着我不能仅仅使用 Vue CLI 创建一个项目，将一切都交给 master，然后期望它能正常工作。

在网上做了一些调查后，我选定了一个计划。我创建了一个存放源文件的`sources`分支。然后，在我的电脑上，我构建了项目，将生成的`dist`目录初始化为 git repo，将其设置为跟踪`master`，然后提交和推送。它工作得很好，但主要是手动的。此外，如果我曾经重新克隆项目或使用另一台计算机，我必须再次设置 dist repo。烦人。

### 第 3 部分:自动化部署

我不打算满足于上面列出的过程，所以我想我可以设计一个脚本来完成这个任务，然后设置 Travis CI 来运行它。互联网上流传着相当多的脚本正是这样做的。当我发现 Travis 实际上有一个 GitHub Pages 部署提供程序时，我使用了一段时间，对它进行了调整，使之适用于我的项目。几次提交之后，我用一个非常简单的`.travis.yml`结束了，它为我处理了一切。

```
language: node_js
node_js:
  - "node"
script:
  - yarn run lint
  - yarn run build
deploy:
  provider: pages
  skip_cleanup: true
  github_token: $GITHUB_TOKEN
  local_dir: dist
  target_branch: master
  on:
    branch: sources 
```

Enter fullscreen mode Exit fullscreen mode

它基本上只是运行我的 lint 和 build，然后如果分支是`sources`，强制将`dist`目录推到`master`。欲了解更多信息，请查看 [Travis CI 文档](https://docs.travis-ci.com/user/deployment/pages/)。

### 第四部分:路由

有一段时间非常顺利。我使用 webpack dev 服务器在本地非常快速地开发，完成了热模块重载。当我想部署时，我只需推送我的更改，大约一分钟后它们就上线了。我设置 Vue 路由器一点问题都没有。

事实证明，当我使用 live 站点时，我总是从站点的根目录开始。我从未试图直接进入`/projects`或任何其他页面。这时候我遇到了阻碍。试图在我的应用程序中直接进入一条路线，结果出现了 404 页面。

当我发现这个的时候，我一点也不震惊。这完全有道理，因为 GitHub 不知道 Vue 应用程序的路线。它只知道在回购中有一个`index.html`文件，它可以路由到这个文件。

我尝试了两种不同的方法来解决这个问题。根据您的要求，一种可能比另一种更合适。

#### 企图 1:黑掉 404 页面。

我在网上找到了另一个人，他正试图解决和我一样的问题。你可以在这里看到他的博文[。我承认这是一个很简单的解决问题的方法，但是也很容易实现。在 GitHub 提出更好的解决方案之前，这是一个相当不错的解决方案。](http://www.backalleycoder.com/2016/05/13/sghpa-the-single-page-app-hack-for-github-pages/)

这个解决方案是基于这样一个事实:你可以在 GH 页面上使用一个自定义的 404 页面。你所要做的就是给你的回购添加一个`404.html`页面。按照上面的链接，我在`<head>`中用下面几行创建了`404.html`:

```
<script>
  sessionStorage.redirect = location.href;
</script>
<meta http-equiv="refresh" content="0;URL='/'"></meta> 
```

Enter fullscreen mode Exit fullscreen mode

基本上，所有这些都是将请求的 URL 保存在浏览器的存储器中，并引导您回到站点的根目录。显然，这还不够。如果有人点击这篇博文的链接，我不希望他们被发送到主页。下一步同样简单。我基本上给`index.html`添加了另一个脚本来检查保存的 URL，如果它存在的话就重定向到它。

```
<script>
  (function(){
    var redirect = sessionStorage.redirect;
    delete sessionStorage.redirect;
    if (redirect && redirect != location.href) {
      history.replaceState(null, null, redirect);
    }
  })();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这种方法出奇的有效。然而，一旦我决定在我的博客文章中实现 Open Graph 和 Twitter 元标签，它就崩溃了，因为抓取器没有遵循重定向。然而，如果你有一个不需要 meta 标签的简单网站，那么这可能对你有用。

#### 尝试 2:预渲染静态站点

就提供元标签而言，上述路由器攻击有两个问题:

1.  当脸书、Slack、Twitter 或任何其他拥有富媒体卡的网站扫描我的链接时，它们会像预期的那样出现在 404 页面上。然而，通过我上面分享的方法，它们不会被重定向到正确的页面。这是因为 GitHub Pages 为 404 页面返回了一个 404 状态代码，它告诉抓取器放弃。
2.  即使我没有使用 404 页面黑客，我的元标签也是在我的 Vue 应用程序中动态生成的。因为 scraper 下载页面时没有执行任何 javascript，所以它找不到 meta 标签。

因此，两个显而易见的解决方案是服务器端渲染或预渲染。我不想走服务器端渲染的道路，因为我不想为这个网站托管服务器。所以，预渲染吧！

因为我正在使用 Webpack，所以我认为添加一个 webpack 插件来从我的站点生成静态 HTML 是有意义的。我找到了几个预建的插件来做这件事，它们都利用幻象来渲染站点。出于某种原因，我无法让它在我的网站上呈现任何实际内容。我浪费了大量时间试图让它工作，但没有成功。

对此感到沮丧，我决定写自己的插件。于是，[web pack-static-site-generator](https://github.com/esalter-va/webpack-static-site-generator)诞生了(很棒的名字，我知道)。有了这个插件，每次我为生产建立我的站点时，我都会为我的所有页面获得静态 HTML 文件。在本地开发时，我仍然可以使用 Webpack dev 服务器，而无需构建，这是一个巨大的优势。

因此，如果你在任何主机提供商上托管你的 SPA 有任何问题，你可能想要检查预渲染。

### 第五部分:自定义域

如果你已经做到了这一步，你应该已经完成了在 GH 页面上托管单页面应用程序的“陷阱”。建立一个自定义域非常容易。只有几个步骤:

1.  在 repo 中创建一个名为`CNAME`的文件，其中包含一行您想要使用的域名(即`www.evansalter.com`)。确保这个文件在构建运行时提交到您的主分支。
2.  与您的域名注册商建立一个指向您的`github.io`地址(即`esalter-va.github.io`)的`CNAME`记录

如果你想使用一个 apex 域(没有 www)，GitHub 的支持页面上有一些稍微不同的说明，但应该不会太麻烦。

我发现了一个小问题。当你使用带有 GH 页面的自定义域时，没有 SSL 支持。我对此有点恼火，但鉴于我的网站没有理由放在 HTTPS，我就释怀了。

### 最后的想法

当我第一次启动这个网站时，我对使用 GitHub Pages 托管单页应用程序感到非常兴奋。然而，它并不像看起来那么神奇。一路上我遇到了很多问题。其中一些有简单的解决方案，而另一些则稍微复杂一些。然而，我仍然对最终的结果感到高兴。页面的简单性是它的伟大之处，我建议每个人至少在他们的下一个项目中考虑它。