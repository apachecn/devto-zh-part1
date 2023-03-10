# 不能等待 Windows:柏树

> 原文：<https://dev.to/antonfrattaroli/cant-wait-for-windows-cypress>

几年前，当我第一次看到 Cypress 时，我看了创作者的演示视频，差点喜极而泣。我注册了早期访问，但从未被接受。我曾经在他们的团队聊天中打扰过他们。我点击了 github 项目上的手表按钮，等待着——关注着这个特殊的问题: [Windows 支持？](https://github.com/cypress-io/cypress/issues/74)

有些还在等 Windows 支持，但是我不能再等了。幸运的是，github 问题中有一个面包屑:当有人试图在 Windows 上运行 Cypress 时，错误消息应该会显示:`You can use Cypress if you install a Linux VM using something like VirtualBox.`

# 虚拟盒

没听说过。我的虚拟机一直是 VMWare。谷歌搜索。 [下载。](https://www.virtualbox.org/wiki/Downloads)安装完毕。

很简单。有一个下拉菜单可以选择我的操作系统。我最后一次使用 Linux 是在 2009 年，当时 Windows Vista 发布了，每个人都讨厌它，Ubuntu 是 Linux 的救世主。当时我试了一下，只记得没有适用于 Ubuntu 的 Visual Studio，于是我放弃了任何转换的努力。Ubuntu 在操作系统的 VirtualBox 列表中，我选择了它。

# Ubuntu

...`FATAL: Could not read from the boot medium.`

你是说我得自己装 Ubuntu？软弱。为什么是下拉菜单？随便啦。谷歌搜索。 [下载。](https://www.ubuntu.com/download/desktop)装上。

...`This kernel requires an x86-64 CPU, but only detects an i686 CPU, unable to boot.`

我不知道你在探测什么，但它不是 i686。谷歌搜索。哦，我必须创建一个新的虚拟机，并从下拉列表中选择 64 位 Ubuntu...没有 64 位选项。[谷歌搜索。](http://www.fixedbyvonnie.com/2014/11/virtualbox-showing-32-bit-guest-versions-64-bit-host-os/#.WZzSMem2z6Q)哦，我需要更改我的 BIOS 设置来启用“虚拟化技术”，没问题。

安装了 Ubuntu。好吧，现在我们谈了！这些办公软件是什么？这就是为什么下载 ISO 需要很长时间的原因吗？尽快处理掉桌面垃圾。现在是时候遵循 [Cypress 安装指南了。](https://docs.cypress.io/guides/getting-started/installing-cypress.html)

我需要安装节点。[Google 了一下。](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-16-04) `sudo apt-get update`

哦，不，不能上网。[Google 了一下。](https://supportforums.cisco.com/t5/vpn/anyconnect-3-1-04072-won-t-allow-internet-connectivity-from/td-p/2400378)我必须更改 Ubuntu 的设置，以便与主机的 Cisco AnyConnect VPN 配合使用。令我困惑的是，它必须在虚拟机上运行，而不是在物理机上运行，或者在 VirtualBox Manager 中运行。还是那句话，管他呢。我们走吧。

# 节点和 NPM

`sudo apt-get update`
T1】

虽然在 Cypress 文档中没有提到，但是他们使用了 npm 命令，我知道这是独立的——虽然我没有使用过它，但是我一定是潜移默化地学会了它。

`sudo apt-get install npm`

太棒了。我们去柏树山吧。

~~`npm install -g cypress-cli`~~

错误。无法连接。否则我可以浏览互联网...它能在我的物理机器上工作吗？哦，不，registry.npmjs.org 被“互联网通信”类别下的企业代理屏蔽了。首先，这个类别没有意义，其次，我对 npm 被屏蔽感到愤怒。这就像告诉成千上万的人，他们的集体工作不值得信任。向 NetSec 申请支持票...畅通无阻。

~~`npm install -g cypress-cli`~~

没有。那没用。需要安装 nodejs-legacy。

`sudo apt-get install nodejs-legacy`
T1】

~~`cypress install`~~

没用。需要 git。

`sudo apt-get install git`
`cypress install`
`cypress open`

万岁。我可以用 GitHub 登录吗？酷，我也有一个。安装 Cypress 文档，现在开始[编写你的第一个测试](https://docs.cypress.io/guides/getting-started/writing-your-first-test.html)...“打开你最喜欢的 IDE”。哎哟，2009 回来打我的脸。在某个时候，我会将它签入源代码控制，所以最好确保我有支持 TFS 的东西。[谷歌搜索。](http://blogs.microsoft.co.il/morgen/2010/06/07/using-tfs-in-linux-ubuntu/)感谢你 2010 年的博客为我 2009 年的问题提供的解决方案。

# 月食

谷歌搜索。看来我得先要 Java 8。向前一步，向后两步。

`sudo add-apt-repository ppa:webupd8team/java`
`sudo apt-get update`
T2】

明白了。[下载 Eclipse。](https://www.eclipse.org/downloads/)提取。双击一个图标进行安装——如果是 Windows 就好了，但此时我希望使用命令行。

团队浏览器无处不在...谷歌搜索。我读到也有一个 IntelliJ 的插件，我还读到很多人更喜欢 IntelliJ 而不是 Eclipse。现在太晚了，我想要我的自动化测试。

下载源...

# 柏树

添加文件夹...新赛普拉斯项目...复制/粘贴/删除示例测试文件。在半小时内，我测试了使用 OAuth 登录到一个站点:

```
 describe('Log On Test', function() {

    it('Logs On [www.mysite.com](http://www.mysite.com)', function() {

      cy.visit('[https://www.mysite.com'](https://www.mysite.com'))

      cy.get('.landing-login-panel .mdl-card__supporting-text:last a').contains('Log In').click()

      if (cy.url('host') === 'login.domain.com') {

        cy.get('#domain-id').type('username')

        cy.get('#password').type('********')

        cy.get('.submit').click()

      }

```
 cy.wait(2000)
  cy.get('.user-nav-menu').click()
  cy.get('[for="UIMenu-99"]').should('be.visible')
  cy.get('[for="UIMenu-99"] a[href="/MyProfile"]').click()
}) 
```

    Enter fullscreen mode 

    Exit fullscreen mode 

}) 
```

Enter fullscreen mode Exit fullscreen mode

# 
  
满意度

勇往直前，不要让障碍挡在你的路上。这是我的一项技能。这当然是一个漫长的旅程，但这是一个值得花的早晨。

也许一旦我重新创建了回归测试套件，我会将它集成到 CI 工作流中。我相信 QA 和 devops 会喜欢的。

总有一天，Windows 支持会来到 Cypress，我会把它强加给我的同事。