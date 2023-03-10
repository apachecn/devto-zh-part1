# “无法联系本地安全机构”——远程桌面

> 原文：<https://dev.to/alexhyettdev/the-local-security-authority-cannot-be-contacted-remote-desktop-2ej9>

最近，我不得不从以前的快照中恢复一些虚拟机服务器。不幸的是，域控制器快照是在不同的时间拍摄的。

当我用域管理员帐户远程登录每台机器时，一切看起来都很好。直到其中一个返回以下错误:

> 发生了身份验证错误。无法联系本地安全机构。
> 
> 这可能是由于密码过期。如果您的密码已过期，请更新。如需帮助，请联系您的管理员或技术支持。

## 无法联系本地安全机构。这可能是由于密码过期。

该错误表明该帐户的密码可能已过期。通过谷歌搜索，显然可以使用本地管理员帐户登录并重置密码。在我的情况下，我不能远程登录到本地帐户，仍然得到上面同样的错误。

但是，可以从 VMWare 控制台登录并访问机器。考虑到我可以登录到其他机器上，我有点不愿意重置域管理员密码。当使用域管理员帐户本地登录时，我得到了以下错误:

> 这个工作站和主域之间的信任关系失败。

那么是什么导致了这个问题呢？每隔 30 天，与域控制器共享的机器私有秘密就会改变。不幸的是，我的私人秘密在两台机器上拍摄快照的日期之间发生了变化。

微软对此的[解决方案是将计算机从域控制器中移除并重新连接，但这并不总是可行的，而且有点笨拙。](https://support.microsoft.com/en-us/kb/2771040)

要解决这个问题，您需要以本地管理员身份本地登录，并在 PowerShell 中运行以下内容。

```
$credential = Get-Credential 
```

Enter fullscreen mode Exit fullscreen mode

此时，系统会提示您输入帐户凭据。为此，您应该使用域管理员帐户。然后运行以下命令:

```
Reset-ComputerMachinePassword -Credential $credential -Server dc.dev.local 
```

Enter fullscreen mode Exit fullscreen mode

服务器应该是域控制器的主机名。

如果出现此错误，可能是因为您使用的是 PowerShell 2，而不是不支持凭据参数的 3。在这种情况下，只需运行这个命令，就会提示您输入凭证。

```
Reset-ComputerMachinePassword -Server dc.dev.local 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该能够再次以域管理员的身份登录。

我自己没有试过，但是其他人也用下面的命令成功了:

```
Test-ComputerSecureChannel -Repair -Credential $credential 
```

Enter fullscreen mode Exit fullscreen mode

如果这让你摆脱了困境，请在下面留下评论。