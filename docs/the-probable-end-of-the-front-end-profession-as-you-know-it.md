# 你所知道的前端职业的(可能)终结

> 原文：<https://dev.to/diegoeis/the-probable-end-of-the-front-end-profession-as-you-know-it>

一个常规常见的 web 开发过程可以分为三大类:设计、前端和后端。在我看来，前端的人工和重复流程最多。

想一想，前端工作的核心概括起来就是两个部分:

*   布局实现:创建项目的第一个代码，我们复制在一些图形应用程序设计的布局，静态 HTML，CSS 和 JS 代码；
*   API 集成:在静态代码完成后(或同时)，使用 API 集成接口，这通常包含大量逻辑，因为相同的 API 用于支持其他平台，如手机、机器人、网络版等；

围绕前端的其他职责，如可访问性、SEO、性能、浏览器之间的兼容性、语义代码，以及其他您可能认为是前端职责的事情，仅仅是一个细节。它们可能存在于项目中，也可能不存在。但是，如果没有布局的前端代码，没有集成到界面中的内容，项目就无法生存。

如今，我们已经将大量重复的手动任务委托给工具来节省时间，实现了前端工作流程的自动化。仅举几个工具汤:

*   CSS 预处理程序:Sass，Less，手写笔
*   脚手架:约曼，泥浆
*   依赖项/模块包:鲍尔、NPM、Yarn、Webpack、Duo、RequireJS、Browserify、JSPM、汇总
*   SPA /库/框架:React、Angular、Vue.js、Backbone、EmberJS、todomvc、Polymer、Lodash、Aurelia、MeteorJS
*   CSS 框架/库:SemanticUI，Bootstrap，Foundation，UiKit，YUI，Susy
*   JS 测试:摩卡，茉莉，QUnit，艾娃，磁带，Jest
*   JS 模板:下划线，小胡子，车把，圆点，灰尘，EJS

但是，即使有了所有这些工具，前端的核心职责仍然是实现原始布局，并将接口与后端集成。你仍然继续复制别人花了几天时间设计和集成别人创建的 API 内容的布局。你的一天归结为在 Sublime/Sketch/Browser/Sublime/API/Browser/Sublime 的窗口之间切换。

> “自动化并不意味着懒惰。而是要有效率。—艾迪·奥斯马尼

这一过程变得单调乏味，并且对高效前端工作的要求只会增加。每一项机械的、重复的和手动的任务都趋向于自动化，在我看来，很快，我们将不再需要有人来运行端到端的前端工作。

好的，深呼吸。这是我的看法。由于前端是整个流程中最具操作性的部分，所以在前端完成的所有(或大量)工作迟早都会实现自动化，甚至这两个主要任务也是如此。然而，他们的日子可能已经屈指可数了。

### 处理直接来自设计的真实数据

你可能不是一名设计师，但在设计师的世界里有一个前提，那就是你应该总是与真实的内容一起工作。这意味着使用 Lorem Ipsum Dolor 交付带有文本的布局是初级设计师的水平。

> *“如果您的网站或应用程序需要数据输入，请输入真实相关的文字并键入文本，不要只是从其他来源粘贴。——杰森·弗里德*

这个想法是使用术语、单词、名称、日期等尽可能准确地创建一个布局，以便更接近用户体验。

目前，大多数用于创建网页布局的可视化程序都有一些功能或插件，允许集成一些包含真实内容的数据源。

例如，Sketch 是目前最受欢迎的视觉创作程序，它有允许 API 和布局直接集成的插件。例如，请参见下面演示 Craft 插件使用的视频:

观看使用 Stackoverflow API 的演示:

这里的要点是:我们只需要使用选择的工具创建一次布局，就这样。我们不需要一个人用 HTML、CSS 和 JS 手动重新实现这个布局。这就引出了第二个讨论:即使设计已经就绪，并且使用了来自 API 的真实数据，我们仍然需要浏览器能够访问它。

“在浏览器中设计”这个概念在这里有什么用？这个提议的过程实际上是为了避免两次产生相同布局的工作，但是使用可视化程序进行设计比直接在代码中编写要好得多。IMHO。

### 漂亮的代码并不重要

前端开发人员总是讨厌像 Dreamweaver 这样的工具生成的代码。他们这样做是有原因的:代码通常不可维护。那是一个互联网连接不稳定的时代，我们竭尽全力改善网站性能。所见即所得应用程序生成的代码有几个问题:难以人工阅读，语义差，包含无用的代码，并且经常与所有浏览器或屏幕分辨率不兼容。所有这些使我们重视干净、语义化、精益和可访问的代码。

