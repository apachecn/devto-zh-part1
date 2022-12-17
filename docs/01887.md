# 我建立了一个 Alexa 技能…你也可以！

> 原文：<https://dev.to/bengreenberg/i-built-an-alexa-skill-and-you-can-too-2pf>

自从大约一年前我们第一次把亚马逊 Echo 带进我们的家，它就成了我们小孩持续娱乐的来源。我们孩子的最新幽默来源是让 Alexa 用 google 乘以 google，然后听 Alexa 背诵给他们的所有零。我们通过 Echo 查看天气、玩危险游戏、控制家里的灯等等。作为一名新的软件开发人员，我也想开发一个 Alexa 技能有一段时间了，这个周末我终于做到了。这样做既简单又直观。其实你也可以！

[![](img/eb2f4cb01d8007f123440aa6764969fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--saI0gR-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/450/1%2Ai4r47PDHxhpIj8tTP4hifA.gif)

我最初的灵感来自 Will Velida 写的一篇关于 [*实用开发*](https://dev.to/willvelida/building-my-first-alexa-skill--rugby-facts-doc) 的文章，他记录了他发布第一个 Alexa 技能的步骤。在我读了那篇文章后，我知道我也能做到。第一个技能是事实技能，这意味着用户将请求一个事实，该技能将返回一个事实。为什么从那里开始？这是一个相对简单的功能实现，并给出了一个很好的 Alexa 技能发展的介绍。

我会创造什么样的事实技能？作为一名拉比和软件开发人员，选择是显而易见的。我会创造一个托拉事实技能！这项技能将有一个关于托拉的事实列表，用户可以通过说“ *Alexa，请托拉事实告诉我一个事实【T1””来提示它，它会背诵其中一个事实。*

[![](img/148eea0146ff9b66dc395d6bce4a1520.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--giNmE07M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/480/1%2AjhFFEAK_1SBpvEsIp7hLXg.gif)

要开始学习你的新技能，只需前往 [Alexa 技能工具包教程](https://developer.amazon.com/alexa-skills-kit/tutorials)，在那里你会找到一步一步的指导。设置您的 Alexa 技能需要一个 Amazon 开发者帐户来注册您的应用程序，并需要一个 Amazon Web Services (AWS)帐户来托管您的应用程序。教程中包含了创建这两者的说明。亚马逊网络服务有一个免费层，假设你的技能没有达到亚马逊技能的前十名，应该足够了。

你实际的 Alexa 技能是一个 Node.js 应用程序，它利用了 [alexa-sdk](https://www.npmjs.com/package/alexa-sdk) 节点包。要开始使用 alexa-sdk 包，你应该这样做:

```
const Alexa = require('alexa-sdk'); 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可能希望建立一个数据结构来存放您的事实列表。在我的例子中，我的数据是这样的:

```
const languageStrings = {
    'en': {
      translation: {
       FACTS: [
        'fact 1',
        'fact 2',
        'fact 3'
      ]
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，您可以使用一种语言的标准缩写，用多种语言创建数据。对于这项技能，我们只使用英语，所以我需要的是 en 缩写。

每次用户提示输入一个事实时，我们都希望调用一个随机事实，所以我们希望创建一个函数来获取一个随机索引号，我们可以使用这个索引号从我们的数组:

```
const factArr = this.t('FACTS'); 
// this.t() localizes the strings in the array

const factIndex = Math.floor(Math.random() * factArr.length); 
// get a random number within the size of our array

const randomFact = factArr[factIndex]; 
```

Enter fullscreen mode Exit fullscreen mode

其余的功能利用 alexa-sdk 包中内置的监听器和发射器来解释 alexa 的用户提示，并将发声的事实返回给用户。这方面的文档做得非常好，可以在 Github [库](https://github.com/alexa/alexa-skills-kit-sdk-for-nodejs)上找到。

完成后，您的技能将处于开发模式，可以在与您的帐户相关的 Echo 设备上进行测试。您也可以邀请其他用户在开发模式下进行试验。一旦一切正常，你感到满意，你可以提交给亚马逊批准。从确认我的提交到技能在亚马逊技能列表中上线，这个过程花了一天时间。

[![](img/75766732dc889f7758c37bdbc97687f8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D_J4xwP9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/300/1%2ATVQukJUB-Wxd7ibe0Sfv3w.png)

总的来说，这是向我介绍 Alexa 应用程序开发的一个很好的方式。这是一个值得度过的下午。现在，当我的孩子玩我们的 Echo 时，他们不仅要求 Alexa 告诉他们 google 乘以 google 的值是多少，而且他们还要求 Alexa 告诉他们一个 Torah 事实，没有什么比当你的孩子认为你做的事情很酷更令人满意的了。

*托拉事实*可以在[亚马逊](https://www.amazon.com/dp/B077ZDHCZL/)上找到。