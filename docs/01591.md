# Matt 最喜欢的 Visual Studio 代码扩展

> 原文：<https://dev.to/mattstratton/matts-favorite-visual-studio-code-extensions-1cm4>

[![](img/9d06e2536d75af40ba5a59c9f34252f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OdtgLjW7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ASQm5ZwCZdYRLXwz9em5xLA.jpeg)

[回到 10 月](https://dev.to/mattstratton/on-trying-new-things-cel)，我决定要“强迫”自己使用 [Visual Studio 代码](https://code.visualstudio.com)作为我唯一的编辑器，而不是 [Atom](https://atom.io) (明确地说，我也继续使用 vim)。实验取得了令人振奋的成功；从他们之后我就没打开过 Atom。我发现 VS Code 的性能非常出色，特别是 Go 扩展让我非常高兴。

因此，我决定汇编一份我发现在我的旅程中最有帮助的 VS 代码扩展列表。请记住，这是基于 *my* 工作流和工具的扩展列表。如果你不像我一样研究同样的技术，你可能会觉得它们没用。

*   [括号对上色器](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer) —永远回答“这个括号是哪个左括号括起来的？”
*   [Chef 扩展](https://marketplace.visualstudio.com/items?itemName=Pendrica.Chef)——支持 [Chef](https://www.chef.io) DSL，加上一些令人敬畏的片段。甚至支持林挺烹饪风格。
*   [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)——因为没有 Docker 你就不能 DevOps。除了 Docker 命令的一些命令面板操作(老实说，我还没有尝试过)，您还可以突出显示 Dockerfile 和 docker-compose.yml 文件的语法。
*   [要点扩展](https://marketplace.visualstudio.com/items?itemName=kenhowardpdx.vscode-gist)——非常基本，但它确实有用。让从 VS 代码中创建和编辑 [GitHub Gists](https://gist.github.com) 变得很好很容易。[1]
*   [git 历史](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory) —检查文件的历史(即 Git 日志或单行的历史(即 Git 错误))。然而，对于责备，我更喜欢下面列出的 Git 镜头扩展。
*   Git Lens —我们开始吧…如果你在 VS 代码中做任何与 Git 相关的事情，这是非常必要的。我最喜欢的一个特性是，它突出显示了最近提交中更改的所有行。
*   GitHub —直接在 VS 代码中处理 Pull 请求。
*   非常简单。的语法突出显示。gitignore 文件。您甚至可以使用一个命令从 [github/gitignore](https://github.com/github/gitignore) 存储库中生成一个. gitignore。
*   [Go](https://marketplace.visualstudio.com/items?itemName=lukehoban.Go)——这差不多就是让我从 Atom 转换到 VS 代码的原因。我甚至不能开始列出所有的功能。如果你在 Go 中编码，没有这个扩展你不可能使用 VS 代码。现在就安装。我们会等的。
*   [CSS 类名的 Intellisense](https://marketplace.visualstudio.com/items?itemName=Zignd.html-css-class-completion)—如当前工作区(或链接的引用)中所定义的，提前键入您的自定义 CSS 类。没有它你不会知道你是怎么工作的。
*   我和我的同事[埃里克·西格勒](https://esigler.com/)最近都在哀叹没有一个代码编辑器有“打印”功能。是的，有时你仍然需要使用枯树。但通常情况下，你只需要把你的 [Markdown](https://daringfireball.net/projects/markdown/syntax) 做成 PDF 格式，与那些不阅读 Markdown 的人分享。这个分机做它在锡上所说的。
*   [Markdown 预览 Github 造型](https://marketplace.visualstudio.com/items?itemName=bierner.markdown-preview-github-styles)——承认吧。你用 VS 代码写的 99%的 Markdown 都是为了在 GitHub 上的 README 或类似文件中使用。这应用了 VS 代码的 Markdown 预览功能，看起来像是 [GitHub 的](https://github.github.com/gfm/)。
*   有林挺在你的拍卖场真好。这个扩展是令人敬畏的，同时也是我存在的祸根(我不断违反让我恼火的规则，比如 MD 001 Header-increment-Header levels 一次只能递增一级。幸运的是，您可以告诉 linter 忽略某些规则，无论是全局的，还是逐个项目的。(塔是付费产品；一个类似的非常流行的是 [GitKraken](https://www.gitkraken.com/) ，但是我还没有找到它的 VS 代码扩展)
*   虽然我知道很棒的人只在命令行使用 git，但我偶尔会发现使用 GUI 的价值。我发现的唯一有附加值的建筑之一是[塔](https://www.git-tower.com/)。这个扩展使得在那里打开项目变得很容易。
*   项目经理 -非常简单…只要创建一个常用项目的列表，你就可以得到一个很好的命令面板动作来浏览/打开它们。
*   REST 客户端 -它不是对[邮差](https://www.getpostman.com/)的替代，但是如果你想在你的代码中尝试一些 REST 调用，这非常有效。
*   给你的 SCSS 文件添加缩进格式。小事意义重大。
*   [同步](https://marketplace.visualstudio.com/items?itemName=nonoroazoro.syncing) -将你的设置备份到一个秘密的 GitHub gist(如果你喜欢，你可以使用一个公开的，尽管我更喜欢一个秘密的，这是默认的)。这有助于 a)保持您的工作空间在多台机器上同步，以及 b)当您获得一台新机器时，添加您的所有设置(包括扩展)。注意:这种同步不会自动发生；您需要运行命令。
*   [Travis CI 状态](https://marketplace.visualstudio.com/items?itemName=felixrieseberg.vsc-travis-ci-status) -在 [Travis](https://travis.ci) 中了解项目的最新状态。
*   [Waka time](https://marketplace.visualstudio.com/items?itemName=WakaTime.vscode-wakatime)——《程序员 Fitbit》。记录你在哪些项目上花费了时间。你可以在 wakatime.com/@mattstratton 的[看到我的。](https://wakatime.com/@mattstratton)

你发现过哪些超级牛逼的扩展？请在评论中告诉我！

[1]如果你的 GitHub 帐户使用 2FA，你需要设置一个个人访问令牌(只需要 gist 访问)，然后将它弹出到你的 VS 代码设置中，如下所示:" gist.oauthtoken": "XXXXXXXX "