web 中干净的代码成为了 Google 搜索中良好排名、良好的跨浏览器兼容性、负载性能、团队成员更高的生产力、更好的维护等等的同义词。

这些问题现在大部分都解决了:

*   浏览器更符合 web 标准，解决了大部分交叉兼容问题；
*   性能在几个方面得到了提高:资产的构建过程，像 HTTP/2 这样的技术，甚至互联网连接的发展；
*   HTML / CSS 代码的维护和可读性不再是一个主要问题，因为 HTML 很容易使用少量的语义标记编写，CSS 有预处理程序，这在设置标准和良好实践时很有帮助；
*   框架、库和一系列好的实践给 JS 提供了很好的建议，它们负责大部分的工作，几乎不给 dev 留下出错的空间；
*   对我来说最重要的是语义不再在 HTML 中。由于像 JSON-LD 这样的技术，语义不再与 HTML 代码捆绑在一起，这很好；

我知道，即使把所有的责任都交给框架、库和工具，开发人员也很有可能写出糟糕的代码。我们总是犯错误，或多或少，没有灵丹妙药。

试着理解一件事:在一个工具可以生成一段好代码的世界里，漂亮的代码不再那么重要了。今天帮助我们构建资产的工具可以被工具用来从 Sketch 等程序中产生的布局自动创建 HTML/CSS/JS 代码。见[这个插件例子](https://github.com/sskyy/blade&usg=ALkJrhgawsq8-A94001PU1OM3MjloN4fOw)从 2015 年开始就不再更新，已经在 Sketch 中尝试自动化代码导出。这个人试图从草图版本 3 中绘制的布局制作 HTML 代码。今天，Sketch 目前在版本 42 中，在版本 43 中，Sketch 以 JSON 格式开放其文件的代码。这就把我们带到了下一个话题。

### 设计自动化

> *“我们有了一种更紧凑的新文件格式，能够为第三方开发人员提供更强大的集成。——*[素描团队](https://rink.hockeyapp.net/apps/0172d48cceec171249a8d850fb16276b&usg=ALkJrhh6nxr61ShVR9GPU8ZaHF9qO3s7KQ)

素描将成为一个发展平台。打开你的文件的代码，任何人都可以阅读这些文件，并从那里创建任何东西。有人创建一个读取 JSON 格式的草图文件，从草图布局自动生成 HTML、CSS、JS 的插件需要多久？

> “计算机进化了。如果原则改变了，进化就没有基础了。—卡伊奥·瓦卡罗

好吧，但是等等:即使在这种新的草图文件格式之前，已经有一些你可能不知道的工具叫做[传送](https://protoship.io/tools/teleport.html&usg=ALkJrhiYpdL_B4SfTRoLx_8oKKZEdPYxvw)，它可以把任何网站变成一个草图画板。另外， [UIPad](https://protoship.io/tools/uipad.html&usg=ALkJrhg6Kf_fOVVeOsRn1zZqhGjmea_Hcw) 将草图布局转换为 HTML、CSS 和 React！看一看:

这个趋势很久以前就已经出现了。这是明智之举。你可以做比坐在电脑前切换浏览器、布局、浏览器、布局更重要的事情。

我们有一个与 API 集成的设计，在界面上拉真正的内容。我们有一个设计工具，可以将布局导出到 HTML、CSS 和 JS 代码中，随时可以使用，但我们仍然像以前一样使用代码。还有一步可以改进。

### web 汇编(Wasm)

另一个重要的点，还没有任何定义，但可以开始有意义。WebAssembly 是一种新的二进制(实际上是字节码)格式，由 Google、Microsoft、Mozilla 和其他一些公司创建。

WebAssembly 的代码格式解码速度比 JavaScript 解析速度快得多。这确实给网络带来了本地应用的体验，特别是在移动设备上。

酷的是可以为 WebAssembly 编译其他语言。今天，这个项目更加关注 C / C++，但是其他语言也肯定会包括在内。WebAssembly 的主要目标是性能。

空气中出现的一个担忧是，它带有垄断的味道。还记得闪电侠吗？不管他愿不愿意，这都是在网络上创造本土事物的一种替代方式。但优雅之处在于:

*   WebAssembly 不会取代 JavaScript。一切都具有向后兼容性，一切都将在与 JS 相同的宇宙中运行，安全性也将与 JS 有相同的限制；
*   Wasm 背后不仅仅是一家公司或集团，而是像 Firefox、Chromium、Edge、Webkit 这样的几家；
*   要运行 WebAssembly，您不需要运行第三方插件，因为浏览器引擎将完全兼容；

你明白为什么寻找一个干净的代码(正如我们今天所知道的)不再那么有意义了吗？你的网站/产品字节码将由一个类似草图的工具生成。

### 结论

这篇文章固执己见，充满了我自己的猜测。所有这些都可能成为现实，也可能不会。IMHO，我们所知道的前端职业迟早会不复存在。

我不知道这是否真的会发生，但直到昨天[自动汽车和卡车](https://www.wired.com/2016/10/ubers-self-driving-truck-makes-first-delivery-50000-beers/&usg=ALkJrhgdjY8F7ZGHSA8Lvy-ADN25TSA9pA)还只是电影里的东西。

### 进一步阅读

#### 设计中的实际数据

*   [使用 JSON 在草图中处理真实数据的 3 个简单步骤](https://www.shopify.com/partners/blog/91010886-3-easy-steps-for-working-with-realistic-data-in-sketch-using-json)
*   [用数据设计](https://medium.com/@markjenkins/designing-with-data-7f6bcd907f0a#.95haya5yq)
*   [在成帧器中使用真实数据的原型，从 JSON 到多设备和物联网](https://blog.framer.com/prototype-with-real-data-in-framer-from-json-to-multi-device-and-internet-of-things-6eb1ae8b8325#.fo9b8i4gz)
*   [Adobe XD:用真实数据进行设计](https://medium.com/@anirudhs/project-comet-designing-with-real-data-959beccb5c1a#.v6khfndrh)

#### 关于不在布局中使用假文本

*   [Lorem Ipsum 正在扼杀你的设计](https://www.smashingmagazine.com/2010/01/lorem-ipsum-killing-designs/)
*   [为什么设计师不应该使用虚假文字](https://thenextweb.com/dd/2015/04/09/why-designers-should-never-use-fake-text/#.tnw_zjSSHkxh)
*   [停止使用 Lorem Ipsum！](http://www.creativebloq.com/design/stop-using-lorem-ipsum-7116907)
*   [“获得真正的设计技巧:拒绝 Lorem Ipsum](https://signalvnoise.com/archives/001083.php)

#### web 组装

*   [FAQ 做网站组装](http://webassembly.org/docs/faq/)
*   [WebAssembly —一个 web compilada](https://tableless.com.br/o-webassembly-vem-ai/)
*   谷歌、微软、Mozilla 和其他公司联手推出 WebAssembly，这是一种新的 Web 二进制格式
*   [WebAssembly e o futuro da Web](https://jaydson.com/webassembly-e-o-futuro-da-web/)
*   [从 asm.js 到 webassembly](https://brendaneich.com/2015/06/from-asm-js-to-webassembly)
*   Web 正在获取它的字节码:WebAssembly

#### 关于前端工具场景

*   [前端目录](https://frontend.directory/)
*   [使用前端构建工具](http://radify.io/blog/using-build-tools/)
*   [最佳前端框架的集合](http://usablica.github.io/front-end-frameworks/compare.html)
*   [GitHub:前端 JavaScript 框架](https://github.com/showcases/front-end-javascript-frameworks?s=stars)
*   【2017 年前端工装趋势
*   [更新列表:67 款最佳前端 Web 开发工具](http://blog.debugme.eu/front-end-web-developer-tools/)
*   [最流行的 JavaScript 前端工具](https://techbeacon.com/most-popular-javascript-front-end-tools)
*   【2017 年改善和简化工作流程的十大 JavaScript 模板引擎
*   [自动化前端工作流程](https://speakerdeck.com/addyosmani/automating-front-end-workflow)
*   【2015 年美国国情咨文第三部分
*   [幻灯片—2015 年 Javascript 国情咨文](https://www.slideshare.net/Hugeinc/javascript-state-of-the-union-2015)
*   [前端工装状态 2016 —结果](https://ashleynolan.co.uk/blog/frontend-tooling-survey-2016-results)
*   [前端角色和职责](https://hackernoon.com/front-end-roles-and-responsibilities-6ee8654f1649#.gsg5zdjtr)