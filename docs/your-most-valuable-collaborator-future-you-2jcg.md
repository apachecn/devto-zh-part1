# 你最有价值的合作者，未来-你

> 原文：<https://dev.to/rmhogervorst/your-most-valuable-collaborator-future-you-2jcg>

我最近参加了一个 R 用户会议，会上 Hadley Wickham 谈到了数据争论。他展示了一些有趣的东西！您知道可以将数据框放入数据框吗？您可以创建数据框列表并将该列表添加到您的数据框中。非常酷，比我想象的更有用，但这不是我想谈的。

我想给你一些关于和你将来可能会一起工作的人一起工作的建议。哈德利会把那个人介绍给你:

> “在每个项目中，你至少还有一个合作者；未来-你。你不要未来——你要诅咒过去——你" <sup id="fnref:1">[1](#fn:1)</sup>

和未来-你是一个重要的合作者。我和 R 一起工作了将近两年 <sup id="fnref:2">[2](#fn:2)</sup> ，当我回头看的时候，我确实看到了我和 R 一起工作的一些进展。我最近想更新我的 imdb 搜索包但我决定不碰它，因为我不知道如果我改变一些东西会破坏什么。我对最近的软件包不太担心，因为我对几乎所有的功能都进行了测试，我可以在每次更改后运行测试，看看功能是否仍然存在。

我试图在我的 github 中找到更老的作品，但事实上我对这些作品的记录之好印象深刻！所以我的例子不怎么样，或者就是没有上传不好的例子……]

我确实发现，匆忙的工作最终会占用最多的时间。如果我只是快速编写一些代码，然后在一周后重新访问，它可能仍然有效，我可能仍然知道我想要做什么。但是如果未来的时间延迟变得更长，你将需要花更多的时间来弄清楚过去你试图实现的目标。

所以即使你现在很着急，将来也不会记得某些决定的理由。尤其是当你的项目越来越大的时候，你很容易忘记事情。

关键是对未来尽可能清晰明了。那么，我们如何帮助未来的你(或其他合作者)了解过去的你呢？

以下是基于大量引用和花絮的一些建议。

## 自述驱动开发

几乎所有的 github 项目都有格式良好的自述文件。这是一个非常简单的文档，描述了项目的总体意图以及如何使用和安装它。自述文件是一个很好的软件文档。那么，如何确保自述文件准确地描述了软件呢？

你先写自述。

