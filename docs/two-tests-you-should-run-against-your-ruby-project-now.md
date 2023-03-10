# 现在您应该对您的 Ruby 项目运行两个测试

> 原文：<https://dev.to/philnash/two-tests-you-should-run-against-your-ruby-project-now>

上周，我有幸参加了精彩的布莱顿红宝石会议。里面充满了优秀的建议、智慧和代码。然而，有一次谈话敦促我立即回家做一些事情，以确保我的项目的安全性和可用性。

Andrew Nesbitt 在 T2 图书馆的支持下，讲述了 Ruby 和其他开源软件的隐性成本。[他的幻灯片可以在这里找到](https://speakerdeck.com/andrew/can-my-friends-come-too)，当视频发布时，我会链接到视频。

## 清点费用

Andrew 的演讲中有两点可以立即付诸实施:检查我的项目中的安全漏洞和许可问题。

## 安全审计

[Rubysec](https://rubysec.com/) 维护着[红宝石咨询数据库](https://github.com/rubysec/ruby-advisory-db)，这是一个易受攻击的红宝石数据库。该数据库目前包含 147 个全球监测系统的 287 份咨询意见。如果你担心你的项目依赖于一个易受攻击的 gem，他们会发布 [bundler-audit](https://github.com/rubysec/bundler-audit) ，这是一个根据他们的数据库审计`Gemfile.lock`的 gem。

要使用 bundler-audit，您需要安装它:

```
$ gem install bundler-audit 
```

Enter fullscreen mode Exit fullscreen mode

并在您的项目目录中运行它。

```
$ bundle-audit
No vulnerabilities found 
```

Enter fullscreen mode Exit fullscreen mode

### 第二种意见

如果一次安全审计不足以消除你的恐惧，那么你很幸运。虽然它不是一个 Ruby 工具，Andrew 也没有提到，但是 [Snyk](https://snyk.io/) 是一个通过他们自己的[漏洞数据库](https://snyk.io/vuln/)来监控 Ruby、JavaScript 和 Java 项目漏洞的工具。

他们发布了一个[命令行界面](https://snyk.io/docs/using-snyk)，你可以用它来检查你的应用程序。你需要[创建一个账户](https://snyk.io/signup)来使用它，并安装 Node.js 和 npm 来运行它。一旦克服了这些障碍，你就可以安装并运行它，就像这样:

```
# install
$ npm install -g snyk
# authenticate the client
$ snyk auth
# test the project in the current directory
$ snyk test
✓ Tested 23 dependencies for known vulnerabilities, no vulnerable paths found. 
```

Enter fullscreen mode Exit fullscreen mode

现在安检过了，执照呢？

## 许可审核

未经许可的代码是您无权使用的受版权保护的代码。

这听起来并不坏，直到你听说 *28%的红宝石宝石没有申报*的许可证。超过 37000 颗宝石。您不需要担心您安装的 gem，而是它们的依赖关系。以及它们的依赖关系。和他们的...还在继续。

谢天谢地，有一个工具来检查这一点。 [`license_finder`由 Pivotal](https://github.com/pivotal/LicenseFinder/) 维护，并检查您对许可证的依赖性。该工具会报告每个依赖项的许可证。然后，您必须决定许可证是否适合您的项目。您可以选择接受您的每个依赖项，并且可以通过将某些许可证列入白名单来批量接受。如果你没有使用 Ruby， [`license_finder`可以处理很多类型的项目](https://github.com/pivotal/LicenseFinder#supported-project-types)。

你用红宝石安装`license_finder`。

```
$ gem install license_finder 
```

Enter fullscreen mode Exit fullscreen mode

然后在项目目录中运行它。这是我和 [`envyable`](https://github.com/philnash/envyable) :
比对的结果

```
$ license_finder
..............................................................................
Dependencies that need approval:
bundler, 1.12.5, MIT
codeclimate-test-reporter, 1.0.8, MIT
docile, 1.1.5, MIT
envyable, 1.2.0, MIT
minitest, 5.10.2, MIT
rake, 12.0.0, MIT
simplecov, 0.13.0, MIT
simplecov-html, 0.10.1, MIT
thor, 0.19.4, MIT 
```

Enter fullscreen mode Exit fullscreen mode

然后，当我将 MIT 许可证加入白名单并再次运行它时:

```
$ license_finder whitelist add MIT
$ license_finder
..............................................................................
All dependencies are approved for use 
```

Enter fullscreen mode Exit fullscreen mode

许可证获得批准。

## 测试所有时间

无论是存在安全漏洞还是未经许可的项目，您都可能让自己或您的用户遇到问题。

安装和运行这些工具并不需要很长时间，我也鼓励你这样做，它们可以将你从依赖中解救出来。将它们添加到您的配置项设置中可能会花费较长的时间，但这可能是值得的。

要了解更多关于 Andrew 演讲的视频，请查看 [libraries.io](https://libraries.io/) 的没有许可证的[图书馆列表](https://libraries.io/unlicensed-libraries)甚至[深入研究数据](https://libraries.io/data)，看看你能找到什么关于我们的开源生态系统的信息。

* * *

*[你现在应该针对你的 Ruby 项目运行的两个测试](https://philna.sh/blog/2017/07/12/two-tests-you-should-run-against-your-ruby-project-now/)最初发表于 2017 年 7 月 12 日 [philna.sh](https://philna.sh) 。*