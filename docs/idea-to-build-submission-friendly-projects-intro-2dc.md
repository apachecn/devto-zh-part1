# 提交友好项目:简介

> 原文：<https://dev.to/z0al/idea-to-build-submission-friendly-projects-intro-2dc>

> 灵感来源于 [Ilango](https://dev.to/ilangorajagopal) 的`Idea to Launch` [系列](https://dev.to/ilangorajagopal/idea-to-launch-building-a-side-project-in-public-2ae)。

这是我计划写的一系列帖子的第一部分，我正在构建我的开源项目:**提交者**。我会定期(希望)写下我的工作，包括我遇到的所有挑战。

**其他帖子:**

*   **第二部分:** [格式和堆栈](https://dev.to/ahmedtaj/idea-to-build-submission-friendly-projects-format--stack-7ee)

## 问题

去年早些时候，GitHub 发布了[发布和拉取请求模板](https://github.com/blog/2111-issue-and-pull-request-templates)。它们旨在帮助贡献者在主题开始时分享正确的细节。虽然很酷，但我认为还可以更好！

我已经向许多 OSS 项目提交了许多问题/PR，它们都有不同的问题和 PR 模板。其中一些模板很容易理解，尤其是如果项目仅使用 GitHub 问题来报告 bug 的话。然而，我遇到了许多难以理解(或者太长)的模板。我不知道是不是只有我这样，但我发现自己有时不确定是否要删除一部分，还是就把它留在那里？我需要检查那个盒子吗？或者..？这使我在某些情况下浏览该存储库中打开的以前的问题/PRs，以检查其他人如何格式化他们的描述。

## 想法(实验)

嗯，我不能说这是一个解决方案，因为我不确定它是否真的能解决问题，但至少这是我的实验，让向 GitHub 提交问题不那么痛苦。

这个想法是建立一个基于 GitHub 风格的 Markdown 的传统问题/公关模板规范，这将使问题/公关模板更容易以编程方式阅读，以便以后呈现。别害怕，我只是太不擅长描述事情了。看看这个简单的问题模板:

```
I'm submitting
 - [ ] A feature request
- [ ] A bug
- [ ] Other ..

## Description

[Write here] 
```

Enter fullscreen mode Exit fullscreen mode

如果所有的模板都那么简单，我的问题就不会存在了，然而，大多数模板需要更多的信息(即版本，如果是一个 bug，如果是一个功能请求，则受益..等等)，这很好，但另一方面，让模板有点难以遵循。现在，让我们假设上面的模板太长，我们需要一个更好的方法来填充它(我的项目核心思想)。

首先，让我们再次重写模板，以确认我们的“常规问题/PR 模板规范”(嗯，我撒谎了，还没有规范，只是我脑海中的一个想法:):

```
I'm submitting
 1. [ ] A feature request
2. [ ] A bug
3. [ ] A question

## Description

(Write here) 
```

Enter fullscreen mode Exit fullscreen mode

哇，就是这样！等等，什么？是的，完全正确！

现在我们来解释一下“供以后渲染”的部分。如果我们将上面的“常规”模板传递给我们的 renderer 服务(我需要构建一个理解我们的规范的小型 web 服务)，它将输出类似于下面的内容

```
I'm submitting
<select>
    <option value="x">A feature request</option>
    <option value="y">A Bug</option>
    <option value="z">A question</option>
</select>

<h2>Description</h2>
<textarea placeholder="Write here">
    <!-- A GFM friendly text area-->
</textarea> 
```

Enter fullscreen mode Exit fullscreen mode

> **注意:**由于某些原因，我们不能使用下拉列表，我们将使用单选按钮。查看本系列的[第 2 部分](https://dev.to/ahmedtaj/idea-to-build-submission-friendly-projects-format--stack-7ee)了解更多信息。

注意到区别了吗？我们的渲染器 web 服务将订单列表转换为 HTML `<select>`元素，将`(text)`模式转换为带有占位符的`<textarea>`元素。现在，从理论上讲，用户可以简单地从下拉列表中选择他们的问题类型，并在文本区输入他们的文本(他们可能仍然使用 GFM)，然后按“提交”按钮，问题就会以他们的名义在 GitHub 上创建。

当然，这不仅仅是关于下拉，你可以在适当的时候使用普通的 GitHub 清单，比如这个:

```
 - [ ] I've tested my changes
- [ ] I've updated the docs 
```

Enter fullscreen mode Exit fullscreen mode

此外，在未来，我可能会支持显示/隐藏一些部分的基础上下拉选择(即，如果您选择“错误”只有部分相关的错误报告将被显示)。

我希望你能得到这个想法，如果没有，请写一个评论指出不清楚的部分，我会尽我所能重写得更清楚。

## 最终的项目可能会是什么样子？

1.  您需要在回购中进行发行(即`owner/repo`)
2.  您需要导航到我们的 web 服务 URL(即`ourwebservice.com`)。网址看起来会像这样:`https://ourwebservice.com/new?repo=owner/repo`
3.  然后，web 服务会要求您通过 GitHub 登录。
4.  如果您登录，它将从`owner/repo`(例如`.github/OUR_ISSUES_TEMPLATE.md`)获取模板文件，解析它，然后用原始 markdown 模板的 HTML 呈现版本进行响应。
5.  然后你需要填写模板表格(如上所示)，并按“提交”
6.  将在`owner/repo`存储库中为您创建一个新问题。
7.  搞定了。

## 现在怎么办？

我仍然在我的脑海中验证这个想法，非常感谢你的反馈和想法，请在下面写下评论吧！

在下一篇文章中，我将向你展示我们简单的常规问题/公关模板的初稿。

谢谢:)