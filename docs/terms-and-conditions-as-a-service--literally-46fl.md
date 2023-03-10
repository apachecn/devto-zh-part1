# 博客文章:条款和条件即服务——字面意思

> 原文：<https://dev.to/jonasbn/terms-and-conditions-as-a-service--literally-46fl>

前段时间我把头衔改成了产品经理。多年来，我一直是一名开发人员，后来成为一个开发团队的团队领导，所以这是一个有趣的变化。

作为团队领导，我慢慢地从实际的日常编码中解脱出来，做越来越多与人力资源相关的任务和会议。所以当有人建议我在软件开发中扮演更积极的角色时，我接受了。我在新职位上面临的唯一要求是:

–*利用您的知识和技能持续支持我们的软件服务和产品。*

我对这个新角色有点不自在，也许主要是这个头衔。作为一名长时间的开发人员，很难摆脱技术人员。我建议“技术产品经理”,但遭到了拒绝——我克服了这一点，这真的无关紧要——毕竟这只是一个头衔

仍然担心这会让我远离编码，我决定尝试塑造我的新角色以更好地适应我。我工作的组织以前从未有过产品经理，所以我想我不妨试着概述一下我自己的角色。

我首先审视了一个想法，这个想法我已经考虑了一段时间，但是还没有实施。作为一名产品经理，我认为创建原型来评估我们服务组合的可能候选人是完全合法的。

这个想法是为了处理“条款和条件”的问题领域以及这些问题的交流。问题区域可以用以下方式描述:

1.  条款和条件必须以可保存的格式提供(我不是法律专家，所以我不知道确切的措辞，但这是向我解释的方式)
2.  条款和条件必须在最初呈现给用户的修订/版本中对最终用户可用

此外，以下是更基本的要求:

1.  我们希望能够链接到当前的条款和条件，以便您可以通过网站找到它们
2.  我们希望能够链接到特定的修订，所以我们可以为网站创建链接
3.  我们希望能够通过电子邮件沟通的条款和条件，没有发送完整的条款和条件，但只是提供一个链接
4.  我们想同时支持丹麦语和英语

我煮了一个原型服务来处理这些需求，这个原型可以在 GitHub 和 T2 docker hub 上找到。

该解决方案提供了以下优势:

–条款和条件可以下载为 PDF 格式，这已被接受为可保存的格式

–您可以链接到准确的版本，例如

的构建列表–您可以链接到日期参数， 这将为您提供与给定日期

相关的修订版–您可以链接到该服务并获取条款和条件的当前修订版

–您可以通过使用丹麦语的“da”和英语的“en”来指向 URL 中文档的给定翻译

让我们浏览一下:

–在任何现代 web 开发框架中，提供 PDF 文件作为资产都非常容易

–基于日期的查询:

/en/terms _ and _ conditions/2002 06 11

返回在指定日期有效的条款和条件。这可以在电子邮件中使用，例如，您可以在邮件中加盖当前日期。

–基于修订的查询:

/en/terms _ and _ conditions/revision/2

返回当前条款和条件修订版 2。这可以用于枚举和列表或者特定的*深度*链接。

–基本查询:

/en/terms _ and _ conditions

返回当前条款和条件，这些条款和条件可用于您想要显示当前修订以供请求者评估的网页。

–基本查询，支持另一种语言:

/da/terms _ and _ conditions

以丹麦语返回当前条款和条件，可以通过指定:en 而不是 da 将其更改为英语。

所有可用的文档都是服务的资产，这些可以从数据库或类似的地方获取，在原型中它们只是公开可用的文件。

原型解决了上述问题，并向我们概述了面向公众的部分，即服务和功能部分，但在许多方面还可以改进，特别是在内部方面。

–如果文档不再是当前版本，您可以对其进行注释。我的建议是注释实际的 PDF，或者浏览器中的演示可以解决这个问题。当前的原型不能处理这个问题。

–处理不同时区的问题可能相当复杂，我的建议是确定一个时区为权威时区

–分辨率的算法可以优化

–后端中条款和条件的表示可以移动到数据库中

–日期参数是一个弱点，参数处理也可以改进，同时，我们希望用我们已经知道的日期格式标记 URL，产生一个查询

原型甚至拥有一个额外的功能，由于中央算法的工作方式，你实际上可以提前添加一个资产。在开始日期过去之前，它不会作为条款和条件的当前修订版。这意味着没有人必须在新年前夕发布 1 号可用的条款和条件的新修订版。一月的。

这些当然可以根据修订进行检索。这种处理是可以实现的，但我实际上认为这是一件好事，因为这意味着你可以测试应用程序，而不用跳过太多的关卡。

我以前从来没有大规模地使用过原型，但是使用*我的无聊堆栈*，它实际上很快就可以让一些东西工作起来，它会揭示 UX 和内部实现的有趣方面，比如主算法，最后它提供了概念证明，这可能会激发新的想法。

成为一名产品经理很难，但这并不意味着你必须从编码中脱离出来。原型制作很有趣，而且这肯定不是我最后一次用这种方式处理问题。

*1 头衔的改变可能会适得其反*自从我在 Linkedin 上更改了头衔，我就收到了很多与产品经理相关的邮件*