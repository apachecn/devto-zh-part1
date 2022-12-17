# org manager:GitHub 组织的免费邀请系统

> 原文：<https://dev.to/m1guelpf/orgmanager-free-invite-system-for-your-githuborgs-5b04>

GitHub 组织是管理开源团队或组织你的品牌开源项目的绝佳方式，也是与来自世界各地的陌生人合作的绝佳方式。这个“开放 GitHub 组织”是每个人都可以加入的组织，如果他们要求加入的话。

我注意到 GitHub 组织缺少一个更好的邀请系统:你可以给一个组织添加人员，但是你不能设置一个“加入这个组织”按钮出现在组织页面的顶部。

这意味着未来的成员需要:

1.  请求邀请(大多数“开放组织”都有一个问题，你可以通过评论来获得邀请)。
2.  请等待，直到管理员看到您的消息并向您发送邀请。

这个过程可能需要一天到一周的时间。

当我发现这一点时，我正在学习 Laravel，所以我想我可以使用 Laravel 和 GitHub API 构建一个小应用程序，使这个过程更快更容易。结果被称为 OrgManager。

<figure>[![OrgManager: Free invite system for your GitHub Orgs](img/8929b3119c463b12b33fbbdb026aec1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S9pdvXnY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/orgmanager/branding/master/PNG/orgmanager_Horizontal.png)

<figcaption>org manager 标志</figcaption>

</figure>

对于您的组织，使用 OrgManager 很简单:转到[主页](https://orgmanager.miguelpiedrafita.com)并点击登录链接。然后，您将被重定向到 GitHub，在这里您需要授权 OrgManager 来管理您想要使用它的组织。然后，您将看到一个仪表板，在那里您可以获得与您的用户共享的链接，对页面进行密码保护，邀请用户加入 GitHub 组织团队，从 OrgManager 中同步或删除组织。

对你的用户来说，这很容易:他们打开你分享的链接，点击一个大按钮，他们就进来了！

想用吗？在 [OrgManager 托管版本](https://orgmanager.miguelpiedrafita.com)或[自托管版本](https://github.com/orgmanager/orgmanager#deployment)注册。

最后，你可以[在 Patreon](https://patreon.com/m1guelpiedrafita) 上支持我，帮助我维护这个项目并保持托管版本。