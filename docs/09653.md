# 从定制博客到媒体:我们的转变(第一部分)

> 原文：<https://dev.to/qlikbranch/from-custom-blogs-to-medium-our-transition-part-1-4kgp>

[![](img/5b81cce6ccc96f879ffdbb2dcdec0e24.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hzWtY2xv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AIDHG41ZqotgLUpwMj6gqpA.jpeg)

当我第一次加入 Qlik 的开发关系团队，并开始与我们的主站点 Qlik 分支一起工作时，我们正在与我们自己定制的博客部门一起工作。虽然这对我们来说似乎很容易开始，但还是有一些问题。

*   **曝光:**我们的博客只在我们的网站上可用，并且需要现有用户或发布链接，以便让世界了解它们。
*   **第三方工具:**为了能够在我们的博客中使用格式，我们需要使用第三方内容编辑器。当我进来的时候，我们正在使用[的 Summernote](http://summernote.org/) ，然后我们尝试使用[的 SimpleMDE](https://simplemde.com/) 进行降价。虽然这些选项都相当健壮，但是任何发现的 bug 都会成为一个问题，定制是一件痛苦的事情。
*   维护:我们负责博客的存储和管理。

在团队内部反复讨论并与其他人讨论之后，我们决定选择 Medium。虽然这将解决上述问题，但我们有一个主要障碍需要解决。

虽然我们打算使用 Medium 来托管我们的博客(以下称为 Medium 术语“故事”)，但我们仍然希望使用 [Qlik 引擎 API](https://help.qlik.com/en-US/sense-developer/3.0/Subsystems/EngineAPI/Content/introducing-engine-API.htm) 中可用的[搜索](https://help.qlik.com/en-US/sense-developer/3.2/Subsystems/EngineAPI/Content/Classes/AppClass/App-class-SearchAssociations-method.htm)功能。这涉及到每当发布一个故事时，我们提取所有信息(包括故事的内容)，以便引擎可以将它加载到我们的 Sense 应用程序中。

我搜索的第一个解决方案是使用 Medium API。作为开发人员，这似乎是显而易见的解决方案，但是我很快就遇到了一个大问题。Medium API 本身相当有限，不允许您检索出版物的故事列表或检索单个故事的信息。如果没有这些能力，API 对我来说几乎没有用。

现在，我需要找到任何类型的来源，可以列出出版物的故事。经过大量的搜索，我能找到的唯一解决方案是好的 ole RSS。每个出版物都有其故事的 RSS 源，当您在给定的出版物上将“RSS 源”设置为 **Full** 时，您也可以获得故事的所有内容。所以我写了一个[快速脚本](https://github.com/Qlik-Branch/branch-resource-library/blob/1dbf848215506a0c6be4f02e2cb9fb8dd54d3a8d/feed-pull.js)，它将每 X 分钟 ping RSS 提要，将提要中的每个故事与一个快速校验和进行比较，以查看故事是否有任何变化，并清除提要中没有的那些(以防故事由于某种原因被删除)。问题解决了！！！！！

事实上，不是的。更多的来了。

* * *