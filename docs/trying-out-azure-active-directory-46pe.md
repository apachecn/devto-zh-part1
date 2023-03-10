# 试用 Azure 活动目录

> 原文：<https://dev.to/funkysi1701/trying-out-azure-active-directory-46pe>

我的计划之一是为我的公司数据库创建新的 MVC Webapps。一旦我发布了这些，我将需要保护他们，所以只有工作人员有权访问。

传统方法是将成员资格表插入我的数据库。然后，用户必须记住另一个用户名和密码，我必须保护这些凭证的存储。每个人都有很多工作要做。

[![](img/8ffc5cd0a931e3e68398619fd973f5a7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LoNSPPgZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2016/03/arch.png%3Fw%3D600%26ssl%3D1)

有一种更好的方法是使用 Azure Active Directory。你可能听说过活动目录，如果你是一个系统管理员，你可能一直使用它来管理你的公司用户和计算机。Azure Active Directory 是这种技术在云端的延伸。

我过去写过关于使用 Azure 的博客，但这是我第一次尝试将我的内部域连接到 Azure。有一个虚拟实验室帮助我尝试这些想法。

我做的第一件事是在 Azure 中创建新目录。我通过旧的门户(manage.windowsazure.com)做到了这一点，通过新的门户也许是可能的，但我还不知道怎么做。

单击新建，选择应用服务>活动目录>目录，然后选择自定义创建。选择创建新目录，给它一个名称和域名，并从下拉列表中选择一个地区。然后为该目录添加一个全局管理员。

有一个工具叫做[Azure Active Directory Connect](https://www.microsoft.com/en-us/download/details.aspx?id=47594)。下载并在您的一个域控制器上安装此快速设置。您需要指定一个域管理员帐户来访问您的域和您刚刚创建的 Azure 全局管理员帐户。

此时我去睡觉了，所以我不确定同步所有的域信息需要多长时间，但是到了早上，这些信息都显示在 Azure 的用户列表中了。

我所有的用户账户都显示为@contoso.onmicrosoft.com，可以使用自定义域名，但我还没想好该怎么做。我在我的 Active Directory 中做了一个改变，不久之后，这个改变就出现在 Azure AD 中。

那现在怎么办？打开 Visual Studio，看看能不能用 Azure 认证。

我选择创建一个新的 MVC web 项目，并单击了 change authenticate 选项。其中一个选项是工作和学校帐户，然后我选择了云单一组织，并进入 contoso.onmicrosoft.com。然后我运行这个应用程序，它使用我的域密码通过 Azure 认证。真的对这有多简单印象深刻。

该应用程序随后出现在旧门户网站 Azure 上。在应用程序中，您可以看到哪些用户可以访问您的应用程序，并配置一些其他应用程序相关的设置。

这离在我的实际应用程序中有用还有很长的路要走，但它表明我试图做的基本工作确实有效。有人用 Azure AD 做过类似的事情吗？你进展如何？