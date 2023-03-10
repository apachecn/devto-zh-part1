# 重新思考电子邮件确认

> 原文：<https://dev.to/ruidfigueiredo/rethinking-email-confirmation>

我们非常习惯用户注册在网上的工作方式。输入您的电子邮件，可能选择一个用户名，输入密码，再次输入密码，收到一封确认电子邮件，单击该电子邮件中的链接，瞧，帐户创建完毕。

[![Image of an envelope with a tick](img/bd2f3d218c4eecdae9fdee2a2cca354e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6PW8aIp9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uh9t4k7ngpuvjst8x4t2.jpg)

然而，有一些事情可能会出错。

想象一下这个场景。您想在一个名为 TheWebsite 的假想网站上创建一个新帐户。你走完了所有的正常程序，但是你打错了你的电子邮件。你输入的不是 john.smith@email.com 的，而是 jon.smith@email.com 的。但也不全是坏事，你输入的用户名是正确的:johnsmith。

你等待确认邮件，但它没有到达。现在是下午 6 点，该回家了。你明天会检查它，那时它肯定已经到达了。

明天来了，你忘了没有确认邮箱，于是你尝试登录，johnsmith +密码。它起作用了，快乐的日子。

几年过去了，你一直愉快地使用这个网站。一切都很好，直到有一天尝试登录，你得到一个无效的登录错误。你再试一次，再次得到一个错误。

你认为这是不对的。我总是使用 johnsmith 和相同的密码。你点击重置密码链接，但没有重置电子邮件到达。

这是一个悲剧，你上传到网站上的所有照片和其他东西。你不能再访问它们了。

发生了什么事？

事实证明，真正拥有[jon.smith@email.com](mailto:jon.smith@email.com)的人是一个好奇的孩子，他点击了服务上的电子邮件，要求他验证他的电子邮件地址。

他自己也忘记了这一点，直到几年后他从一些朋友那里听说了这个网站，并决定尝试一下。他试图创建一个帐户，并得到一个“帐户已经存在”的错误。他使用了密码重置功能，仅此而已。他现在拥有原约翰·史密斯的账户。

虽然这个故事看起来很复杂，但它确实发生了。布洛克·艾伦在 NDC 谈论[ASP.NET 身份核心](https://vimeo.com/172009501)时描述了一个发生在他家人身上的情况。

那么能做些什么呢？

一个可能的解决方案是在点击电子邮件确认链接时再次要求输入密码。这样，乔恩·史密斯就无法确认约翰·史密斯的电子邮件，因为他不知道密码。

不过这也带来了一个新问题。如果在选择密码和收到确认邮件之间你忘记了密码怎么办？

好吧，然后再见能力创建一个帐户与该电子邮件。再见了，你选的用户名很酷，以后不能再用了。

没有希望了吗？

有。这里有一个激进的想法:不要让用户选择任何东西，直到她/他确认了电子邮件地址。

该过程将是:

*   点击注册
*   输入电子邮件
*   告诉用户检查电子邮件并点击电子邮件确认链接
*   用户点击电子邮件确认链接，进入一个页面
    *   用户名(如果需要，一些网站只需要电子邮件)
    *   密码
    *   重新键入密码
*   完成的

这就像是说，只有当你确认你拥有那个邮箱地址的时候，我才会和你说话。之后你可以告诉我你想要什么。

## 试图创建一个实际的实现

上周，我写了一篇关于如何从头开始创建一个能够使用电子邮件确认处理用户注册的 web 应用程序的指南。

在研究 ASP.NET 身份核心的时候，我看到了 NDC 和布洛克·艾伦的视频，并了解到了这个问题。

我决定尝试使用 ASP.NET 身份来实现这种方法，但不幸的是无法获得可靠的实现。

如果用户选择了无效的密码或已被使用的用户名，将会使用户的电子邮件帐户处于不稳定状态。

下面的片段演示了使用 ASP.NET 身份时的问题:

```
[HttpPost]
public async Task<IActionResult> VerifyEmail(string id, string token, string username, string password, string repassword)
{
    var user = await _userManager.FindByIdAsync(id);

    var confirmEmailResult = await _userManager.ConfirmEmailAsync(user, token);
    if (!confirmEmailResult.Succeeded)
        throw new InvalidOperationException("Invalid token");      

    var addPasswordResult = await _userManager.AddPasswordAsync(user, password);
    if (!addPasswordResult.Succeeded)
    {
        addPasswordResult.Errors.ToList().ForEach(error => ModelState.AddModelError(string.Empty, error.Description));
        return View(); //goes back to the form where the user chooses the username and password
    } 
    //... 
```

Enter fullscreen mode Exit fullscreen mode

如果用户输错了密码，电子邮件会得到确认，但用户没有设置密码(可以在 ASP.NET 身份中创建没有密码的用户)。用于确认电子邮件的令牌不能再次使用，因此用户将永远无法设置密码。

有人可能会认为，我们应该在设置用户密码后才确认电子邮件令牌，不幸的是，这不起作用，因为如果您更改用户的任何属性，令牌就会失效(与用户不匹配)。

此外，我们不能验证电子邮件，然后将用户重定向到另一个页面来选择用户名和密码。另一个页面可能会被另一方滥用，只需在用户之前导航到该页面就可以接管帐户。

因此，似乎没有可靠的方法可以用 ASP.NET 身份做到这一点，至少没有创建自定义的电子邮件确认令牌，如果我们设置了用户的密码，这些令牌仍然有效。

这个想法仍然是有效的，但是当我们试图这样做的时候，很难不感觉到我们在和框架做斗争。