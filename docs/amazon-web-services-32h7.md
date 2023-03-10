# 亚马逊网络服务

> 原文：<https://dev.to/funkysi1701/amazon-web-services-32h7>

我是 Azure 的忠实粉丝，但我对其最大的竞争对手亚马逊网络服务(Amazon Web Services)或 AWS 一无所知。

因此，让我们注册一个免费试用，看看它能做什么。[![Amazon Web Services](img/415ef5032f2a4d320f77b273f079ebf9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---TYOxZ92--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pwnedpass.com/wp-content/uploads/2016/07/aws-300x169.png%3Fresize%3D300%252C169)

AWS 免费试用从[https://aws.amazon.com/free/](https://aws.amazon.com/free/)开始，为期 12 个月。根据记忆，我认为 Azure 免费试用只持续了一个月。

首先，你需要用你的亚马逊账户登录并创建一个 AWS 账户。这需要您的姓名和地址以及您的付款信息(只有在您使用免费试用不包含的服务时，才会向您收费)。

有趣的是，AWS 要求你通过自动电话来验证你的身份。(我不记得为 Azure 做过类似的事情，但如果我错了，请纠正我。)

登录后，您会看到一系列链接，显示所有可用的不同服务。第一印象是，这是 Azure 门户网站的一个更简单的视图，具有类似数量的服务。在右上角是一个选项，选择你想使用哪个地区，在 Azure 我用北欧和西欧，AWS 有爱尔兰和法兰克福。

## 创建一个 Web App

首先要做的是建立一个网站。我选择创建一个 web 应用程序，然后我得到一个页面，询问我它的基本细节(非常类似于 Azure，但是 AWS 询问你的代码是用什么语言编写的，Azure 处理所有这些)，AWS 网站似乎支持许多类似于 Azure 的不同选项。

网站的实际创建需要一些时间(比如在 Azure 上)。然而，网站的默认 URL 类似于 http://test.vjbbimyv7w.eu-central-1.elasticbeanstalk.com/的[和 http://test.azurewebsites.net 的](http://test.vjbbimyv7w.eu-central-1.elasticbeanstalk.com/)的 Azure

Azure 通过 powershell 提供了大量命令行。AWS 有一个类似的命令行界面，称为 AWS CLI，包括从 git 部署到您的网站的选项。

AWS Toolkit for Visual Studio 是一个扩展，允许将网站发布到 AWS。(就像你可以发布到 Azure 一样)

随着我对 AWS 的了解越来越多，我会继续写博客。[亚马逊网络服务 Pt 2](https://dev.to/funkysi1701/amazon-web-services-pt-2-33fi-temp-slug-7436081)

帖子[亚马逊网络服务](https://www.funkysi1701.com/2016/07/21/amazon-web-services/)最先出现在 [Funky Si 的科技谈话](https://www.funkysi1701.com)上。