汤姆·普莱森-沃纳就此写了一篇很好的文章。如果你先写自述文件，那么你可以先考虑一下这个项目，你已经知道最终用户(可能大部分是未来的你)需要什么了。而且你也不需要事后写文档。我实际上已经在一些项目中尝试过这种方法(f.i. [badgecreator](https://github.com/RMHogervorst/badgecreatr) ，它帮助我思考功能和逻辑步骤。如果你描述了接下来的步骤，其他人会更容易加入进来。

## 先写测试

一种理解过去的方式——你对未来的语无伦次的漫谈(看我在那里做了什么？)，就是在编写通过测试的那部分代码之前编写测试。我说的是单元测试，即检查你的代码是否仍然像你计划的那样工作的测试。

那么，首先编写测试将如何帮助未来的你和当前的(？)-你们互相理解？如果写得正确的话，单元测试是一种承诺，或者是一种理解，关于代码片段应该如何工作。例如，在我的一个项目中，单元测试预计在某些情况下会出现错误。因此，当这些条件出现时，代码应该给出一个错误。

```
test_that("rowsums larger or smaller than 1 are failing",{
        expect_error(CreateVertices(errorset,"var1", "var2", "vartoomuch", verticeName = T),regexp = "column means are not equal to 1" )
        expect_error(CreateVertices(errorset,"var1", "var2", "vartoolittle", verticeName = T),regexp = "column means are not equal to 1" )
}) 
```

我真的很喜欢 testthat 包的优雅，它**字面上**说它测试什么:“*测试大于或小于 1 的行都失败”。如果测试失败，它会准确地告诉你**在哪里**失败了，以及**试图测试什么**(根据你的说法)。*

那么，您将如何实现这一点呢？*我稍后会单独写一篇关于这个的博客，但是现在看看 r-pkg [关于测试](http://r-pkgs.had.co.nz/tests.html)*的第章从你想要创建的东西开始，例如:计算所有被称为 Roel 的人。这是一个不错的小功能。给它起个名字:`roel_counter`，比如。

创建一个单独的测试脚本`test_roel_counter.r`。使用 testthat 框架，您可以将该文件放在`tests/testthat/`中，这样如果您点击快捷键，它和所有其他单元测试都将被执行。

我通常是这样写测试的。

```
context("roel_counter")
teststring <- c("Roel", "Roel", "Hans")
confusestring < c("Roel", "roel", "rOel", "roef"

test_that("roel_counter finds accurate number of roel",{
   expect_equal(roel_counter( teststring, 2)
   expect_false(roel_counter( teststring) == 3)
   expect_equal(roel_counter( confusestring, 3)
   expect_false(roel_counter( confusestring) == 4) 
} 
```

第一个测试将检查 Roel 计数器是否找到 2 个 Roel，第二个测试将检查字符串是否不仅仅返回字符串的总长度。

我可以在测试中加入我名字的各种变体，看看这个函数是否有效。事实上，你在控制台上尝试的大部分功能都可以放到测试文件中。但是在测试驱动开发中，你应该首先创建测试，然后编写函数。在你写了一部分可以工作的代码后，你将测试并看看什么通过了，什么失败了。然后你在代码中加入一些东西来通过更多的测试。在所有的测试都通过之后，你为代码的新部分写一个新的测试。

如果当前-你被打断，未来-你发现自己回到代码，未来-你可以按下测试的热键，找出失败的部分并继续。所以在某种程度上，未来——你可以明确地找到过去——你试图达到的目标。

## 使用版本控制

版本控制可以帮助你保持理智。如果你尝试新的东西，它会保存你的工作，它会帮助你在历史上任何一个之前提交的点重新开始，除了合并错误之外，它通常工作得很好。

版本控制允许您记录以前代码的快照，但也允许您对您解决或更改的内容进行评论。当你保存一个新版本的代码到版本控制中时，你必须明确一点。否则你会发现自己处于这种情况:

[![an example of a less helpful commit message](img/b259fb1d2c217680e8b2a4f2ed48d62e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4ckTRygW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/wrong_commit.PNG)

版本控制，如果你使用有意义的描述，可以帮助你找到你在哪里做了一些动作，你想做什么。描述提交消息中的意图。我很喜欢的一句话是:

> "你通常与自己合作，而两个月前的我从不回复电子邮件." <sup id="fnref:3">[3](#fn:3)</sup>

我的提交有所改善:

[![somewhat better commit messages](img/a7e28e2105a99996fe807d02785cef60.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xrdvpN52--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/example_better_commit.PNG)

你甚至可以使用 github 基于问题的工作流程，在这里你写下你想在不同的问题中解决的问题，在你解决了一个特定的问题后，你将代码推送到 github，引用问题编号。 <sup id="fnref:4">[4](#fn:4)</sup>

## 功能编程

哈德利非常擅长函数式编程。也就是说，明确你想要达到的目标，抽象出计算机本身发生的事情。

管道操作符就是一个例子。管道操作符在许多编程语言中使用(在 UNIX 中是一件大事)，但它不是 base r 的一部分，直到 Stefan Milton Bache 在 magritr 包 <sup id="fnref:5">[5](#fn:5)</sup> 中引入它。管道没有什么特别的，除了它把左边的东西放在右边的第一个地方。例如，`dataset %>% filter(name=="Roel")`意味着我们从数据集开始，然后过滤名称列中有`"Roel"`的行。在后台它确实`filter(dataset, name == "Roel")`

这使得代码更容易阅读。比较:

`filter_by(select( filter(dataset, year == 2012), month, children, accidents ),accidents)`同

`dataset %>%
filter(year==2012) %>%
select(month, children, accidents) %>%
group_by(accidents)`

然后是 purr 包中的 map 函数，它本质上取代了循环。这并不是因为循环本身就不好，而是因为函数关注的是正在执行的操作，而不是循环本身的细节。以后一旦习惯了这些功能我会写的。 <sup id="fnref:6">[6](#fn:6)</sup>

## 注释

关于代码中注释的最后一点思考。

> 经常做。

在评论中主要描述你的意图是什么，或者你使用一个函数的原因。不要描述**什么**，而是描述**为什么**。只要你觉得有必要，就要经常发表评论。多总比少好。但是不要把未来的计划或错误放在评论里，你不会看的。问题追踪器是一个很好的地方。

# 最后的想法

想想这句话:

> “编写代码的时候，要把最终维护你代码的人想象成一个知道你住哪儿的暴力精神病患者。”

但是回头看看这篇文章，**你**很可能是维护代码的暴力精神病患者。

所以这是一些对自己未来有帮助的方法，尝试一些，告诉我效果如何。

# 参考文献

* * *

1.  或者接近这些话，我后来记下来了。——威克姆阿姆斯特丹 18-5-16 <sup>【回归】</sup>
2.  我不知道确切的时间，但是如果用我创建 Github 账户的时间(2014 年 11 月 3 日)来算，我写这篇文章的时候差不多有 19 个月了。<sup>【返回】</sup>
3.  mtholder 的一条推文:[https://twitter.com/kcranstn/status/370914072511791104](https://twitter.com/kcranstn/status/370914072511791104)文字推文:@mtholder 激励 git:你大多与自己合作，而两个月前的我从不回复电子邮件。@ SW 木工<sup>【归来】</sup>
4.  那可能太多了，但是它非常好。如果您键入“关闭#10”或“修复# 10 ”, github 将关闭问题 10 并引用该提交。我还听说有人使用单独的分支来解决问题，并使用主分支的拉请求来解决问题(修复或关闭也是一样)。我在某些情况下尝试过这种方法，它有助于组织你的工作。<sup>【返回】</sup>
5.  我花了很长时间才找到它为什么被称为 magritr。因为大多数 R 包都是以它们的功能命名的，或者是一个奇怪的双关语。但这是马格瑞特(根据维基百科，La trahison des images)的一幅名画的双关语，这幅画是一个烟斗，下面的文字是“ceci n ` est pa une pipe”“‘这不是烟斗’。[https://en.wikipedia.org/wiki/The_Treachery_of_Images](https://en.wikipedia.org/wiki/The_Treachery_of_Images)和为包[https://cran.r-project.org/web/packages/magrittr/index.html](https://cran.r-project.org/web/packages/magrittr/index.html)<sup>【返程】</sup>
6.  请参阅 hadley wickham 所著的《数据科学的 r》一书的第 16 章(迭代)中关于循环的更多信息。这本书仍在编写中，但它是一个信息宝库。<sup>【归来】</sup>