# 教程:在 ASP.NET MVC 项目中轻松使用 Azure CDN

> 原文：<https://dev.to/seankilleen/tutorial-easily-using-azure-cdn-with-aspnet-mvc-projects-448l>

读者们，六月快乐！我最近帮助设计了一个 AlliesForACure.com，一个慈善竞赛的网站。我们有一些静态内容(图像等)。)并希望在保持低成本的同时保持页面快速加载。

这听起来像是 Azure CDN 的工作。但是我以前从来没有用过它，所以我想这是一个用它写博客的好机会。

### 在本文中，我们将:

*   讨论 CDN 的优点
*   设置 Azure 存储帐户并添加一些文件
*   在存储帐户前设置 Azure CDN
*   为存储帐户添加子域
*   添加一个`HtmlHelper`，这将使参考 CDN 内容变得更加容易
*   启用 CDN 版本控制，并更新我们的助手以包含它。

# 等一下 CDN 业务是怎么回事？

## CDN 的作用

简而言之，所有的东西都在你的服务器上——你的网站的图像、视频、声音文件、样式表等等。–所有这些都必须提供给用户。我们经常忽略的是，那些文件必须从服务器一直传输到用户的 PC，这需要时间和带宽。如果你的服务器在纽约，而你的用户在加利福尼亚，文件就必须一直传到那里。

cdn 将您的文件分发到全球各地，并且非常擅长缓存它们。因此，如果您的服务器在纽约，而您的用户在加利福尼亚，当他们访问图像的 URL 时，它实际上可能会从更近的地方提供给，从而节省宝贵的时间。

## 为什么是 CDNs？为什么选择 Azure CDN？

为什么要用 CDN？因为它可以大大减少用户的加载时间，而且在大多数情况下，它会增加你自己提供这些文件的价格的价值。

为什么要用 Azure CDN？在这种情况下，只是因为我想摆弄它。有许多可信的替代方案。在我们的例子中，我们的站点已经使用了 Azure，所以我想为什么不呢？

# 第一步:想好要服务的内容

cdn 通常涉及“容器”的概念，一种一级文件夹结构。Azure 也不例外。所以，我想考虑我想提供的不同类型的内容。这可以归结为:

*   网站图像(徽标、图标等。)
*   团队成员的头像
*   我们在活动中使用的照片

# 设置存储组件

## 先决条件

我假设你有一个 Azure 帐户，并且以前使用过这个门户。如果没有，这并不难做到——我对你有信心。如果你有问题，请在评论中告诉我。

## 创建 Azure 存储帐户

