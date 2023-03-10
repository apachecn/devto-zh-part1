# Bug 赏金猎人 GitHub

> 原文：<https://dev.to/securitytxt/github-for-bug-bounty-hunters-3g5l>

GitHub 库可以为 bug 赏金猎人透露各种潜在的有价值的信息。目标并不总是必须是开源的才会有问题。组织成员和他们的开源项目有时会意外地暴露可能被用来攻击目标公司的信息。在本文中，我将向您简要介绍一下，这将有助于您开始针对 GitHub 库的漏洞和一般侦察。

## 大规模克隆

您可以只在 github.com 上做您的研究，但是我建议克隆所有目标的存储库，以便您可以在本地运行您的测试。我强烈推荐@mazen160 的 [GitHubCloner](https://github.com/mazen160/GithubCloner) 。只需运行脚本，您就可以开始了。

```
$ python githubcloner.py --org organization -o /tmp/output 
```

Enter fullscreen mode Exit fullscreen mode

## 静态分析

当谈到静态分析时，从真正理解你的目标项目开始非常重要。运行项目并使用主要功能。我称之为“Jobert 步骤”,因为我听说, [Jobert](https://twitter.com/jobertabma/) 在每次搜索的前 30 分钟使用项目并在发现漏洞之前了解目标。

## 手动分析

这就是“学会制造，然后打破它”的心态发挥作用的地方。如果你能熟悉一门编程语言，你应该知道在安全方面什么该做，什么不该做的来龙去脉。

一旦你理解了目标和它的架构，你就可以开始 grepping 了！搜索你感兴趣、最了解或者知道开发者容易搞砸的关键词。以下是我在第一次总体评估时会寻找的一些关键词的基本列表:

*   API 和密钥。(再弄点端点，找 API 键。)
*   代币
*   秘密
*   待办事项
*   密码
*   易受伤害的😜
*   http:// & https://

然后，我将关注那些当开发人员把事情搞砸时让我会心一笑的术语:

*   CSRF
*   随意
*   混杂
*   MD5、SHA-1、SHA-2 等。
*   HMAC

当您习惯了某些类型的漏洞时，您将开始确切地知道在特定的语言中应该寻找什么。例如，当我想在 Java 中找到一个时间漏洞时，我知道是`Arrays.equals()`和`HMAC`的组合导致了这个问题。

另一个重要步骤是查看提交历史。您会惊讶于从提交中收集到的信息量。有时我看到贡献者认为他们已经删除了凭证，而他们仍然在提交历史中。由于 git 的历史，我遇到过仍然工作的旧端点。除了当前的问题，您可能会发现过去的问题，由于旧的提交，这些问题可能会被忽略。

[![image](img/db82e1ff5b41cd01724501a625837afd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qm-6nZUT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/29084727-37ae5238-7c2a-11e7-9e2e-36d39feeb5b5.png)

## 工具

有时候，自动化枯燥的任务可以帮助你对要寻找的东西有一个基本的了解。需要注意的是，您永远不要将扫描仪的结果复制并粘贴到您的报告中。您将会得到许多误报，因此您必须总是手动调查可能的问题，以确保可利用性。

当我瞄准 Python 项目时，我使用的主要工具是 [Bandit](https://github.com/openstack/bandit) 。Bandit 会找到常见的问题，但通常会返回低挂水果或误报。所以用的时候一定要小心。绝对不应该依赖。

```
$ bandit -r path/to/your/code -ll 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果你想在一个项目中找到过时的 Python 模块，将`requirements.txt`的内容粘贴到[https://pyup.io/tools/requirements-checker/](https://pyup.io/tools/requirements-checker/)中。这将向您显示指定版本的模块中是否存在任何安全问题。

* * *

Snyk.io 是一个很棒的检查依赖关系的工具。该平台支持多种语言。

[![image](img/faa11a06f7d9af059c345ef2bc244ea3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wsDAp4bj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/29084028-fc707cde-7c27-11e7-8da4-3c40d2d2e9ae.png)

* * *

对于侦察，许多研究人员建议使用 [Gitrob](https://github.com/michenriksen/gitrob) 。该工具将在公共 GitHub 存储库中寻找敏感信息。

```
$ gitrob analyze acme,johndoe,janedoe 
```

Enter fullscreen mode Exit fullscreen mode

* * *

用于查找高熵字符串(API 密钥、令牌、密码等。)，可以用[松露猪](https://github.com/dxa4481/truffleHog)。

```
$ truffleHog https://github.com/dxa4481/truffleHog.git 
```

Enter fullscreen mode Exit fullscreen mode

* * *

如果你正在寻找一个全方位的秘密查找器，那么 [@anshuman_bh](https://twitter.com/anshuman_bh) 的 [git-all-secrets](https://github.com/anshumanbh/git-all-secrets) 就是你的工具。这个工具结合了多个开源秘密发现者成为一个大工具。

* * *

对于 Ruby on Rails 应用，我推荐 [Brakeman](http://brakemanscanner.org/) 。Brakeman 是一个静态分析安全扫描器，可以在代码中找到大量各种各样的安全问题。

* * *

使用 [Gerben Javado](https://twitter.com/gerben_javado) 的 [LinkFinder](https://github.com/GerbenJavado/LinkFinder) 在存储库的 JS 文件中查找端点。

```
$ python linkfinder.py -i 'path/to/your/code/*.js' -r ^/api/ -o cli 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 社会工程

好吧，说真的，不要对项目负责人进行社会工程。

## 汇报你的发现

像往常一样，当涉及到臭虫赏金猎人，通读该计划的政策。很少有程序通过 GitHub 接受报告。联系安全团队，或者如果可能的话，使用 bug bounty 平台，如 [HackerOne](https://www.hackerone.com/) 或 [Bugcrowd](https://www.bugcrowd.com/) 。

另一方面，白盒测试的一个很酷的地方是，因为你可以访问代码，所以可以更容易地提出修复建议或提交补丁。😉