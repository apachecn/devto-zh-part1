# 游戏方面的项目 2 -只需要一个框架

> 原文：<https://dev.to/sethrah/game-side-project-2---just-go-with-a-framework>

所以在我上一篇与游戏相关的帖子中，我提到了我正试图为 C++制作一个基础游戏模板，这样我就可以直接投入并尝试制作游戏原型，而不必做任何琐碎的设置。

我认为我在这方面取得了很大进步。我的第一个目标只是制作一个闪屏，它可以转到一个菜单屏幕，可以转到另一个状态屏幕或者退出游戏。我相信我已经把我需要的一切都编码出来了*除了*我仍然需要实际渲染屏幕。

我开始尝试寻找一些好的图形库的资源，但最终打开了一个完整的罐子或蠕虫(过时的库，几乎没有文档的库，需要在用 C++编译器编译之前运行 python 来编译的库，等等)。我不再停滞不前，不再写任何东西，而只是一小步一小步地研究什么是最好的库。

然后我有了一个想法，就是去看看我最近最喜欢的独立游戏都用了什么(到目前为止我最喜欢的是 [Stardew Valley](https://en.wikipedia.org/wiki/Stardew_Valley) )。原来它在 C#上用的是 XNA！虽然我喜欢回顾和回忆 C++，但 C#是我的经济支柱(我在办公室工作中使用它)*和*我工作的一个额外好处是我可以接触到许多供个人使用的微软产品，所以我想我应该研究一下整个 XNA 的事情

> ...根据 2013 年 1 月 31 日发送的一封电子邮件，XNA 不再被积极开发

等等，仅仅因为它没有被积极开发，并不意味着它不再作为项目模板包含在 Visual Studios 中，对吗？`Loads up VS 2017``...``Goes to game section``...`T4】

嗯...让我们在 XNA 维基百科页面继续阅读

> 替代实现...一个叫做 Mono 的项目。XNA 的形成是为了将 XNA 移植到开源和跨平台的 Mono 框架。来自 Mono 的代码库。XNA 和 SilverSprite 之后，一个名为一夫一妻制的新项目成立了，它将 XNA 移植到几个移动设备上....对 iOS、Android、Mac OSX、Linux、Metro for Windows 8、Windows RT、Windows Phone 8、Playstation Mobile 和 Raspberry Pi 的支持是稳定的

对一夫一妻制的调查显示 [Bastion](https://en.wikipedia.org/wiki/Bastion_(video_game)) 是在它上面制作的(另一个最近的最爱)，所以我想我应该看看他们的网站。原来有一个关于一夫一妻制的 pluralsight 课程(这是我从工作中获得的 MSDN 订阅的免费课程)，所以我开始学习。课程链接是[这里](https://app.pluralsight.com/library/courses/monogame/table-of-contents)如果任何人有一个 pluralsight 帐户，并希望研究它。该课程包括制作 Pong 的克隆体。我现在基本上完成了，完成后会发布。我可能不会张贴源代码(只是因为我不确定遵循课程适用哪种许可)。

我想我可能会克隆其他更小的游戏(也许是扫雷),并发布源代码，直到我很好地掌握我正在做的事情。到那时，我可能会自己开始一个真正的项目。