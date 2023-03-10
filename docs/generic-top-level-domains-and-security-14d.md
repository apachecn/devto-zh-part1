# 通用顶级域名和安全性

> 原文：<https://dev.to/svijaykoushik/generic-top-level-domains-and-security-14d>

**读者注意:**这篇文章早在 2017 年 5 月就写好了。我没有时间提前发表它。请记住，这是基于 2017 年 2 月 20 日发生的事件。

尽管我们诅咒与储蓄银行账户相关的政策变化，但印度最大的公共部门银行印度国家银行(State Bank Of India)给了其网站一个新的头像，以保护其客户免受网络钓鱼欺诈。

是的，我说的是最近关于用自己的最高域名协议将网站从`https://www.sbi.co.in`更名为`https://www.bank.sbi`【gTLD】的新闻。SBI 是第一家拥有自己的 gTLD 的银行。

什么是 <abbr title="generic Top Level Domain">gTLD</abbr> ？它与防范网络钓鱼有什么关系？嗯，gTLD 是一个完全合格的域名的最后一个标签。完全合格的域名是类似于`www.google.com`或`www.wikipedia.org`的网站的完整地址。标签<q>。com</q> 和<q>。org</q> 是顶级域名。出于历史原因，这些顶级域名被称为通用域名，以区分互联网发展过程中存在的不同域名。但是等一下！如果他们被称为顶级域名，那么为什么他们在最后？原因是，域名系统或 <abbr title="Domain Name System">DNS</abbr> 简而言之，负责识别某个特定域名对应的正确网站的目录服务将域名从右到左按层次顺序组织起来。因此，最右边的标签位于层次结构的顶部，最左边的标签位于层次结构的底部。

[![Illustration of DNS hierarchy](img/a44c96f820954b8e76ff13c6b6ba7719.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bU8jrJYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/on4zwsf33sqc3rduku4i.jpg) 带通用顶级域名的 DNS 层级图

创建通用顶级域名是为了代表特定的目的，如商业实体的 *com* ，网络基础设施的 *net* ，教育用途的 *edu* 。但是，由于缺乏限制，这些实体 *com、net 和 org* 现在可以用于任何目的，尽管它们最初有特定的目标，因此这些<abbr title="Top Level Domains">TLD</abbr>被指定为*无限制*。还有其他<abbr title="Top Level Domains">顶级域名</abbr>，如 *biz、name 和 pro* 被认为是通用的，但被指定为受限的，因为向它们注册需要*资格证明*。

*sbi* <abbr title="Top Level Domain">TLD</abbr> 也被指定为受限。这意味着不允许其他个人或组织注册 sbi 域名。那么，这如何保护我们这些客户免受网络钓鱼欺诈呢？要了解这一点，我先给大家讲讲网络钓鱼。根据维基百科:

> 网络钓鱼是指试图获取敏感信息，如用户名、密码和信用卡详细信息(以及间接的金钱)，通常是出于恶意，通过伪装成电子通信中值得信任的实体来实现。传统的网络钓鱼涉及骗子发送电子邮件，通知用户已经在彩票中赢得了 100 亿美元，该用户甚至从未听说过，并要求用户向特定账户发送规定的金额作为处理费，以利用奖金，如果他/她转移所述处理费，用户将永远不会收到该奖金。也就是说，骗子用伪装成来自银行的官方邮件的假邮件来引诱互联网用户，出于安全目的或任何其他目的询问用户的凭证，以说服用户点击邮件中的链接。当点击这些链接时，会打开一个设计与银行网站一模一样的欺诈网站。这些欺诈网站会在用户提交凭据时捕获用户凭据，并将其保存在骗子的计算机中，他/她用这些凭据来窃取用户的金钱。

第一个骗局可以通过将发件人的邮件地址添加到用户的电子邮件服务提供商的垃圾邮件列表中来避免(即使像 Google 和 yahoo 这样的电子邮件服务提供商提供自动垃圾邮件功能，一些垃圾邮件也逃脱了他们的垃圾邮件过滤器)。第二种可以通过验证发件人的邮件地址是否是银行的官方地址(发件人的地址可能拼写错误)来避免。以及通过查看浏览器地址栏上的地址，验证是否是银行官网(检查拼写错误)。检查可疑网站的另一种可能的方法是自愿提供错误的凭证。如果网站不响应错误，那么它是一个欺诈网站。

由于 gTLD *sbi* 仅限于 sbi，人们可以很容易地区分欺诈网站和原始网站和电子邮件，因为官方电子邮件地址和网站将以*T5】结尾。SBIT7】。到目前为止，只有银行的网站上有这个新的 gTLD，银行官员已经宣布，SBI 的其他业务，如保险，也将有自己的 gTLD。官员们还表示，只要客户习惯了新的网址`www.bank.sbi`，银行以前的网址`www.sbi.co.in`将继续存在。*

### gtld 参考消息

com

Mainly for commercial entities, but unrestricted

org

Originally for organizations not clearly falling within the other gTLDs, now unrestricted

edu

Educational use, but now primarily for US third level colleges and universities

gov

Governmental use, but now primarily for US governmental entities and agencies

mil

Military use, but now primarily for US military only

[来自维基百科的 gtld](https://en.wikipedia.org/wiki/List_of_Internet_top-level_domains#ICANN-era_generic_top-level_domains)的完整列表。

### 词汇

Address bar

[A text box in a web browser displaying the address of the web page that is currently being viewed.](https://en.wikipedia.org/wiki/Address_bar)

Email Service Provider

[a company that offers email services.](https://en.wikipedia.org/wiki/Spamming)

DNS

[Domain Name System](https://en.wikipedia.org/wiki/Domain_Name_System)

gTLD

[Generic Top Level Domain](https://en.wikipedia.org/wiki/Generic_top-level_domain)

Phishing

[the fraudulent practice of sending emails purporting to be from reputable companies in order to induce individuals to reveal personal information, such as passwords and credit card numbers.](https://en.wikipedia.org/wiki/Phishing)

Spam

[irrelevant or unsolicited messages sent over the Internet, typically to a large number of users, for the purposes of advertising, phishing, spreading malware, etc.](https://en.wikipedia.org/wiki/Spamming)

Website address or Web address

[an Internet or intranet name that points to to a location where a file, directory or website page is hosted.](https://en.wikipedia.org/wiki/URL)

随机引用

> 让我们不要在愤怒中回顾过去，也不要在恐惧中展望未来，而是要有所觉察。——<cite>詹姆斯·瑟伯</cite>

插图由[sophiedogg.com](http://sophiedogg.com/)绘制。