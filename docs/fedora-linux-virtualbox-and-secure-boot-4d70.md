# Fedora linux、VirtualBox 和安全引导

> 原文：<https://dev.to/victoroalvarez/fedora-linux-virtualbox-and-secure-boot-4d70>

今天在 Fedora 25 上安装并尝试运行 Virtual Box 后，我注意到由于安全引导会阻止未签名的内核模块运行，因此可以禁用它作为在 Fedora 25 上运行 VirtualBox 的简单解决方案。

[来自这个 fedora 论坛链接的两个解释:](http://forums.fedoraforum.org/showthread.php?t=289962)

> 好吧，这是行不通的，就像我上面说的…
> 
> 然后我看了看 bios，我看到安全启动是启用的(之前这台电脑上有 Windows 8…)以防万一，我禁用了它，然后它就工作了
> 
> 是的，如果 virtualbox 内核模块没有签名，安全引导可能会有一定的影响。这正是安全引导应该防止的。
> 
> 使用安全引导，您的内核和所有内核模块都必须签名。
> 
> 这就是事情变得棘手或令人痛苦的地方。
> 
> Fedora 用他们的密钥给你的内核签名。Fedora 不打算签署 virtualbox 驱动程序，特别是因为这违背了他们的自由/开源软件政策。Oracle 无法使用 Fedora 的密钥签署 virtualbox。
> 
> 所以，你只有两个选择。
> 
> 1:禁用安全引导
> 
> 或者
> 
> 2:自己拿钥匙，自己签字。