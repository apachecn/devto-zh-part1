# 博客文章:为新项目做贡献——有点像开始一份新工作

> 原文：<https://dev.to/jonasbn/blog-post-contributing-to-a-new-project--a-bit-like-starting-a-new-job-32o8>

我已经使用和创造开源软件很长一段时间了，但是我认为我从来没有真正贡献过任何有意义的东西。是的，bug 报告，你偶尔的 PR——都很重要，但是我从来没有参与过任何高调的项目，或者是一个更大的项目或系统，背后有许多贡献者或组织。

最近，我从许多博客和播客中了解到，作为一名开发人员，为了发展，你必须走出你的舒适区。不久前我迈出了第一步，当时我决定为 MarkdownTOC 做贡献，这是一个用于 Sublime Text 的插件，插件是用 Python 编写的，我的第一个贡献是[删除了一行](https://github.com/naokazuterada/MarkdownTOC/pull/65/files)。我没有用 Python 编程，但是我使用了 Sublime Text，这个特别的问题，正在挠我自己的痒痒。

这并不多，但积极的影响是作者实际上欢迎我的贡献，我们开始了持续的合作。从那以后，我在文档方面贡献了更多，目前[我在贡献的行数中排名第二](https://github.com/naokazuterada/MarkdownTOC/graphs/contributors)。并不是说这对我来说很有声望，但它确实证明了即使不是实际的代码，贡献也是显著的，并且是最受赞赏的。

在某个时候，我看到了一条来自电子前沿基金会的推文，表明 T2 的开源项目正在寻找志愿者和贡献者。经过一些考虑，我总是做很多考虑当离开我的舒适区时，我决定试一试。

我只能代表我自己，但是让我们后退一步，思考一下舒适区和开源，以及为什么为开源做贡献是一个舒适区的问题。

如果我们从总体上看开源。你制造一些东西，然后把它放在那里让其他人使用或不使用，它可能会被仔细检查或不检查。幸运的是，今天开源的数量是势不可挡的，所以你实际上可以开源你的工作，如果人们不喜欢或不想使用它，他们会选择另一种替代方案来解决他们需要挠痒痒的问题。这意味着审查和反馈可能没有想象中的那么严格，我猜一些开源作者工作的领域，他们的贡献被成千上万的人使用和查看，审查和反馈是不同的，Linux 内核就是一个例子。

我决定去看看 certbot 项目。

我没有用 Python 编程，但是它是一种解释型语言，而且作为一名长期的 Perl 程序员，基于我对 Python 非常有限的了解，我确实希望对这两种语言有所熟悉。

在看了标有“好的第一期”的[期后，我决定看第 4736 期](https://github.com/certbot/certbot/labels/good%20first%20issue)[。我对这个问题发表了意见，因为我不想在已经有人被指派或正在进展的问题上开始工作。我得到了积极的回应，我准备好开始了。](https://github.com/certbot/certbot/issues/4736)

开始需要阅读大量关于如何真正开始、如何做出贡献以及使用什么工具的文档。大多数开源项目不仅仅是它们的源代码。他们有很多基础设施集成和工具链定制，有些项目是“ *fork，hack，test，push* ”，你必须安装额外的工具并配置它们。

我从分叉项目开始，在我的笔记本电脑上安装并运行 Sphinx。

```
$ pip install Sphinx $ cd docs $ make html

sphinx-build -b html -d \_build/doctrees   . \_build/htmlRunning Sphinx v1.6.2making output directory...Exception occurred:  File "[conf.py](http://conf.py/)", line 133, in \<module\>     import sphinx\_rtd\_themeImportError: No module named sphinx\_rtd\_themeThe full traceback has been saved in /var/folders/4s/v4\_4270j5ybb60t4kjwk\_f080000gn/T/sphinx-err-AmhKOS.log, if you want to report the issue to the developers.Please also report this if it was a user error, so that a better error message can be provided next time.A bug report can be filed in the tracker at \<[https://github.com/sphinx-doc/sphinx/issues](https://github.com/sphinx-doc/sphinx/issues)\>. Thanks!make: \*\*\* [html] Error 1 
```

第一个问题是[一个容易解决的问题](https://github.com/rtfd/sphinx_rtd_theme#via-package):

```
$ pip install sphinx\_rtd\_theme $ make html

sphinx-build -b html -d \_build/doctrees   . \_build/htmlRunning Sphinx v1.6.2Extension error:Could not import extension repoze.sphinx.autointerface (exception: No module named repoze.sphinx.autointerface)make: \*\*\* [html] Error 1 
```

第二个问题[另一个简单的解决方法](https://github.com/repoze/repoze.sphinx.autointerface):

```
$ pip install repoze.sphinx.autointerface $ make html 
```

终于获得成功，我能够开始填空了。

我扫描了文件结构，并将其与文档结构进行了比较。

```
cert\_manager.py cli.py eff.py error\_handler.py hooks.py lock.py log.py main.py notify.py ocsp.py renewal.py plugins/ common\_test.py disco\_test.py dns\_common\_lexicon\_test.py dns\_common\_test.py dns\_test\_common.py dns\_test\_common\_lexicon.py manual\_test.py null.py null\_test.py selection.py selection\_test.py standalone\_test.py util\_test.py webroot\_test.py 
```

所以我添加了丢失的文档文件。重新生成文档时，观察到以下问题:

```
certbot/cli.py:docstring of certbot.cli.HelpfulArgumentParser.add:7: WARNING: Inline emphasis start-string without end-string. certbot/cli.py:docstring of certbot.cli.HelpfulArgumentParser.add:8: WARNING: Inline strong start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler:6: WARNING: Inline emphasis start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler:6: WARNING: Inline strong start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler:6: WARNING: Inline emphasis start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler:6: WARNING: Inline strong start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler.register:1: WARNING: Inline emphasis start-string without end-string. certbot/error\_handler.py:docstring of certbot.error\_handler.ErrorHandler.register:1: WARNING: Inline strong start-string without end-string. 
```

一个小小的俏皮技巧帮助消除了警告。最后，Sphinx 向我发出警告，指出一些文件不是整个文档树结构的一部分。

```
certbot/docs/challenges.rst:: WARNING: document isn't included in any toctree certbot/docs/ciphers.rst:: WARNING: document isn't included in any toctree certbot/docs/man/certbot.rst:: WARNING: document isn't included in any toctree 
```

在这之后，我给[发了第一份 PR](https://github.com/certbot/certbot/issues/4736) ，所有这些只是技术问题，可以由我自己解决。总体工作远未完成*。下一步是更新文档，这意味着 Sphinx 用来生成文档的信息也必须与实际实现保持一致，我刚刚开始做这件事。这确实需要更多关于 [certbot](https://certbot.eff.org/) 的知识和更多 Python 方面的阅读。随着我涉及的领域越来越广，我对 Python 细节的了解也越来越多。*

–内部类

–命名约定

–模块使用和继承

–隐式返回

–无数据类型

我对实际的 certbot 实现有很多问题，但我会在每个作业/文件中问这些问题，因为有人建议我为每个更新的文件做一个 PR，我的第一个 PR 正在慢慢成形。

开始为一个更大的项目做贡献是一项艰苦的工作，它让我想起开始一份新工作，因为你会接触到:新系统、新工具、新流程和新同事。你所做的大部分是相似的，或者你有以前的经验，但同时一切都是不同的，所以无论如何都有一个学习曲线。

certbot 项目中的人们很友好，也很乐于助人，这确实意味着舒适区的问题得到了缓解。同时，如果你专注于你能给这个项目带来什么，你带来的*东西*，即使这只是工时，你也不会失败。

然而，如果你所有的公关都被拒绝了，如果你所有的问题都得不到回应，或者你所有的询问都得不到令人讨厌的回应——与其感到不舒服，不如另找一个项目。还有很多其他的开源项目，欢迎您的努力。无论发生什么，你都会学到东西，你会进步，你的舒适区会扩大。不需要被舒适区的感觉所阻碍，走出去，从小事做起，做出贡献，不断进步。

从我的博客转贴一篇[旧帖](https://lastmover.wordpress.com/2017/11/05/contributing-to-a-new-project-a-bit-like-starting-a-new-job/)