*   从 [Azure 管理站点](http://manage.windowsazure.com) <sup id="fnref:1">[1](#fn:1)</sup> ，按照路径快速创建一个存储帐户:

[![](img/ba30b4e8ea8b4881a4278381cfd8544e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NqoCteL5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_01_NewStorageAccount.png)

*   输入您的详细信息，提供名称、地区和复制方式。**注意**:我们选择`Locally Redundant`是因为它将确保数据保存在我们地区的 3 个地方。我们不需要跨洲的数据可靠性，因为我们的受众在美国东部。

[![](img/874b9215c69f53eadc080faac12bf1df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cyUMNDl0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_02_StorageAccountSetup.png)

## 获取您的访问密钥

我们在这里的时候，会拿到你的钥匙。稍后连接到您的帐户时，您将需要此信息。

*   创建存储帐户后，单击它
*   在屏幕底部，点击`Manage Access Keys`:

[![](img/a67e5abfbd4c4d7f3cb90d6487472d3b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ibxz50Sl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_03a_ManageAccessKeys.png)

*   单击主访问密钥旁边的复制图标:

[![](img/f486e2d367b1252e6814d6b2c1c1b26f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kMVe5dz6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_03b_AccessKeyInfo.png)

*   暂时贴在某个地方。你一会儿就会用到它。

## 创建您的容器并添加您的文件

有很多方法可以将文件导入 Microsoft Azure。在这个练习中，我们将使用 [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) ，因为它是一个 GUI 工具，可以快速完成工作。

### 安装 Azure Storage Explorer

*   将您的浏览器指向 [Azure Storage Explorer 下载页面](https://azurestorageexplorer.codeplex.com/releases/view/125870)并下载最新版本
*   解压缩文件
*   运行安装程序。

### 将您的存储帐户添加到 Azure Storage Explorer

*   运行 Azure 存储浏览器程序。
*   点击屏幕顶部的`Add Account`:

[![](img/26b013822e1a2293abec236c24c1193e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AvG8aJ1M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_07a_AddAccount.png)

*   输入您的存储帐户名称(您在 Azure 中为存储指定的名称)，以及您之前复制的存储帐户密钥:

[![](img/8ee796ca296e810119906bce6d7c9665.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--36ESq4C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_07b_StorageExplorerSetup.png)

### 创建容器

*   在左侧，点击`New`按钮创建一个新的容器:

[![](img/4f567ea2d6e987ea7c6f4161120805f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cX9ftFHu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_08a_NewContainer.png)

*   输入容器的名称以及您希望它是私有的还是公共的——出于 CDN 的目的，您通常需要它是公共的。 <sup id="fnref:2">[2](#fn:2)</sup>

[![](img/168f0b4ab790817e6cf47107601dddeb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--eqezJa26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_08b_CreateContainer.png)

### 添加文件

*   点击刚刚创建的容器，选择`Upload`:

[![](img/1779551e8005e3c3e1634e335dcfe5c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZH1DJclY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_09_UploadButton.png)

*   选择多个文件进行上传——在本例中，是我们 awesome 团队的头像:

[![](img/f7cb4daf20eca206a153e2085dda0b43.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--prtgiqqW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_10_SelectMultipleFilesForUpload.png)

一旦这些文件被上传，你就一切就绪了！

# 在存储帐户上创建 CDN

*   回到 Azure 存储门户，选择`New --> App Services --> CDN --> Quick Create`。
*   选择您的订阅，`Storage Accounts`作为源类型，并选择源的 URL 作为您之前创建的存储帐户。

[![](img/da2a930f2f60b94508e56fc199b52faf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dXGtWSAD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_04_CDNSetup.png)

*   Azure 将用一个随机的名字创建你的 CDN，类似于我们的名字`az766003`。

## 通过 CDN 访问您的文件

瞧啊。随着 CDN 的创建，我们现在可以访问我们的文件了。为此，我们来看看 Azure CDN URL 是如何构造的。

`http://[CDN Endpoint].vo.msecnd.net/[Container]/[ImageName.extension]`

*   **CDN 端点**是你的 CDN 的端点，例如`az766003`。
*   **容器**是你的容器的名称，例如`headshots`。
*   **imagename . extension**是图像，比如`image.png`

因此，如果我们想获得我们的头像容器中的图像，我们可以键入`http://az766003.vo.msecnd.net/headshots/image.png`。<sup id="fnref:3">[3](#fn:3)T4】</sup>

但是说实话，**那个长长的网址很恶心，感觉不像我们的域名**。如果能更有家的感觉，比如说`cdn.alliesforacure.com`，不是很好吗？

是啊。就这么办吧。

# 设置一个指向你 CDN 的子域

在这个项目中，我们使用 [NameCheap](http://namecheap.com) 作为我们的注册商——我是他们的忠实粉丝。

## 向您的 DNS 提供商添加子域

*   从我们的主域列表中打开域设置。
*   点击“所有主机记录”。 <sup id="fnref:4">[4](#fn:4)</sup>
*   添加一个子域`cdn`，CNAME 指向`[your cdn name].vo.msecnd.net`，如`az766003.vo.msecnd.net`。

[![](img/e546e323e369e6736173799972d0b83e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mjCQLLAP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_05_SubDomain.png)

*   保存设置。

## 将子域添加到 Azure CDN

*   在管理门户中，进入您的 CDN 的屏幕。
*   点击`Manage Domains`:

[![](img/86d7abe2755f79e77244f992b5cd86b8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9SR5ZKOo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_05b_ManageDomains.png)

*   在框中输入域名，例如`cdn.alliesforacure.com`，等待绿色指示灯亮起，然后单击复选标记完成该过程。 <sup id="fnref:5">[5](#fn:5)</sup>

[![](img/949c78b929963539b9287182c08eaacc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mVSjGMII--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_06_EnterDomainInAzure.png)

## 当前设置的潜在问题

即使有了这个不错的新子域名，也可能会有一些问题:

*   如果你决定改变子域，或者你移动到不同的 CDN 点，URL 的开头可能会改变。
*   blob 名称或文件名也是如此。
*   如果这些网址到处都是，改变它们可能是一个巨大的麻烦，或者充其量是一个查找和替换操作。

有了 MVC 的好东西，应该不难做出一些东西来帮助管理这种复杂性。

# 让它更简单:CDN 内容的 html 助手

我知道，我知道——让我们开始写代码吧！我们可以在 MVC 中使用 HtmlHelpers 来实现这一点。

我想为某人添加使用`Html.CDN("cdnUrl", "containerName", "fileName.png")`来指定从哪里加载 CDN 内容的能力。

更好的是，让我们添加一个重载-如果用户没有指定 CDN URL，我们将在预定义的应用程序设置中查找它。

这一小段代码应该可以正常工作:

```
public static class CDNHelper
{
    public static string CDN(this HtmlHelper helper, string containerName, string blobName)
    {
        //If they don't provide a baseUrl, look for it in the AppSettings
        var baseUrl = ConfigurationManager.AppSettings["CDNBaseUrl"];
        return CDN(helper, baseUrl, containerName, blobName);
    }

    public static string CDN(this HtmlHelper helper, string baseUrl, string containerName, string blobName)
    {
        var baseUrlToUse = RemoveTrailingSlashFrom(baseUrl);
        var containerToUse = RemoveTrailingSlashFrom(containerName);

        return string.Format("{0}/{1}/{2}", baseUrlToUse, containerToUse, blobName);
    }

    private static string RemoveTrailingSlashFrom(string stringToRemoveTrailingSlashFrom)
    {
        if (stringToRemoveTrailingSlashFrom.EndsWith("/"))
        {
            return stringToRemoveTrailingSlashFrom.Remove(stringToRemoveTrailingSlashFrom.Length - 1);
        }

        return stringToRemoveTrailingSlashFrom;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 这段代码到底是干什么的？

这是对`HtmlHelper`的扩展方法。当您调用它时，它会在`CDNBaseUrl`应用程序设置中查找 CDN。

然后，它会删除 baseUrl 和 containerName 中的任何单个尾随斜杠，以防您将它们放在那里。

之后，它重新组装正确的 URL，这就是你使用这个 HtmlHelper 的地方。

现在，我们可以使用`@Html.CDN("headshots", "image.png")`，而不必键入`http://az766003.vo.msecnd.net/headshots/image.png`。好多了。

## 我改了文件但是文件没变！

如你所想，cdn 非常擅长缓存东西，这样它们就可以把它们分发到任何地方。但是有时候，你会改变一些东西——比如一个标志，编辑一个图片或者 css 文件，等等。-你真的希望这能成功。

别担心，我们也为你准备了一些东西。

# 向 CDN 添加“版本控制”

对 CDN 进行版本控制的方法是在 url 的末尾附加一个 QueryString。CDN 认为`image.png`和`image.png?v1`不一样。这意味着，如果你能在最后自动生成这一点，你就能在任何需要的时候更新你的 CDN 内容。

## 通过 Azure 启用 QueryString 版本控制

*   在 Azure 管理门户中，转到您的 CDN 部分。
*   通过底部的按钮启用查询字符串:

[![](img/07f6a9297385e0e70509ae9caa36672f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nCcg2y3k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://SeanKilleen.cimg/post-images/AzureCDN_11_EnableQueryString.png)

这就是你需要做的。

## 更新我们的 HtmlHelper 以支持 CDN 版本控制

我们为`versioning`添加一个布尔值并传递它，默认为 true:

```
public static string CDN(this HtmlHelper helper, string containerName, string blobName, bool versioning = true)
{
    //If they don't provide a baseUrl, look for it in the AppSettings
    var baseUrl = ConfigurationManager.AppSettings["CDNBaseUrl"];
    return CDN(helper, baseUrl, containerName, blobName, versioning);
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们连接 URL:
时，我们添加了一个分支语句

```
public static string CDN(this HtmlHelper helper, string baseUrl, string containerName, string blobName, bool versioning = true)
{
    var baseUrlToUse = RemoveTrailingSlashFrom(baseUrl);
    var containerToUse = RemoveTrailingSlashFrom(containerName);

    if (versioning)
    {
        var versionNumber = ConfigurationManager.AppSettings["CDNVersionNumber"];
        return string.Format("{0}/{1}/{2}?v{3}", baseUrlToUse, containerToUse, blobName, versionNumber);
    }

    return string.Format("{0}/{1}/{2}", baseUrlToUse, containerToUse, blobName);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 在项目中添加配置设置

我们可能应该指定一个版本号，这样我们就有一个了。我们将以下内容添加到`web.config`中的`appSettings`部分:

```
<add key="CDNVersionNumber" value="1"/> 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以从 web.config 更改该设置，或者在 Azure 控制面板中覆盖它，静态内容每次都会刷新。我们甚至可以将它与一个构建系统挂钩，每次都会增加它，以确保当我们推送代码时 CDN 内容得到更新。

# 一些注意事项

*   cdn 长时间缓存内容。你可以使用 Azure CDN QueryStrings 来解决这个问题，但是要小心，因为这有点违背初衷。通常情况下，querystrings 与类似于站点版本的东西一起使用，例如`myImage.jpg?v=1.2.3`。
*   别忘了检查 css 文件等。对于像内嵌图像这样的东西的 URL。一开始我没有注意到这一点，也不明白为什么某些东西没有显示出来。只是短暂的“哦！”。

# 就这么定了！

我希望你觉得这很有用。如果你有，请考虑在[http://AlliesForACure.com](http://AlliesForACure.com)捐款。

此外，我很想听听你的想法——在评论中发表吧！

我们使用管理网站，而不是[闪亮的新门户](http://portal.azure.com)，因为在撰写本文时，cdn 还不被支持。我决定保持一致。↩

现在我想起来了，有可能有私有存储 blobs，但仍然通过 CDN 公开它们。我不知道这是否可能，但如果我错了，一定要让我知道。↩

顺便说一下，容器和 blobs 的 URL 是区分大小写的。`image.png`被认为不同于`Image.png`。一开始我就被它咬了一口。↩

这些可能有不同的名称，但它们是域的 DNS 设置。↩

此时您可能会看到一个错误，说没有条目。有时 DNS 设置需要一段时间来传播，您可能会在几分钟内看到一个错误。仔细检查以确保你保存了所有的东西，过一会儿你就可以开始了。根据我的经验，这需要 5-15 分钟。↩

[教程:在 ASP.NET MVC 项目中轻松使用 Azure CDN](https://SeanKilleen.com/2015/06/A-Quick-Guide-To-Azure-CDN/)最初由 Sean Killeen 于 2015 年 6 月 1 日在 SeanKilleen.com[发表。](https://SeanKilleen.com)