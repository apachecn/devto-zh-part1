# WordPress 的未来聚焦于安全性

> 原文：<https://dev.to/tnolte/wordpress-with-a-future-focused-on-security-1eel>

作为 WordPress 社区的一员，在过去的 10 多年里，我见证了 WordPress 的成长，也看到了对平台安全性的持续担忧。作为一名技术专家，我看到了安全领域从事后的想法变成了第一个被问到的问题。然而，考虑到最近由 WordPress 插件维护者在 4.5 年的过程中发现的可疑活动，WordPress 核心团队和社区都需要更加关注安全性。

> [9 个 WordPress 插件在协调的 4.5 年垃圾邮件运动中被锁定](https://www.wordfence.com/blog/2017/09/coordinated-plugin-spam/)

鉴于我目前正在开发一个新的插件，而且我刚刚听了一个最近的 [WP-Tonic](https://www.wp-tonic.com/) 播客讨论这个主题，我不禁开始思考 WordPress 安全的未来。

> [233 WP-Tonic 圆桌显示“WordPress 钩子、动作和过滤器”](https://www.wp-tonic.com/podcast/233-wp-tonic-round-table-show-wordpress-hooks-actions-filters/)

我的想法很快进入了开发运维以及持续集成和部署的世界。DevOps 的主要部分之一是自动化测试。我可以很容易地预见未来，如果没有测试套件的支持，所有插件和主题都不会被回购接受。运行自动化测试，并查看这些测试的代码覆盖率，可能会发现一些安全问题。我认为通读测试套件有助于评审团队理解开发人员的意图。此外，恶意尝试似乎也更容易被抓住。使用代码覆盖率这样的东西甚至可以帮助按照插件和主题的“质量”来分类。当然，编写测试套件似乎是一个更大的技术障碍，但是在今天的开发环境中，我认为自动化测试不再是可选的。

我还看到 [WP Tavern](https://wptavern.com) 的文章，WP-CLI 正在寻求将他们的校验和安全措施扩展到插件和主题。

> 新的 WP-CLI 项目旨在将校验和验证扩展到插件和主题

虽然这是开源软件世界中广泛使用的东西，但有许多人，如 [Security Now](https://www.grc.com/securitynow.htm) 的 Steve Gibson，认为这种做法是一种[虚假的安全感](https://www.grc.com/sn/sn-630.htm)，很容易被证伪。

关注未来安全的帖子首先出现在[诺尔特数字解决方案](https://www.ndigitals.com)上。