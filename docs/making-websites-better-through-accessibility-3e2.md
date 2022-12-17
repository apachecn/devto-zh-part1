# 通过可访问性使网站更好

> 原文：<https://dev.to/gaijinity/making-websites-better-through-accessibility-3e2>

# 通过可访问性让网站更好

### 使你的网站可访问不仅仅是法律上的问题，这也意味着不要拒绝顾客。因为谁想这么做？

安德鲁·韦尔奇

[![Web Accessibility Ada](img/55e533970b3cc6c2a26d59b6e012582f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--87FnPc1d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/web-accessibility-ada.jpg)

让你的网站可访问意味着让尽可能多的人可以访问它。首先，让人们访问你的网站已经是一项很有文化的任务，所以我们肯定不想因为访问权限问题而拒绝百分之一的人。

我发现许多开发人员并不真正理解访问权限，或者在他们的任务中把它放在“这很好”的列表中。只要有一点 edu 行动和适当的工具，事情就不会变成那样。

<aside>I view web­site acces­si­bil­i­ty as cus­tomer acqui­si­tion, as well as build­ing an inclu­sive web­site that serves the most num­ber of peo­ple as possible</aside>

在许多国家，网站的访问权限也受到法律的约束。例如，在美国，有《美国残疾人法案》(ADA)，DOJ 已经明确将该法案延伸到互联网。

像 Tar get(见 [NFB 诉 Tar get](https://www.w3.org/WAI/bcase/target-case-study) )和 Net flix(见[纳德诉 Net flix](http://www.3playmedia.com/2015/07/23/nad-v-netflix-ada-lawsuit-requires-closed-captioning-on-streaming-video/) )这样的公司已经成为基于此的法律诉讼的对象；但是让尽可能多的人接触你公司的产品或服务也是一件好事。

如果您计划为美国联邦政府或州政府(包括 edu 国家机构)开发网站，[网站根据 ADA](https://www.ada.gov/pcatoolkit/chap5toolkit.htm) 文档标题 II 获得许可，列出了网站的许可日期。即使是公共公司也很可能需要与 ADA 合作；看看[你的网站需要成为一个无障碍的网站吗？](http://www.logicsolutions.com/does-website-need-ada-compliance/)详情见文章。

我只是假设你想让你的网站可以被上面提到的超出法律规定的实际原因访问。就像我之前讨论过的许多顶级集成电路一样，这一切都是为了让网络变得更好。

<aside>Both per­for­mance & acces­si­bil­i­ty are not about padding pro­pos­als; they are about mak­ing web­sites that are more effec­tive for both our clients, and their customers.</aside>

这也是我们应该如何向客户销售访问权限的方式，类似于我们在文章[中谈到的“一个正确的网站是不够的”](https://dev.to/gaijinity/a-pretty-website-isn-8217-t-enough-1nbj-temp-slug-3932740)中的销售权限。我们应该将无障碍能力融入到我们的项目中，并将其融入到我们的设计&开发工作流程中。Edu 向客户解释为什么这很重要，他们很快就会明白。

[![Velvet Rope Let People In Accessibility](img/b7da91281415123f2f399f43c21dd1f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MHqAdDDr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_992x558_crop_center-center_82_line/velvet-rope-let-people-in-accessibility.jpg)

[个人访问 UX](http://rosenfeldmedia.com/a-web-for-everyone/personas-for-accessible-ux/) 文章是一个非常有用的资源，可以帮助您了解人们可能患有的疾病类型，以及这些疾病如何影响他们访问您网站的能力。花点时间通读这里列出的每个 sonas，完成后再回来。

真，这是值得的。我会等的。

## 主要使用案例

因此，这里有一个人们在你的网站上可能遇到的疾病的非详尽综合分析(从[这里](https://github.com/dpersing/ada-a11y-intro/blob/master/a11y-presentation.md)摘录):

*   视力(失明、低视力、色盲或失明)
    *   2011 年，美国超过 6，500，000 名成年人(大多数在 18 至 64 岁之间)和超过 650，000 名儿童报告了与低视力或失明有关的问题。
    *   大约十分之一的男人是色盲。
*   听力(耳聋)
    *   美国大约有 3600 万成年人有某种形式的听力损失。
    *   在美国，大约千分之二的儿童天生耳聋。
*   运动技能(使用鼠标有问题)
*   认知和学习问题(自闭症、智力障碍、注意力不集中等)。)
    *   在美国，大约 88 个孩子中就有 1 个属于自闭症谱系。
    *   大概有 20%的人有某种形式的阅读障碍。
    *   在美国，11%的 4-17 岁儿童和大约 4%的成人被诊断患有注意力缺陷障碍。

一般来说，据估计 12-20%的人患有某种疾病。

所以让我们做点什么吧！

## Pa11y 作为辅助工具

实际上，让网站无障碍并不难；难的是知道你应该做什么(T2)。虽然现在有一些基于网络的工具，但我发现`npm`pack age[pa1y](https://www.npmjs.com/package/pa11y)对我来说是最新最有效的工具。

Pa11y 只是一个位于 [HTML_ CodeSniffer](http://squizlabs.github.io/HTML_CodeSniffer/) 之上的客户端程序，它执行[网页内容访问安全指南(WCAG) 2.0](http://www.w3.org/TR/WCAG/) 的三个性能级别，而[美国“508 条款”分支的网页相关组件是第](https://www.section508.gov/)款。

那么就来说说怎么用吧。

<aside>The abbre­vi­a­tion #a11y is often used for ​“acces­si­bil­i­ty”, where the num­ber 11 refers to the num­ber of let­ters omit­ted from ​“acces­si­bil­i­ty”</aside>

我将坦率地声明，我不是访问权限方面的专家；如果您想深入了解访问权限，请使用 Aria 资源查看[开始使用 Web 访问权限](https://www.w3.org/WAI/gettingstarted/) & [。但是如果你使用](https://w3c.github.io/using-aria/)[语义 HTML5 元素](https://www.w3schools.com/html/html5_semantic_elements.asp)，使用合适的标题，并使用`alt`属性来显示图像，你就成功了一半。

对于其余的部分，`pa11y`给你一个很容易理解的列表，列出了什么是 bro ken，以及如何修复它。使用 [WC3 Val ida tor](https://validator.w3.org/) 来检查你的 HTML 的正确性是非常简单的，所以列出一大堆要修改的东西并不难。

[![Kintsugi Fix Whats Broken](img/bf21fc34a4bbb8912e33851cf53b1a05.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ct9mLvF9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/kintsugi-fix-whats-broken.jpg)

你需要安装[节点](https://nodejs.org/) & [NPM](https://www.npmjs.com/) (或者[纱线](https://yarnpkg.com/en/))来利用它，但是希望你已经能够快速使用这些工具。如果你不是，那就一定要跟上时代的步伐，因为发展趋势正在不可避免地向他们靠拢。

所以我们要做的第一件事就是安装一个叫做 [npx](https://www.npmjs.com/package/npx) 的包；这允许你运行节点模块，而不需要安装它们(全局安装或作为软件包的一部分)，通过`npm`或`yarn` :

```
 sudo npm install -g npx

sudo yarn global add npx 
```

我们正在安装`npx` pack age glob al，所以我们使用`sudo`使它可以打开；但是如果你愿意，或者没有 T2 的权限，你也可以查看如何在没有 T4 的情况下安装软件包。

当然，如果你愿意，你也可以在你的项目中添加`pa11y`作为依赖项，如果你愿意这样做的话。你甚至可以安装`pa11y` glob al ly，就像我们安装`npx`一样。但是我想介绍使用`npx`作为一种通用的方法来执行节点模块，而不需要安装它们。

`npx`在这种情况下也有一个特殊的 cif ic ben e fit:它将确保我们总是用最新的规则集运行最新的`pa11y`,而不必保持它是最新的。

## 修复访问权限问题

现在我们已经安装了`npx`,我们可以运行`pa11y`,并获得它对我们网站访问能力的评估！我们将使用这个网站的[博客页面](https://nystudio107.com/blog)作为访问权限问题的试验品:

[![Guinea Pig Test](img/abca6c17612148aaf1cdf45dcfb68cca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RxXhsxmw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/guinea-pig-test.jpg)

要运行`pa11y`，我们只需做:

```
 npx pa11y --standard "WCAG2AA" --ignore "notice;warning" https://nystudio107.com/blog 
```

我们告诉它，我们不想看到任何`notice`或`warning`，而只是错误。如果您想做一个更全面的检查，您也可以通过在命令中省略`warning`来显示它。

开箱即用，`pa11y`支持 4 种访问速度标准:`Section508`、`WCAG2A`、`WCAG2AA`、`WCAG2AAA`。缺省值是`WCAG2AA`，但是你可以通过`--standard`命令行参数指定任何你想要的标准。

所有的`WCAG2*`标准都是相同的国际标准，但是严格程度不同(`A`最不严格，`AAA`最严格)。在控制台的[中可以找到关于 WCAG 水平的更多信息。](https://www.w3.org/TR/UNDERSTANDING-WCAG20/conformance.html#uc-levels-head)

这个标准是给美国联邦政府机构的，如果你在为他们做网站的话。

但是我们将使用默认的`WCAG2AA`标准，它在严格性方面处于中间位置(确实是 WCAG 建议使用的标准)，如果我们不指定`--standard`，它将默认使用。

下面是输出的样子:

```
 vagrant@homestead:~$ npx pa11y --ignore "notice;warning" https://nystudio107.com/blog
Welcome to Pa11y

 > PhantomJS browser created
 > Testing the page "https://nystudio107.com/blog"

Results for https://nystudio107.com/blog:

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nav-menu > div > div:nth-child(1) > div > div > a
   └── <a href="/">

 • Error: This element has insufficient contrast at this conformance level. Expected a contrast ratio of at least 4.5:1, but text in this element has a contrast ratio of 2.93:1\. Recommendation: change background to #3c3d3f.
   ├── WCAG2AA.Principle1.Guideline1_4.1_4_3.G18.Fail
   ├── #nav-menu > div > div:nth-child(3) > ul > li:nth-child(2) > a
   └── <a href="/blog" class="nav-link">Blog</a>

 • Error: This text input element does not have a name available to an accessibility API. Valid names are: label element, title attribute.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.InputText.Name
   ├── #searchbox
   └── <input type="text" id="searchbox" placeholder="search" autocomplete="off" class="autocomplete-input">

 • Error: This form field should be labelled in some way. Use the label element (either with a "for" attribute or wrapped around the form field), or "title", "aria-label" or "aria-labelledby" attributes as appropriate.
   ├── WCAG2AA.Principle1.Guideline1_3.1_3_1.F68
   ├── #searchbox
   └── <input type="text" id="searchbox" placeholder="search" autocomplete="off" class="autocomplete-input">

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(1) > div > a
   └── <a href="/"> 

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(1)
   └── <a class="social" href="mailto:info@nystudio107.com"><i class="icon-mail-alt"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(2)
   └── <a rel="nofollow" class="social" href="https://www.facebook.com/newyorkstudio107"><i class="icon-facebook"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(3)
   └── <a rel="nofollow" class="social" href="https://twitter.com/nystudio107"><i class="icon-twitter"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(4)
   └── <a rel="nofollow" class="social" href="https://github.com/nystudio107"><i class="icon-github-circled">...</a>

 • Error: Iframe element requires a non-empty title attribute that identifies the frame.
   ├── WCAG2AA.Principle2.Guideline2_4.2_4_1.H64.1
   ├── #Smallchat > iframe
   └── <iframe data-reactroot="" style="z-index: 999999999; position: fixed; right: 0px; bottom: 0px; border: 0px; background-image: none; transition: width 200ms cubic-bezier(0.25, 0.25, 0.5, 1), height 200ms cubic-bezier(0.25, 0.25, 0.5, 1); -webkit-trans...

10 Errors
0 Warnings
0 Notices 
```

它实际上做的非常漂亮:它启动了一个“无头”浏览器，在它的 [Phan tomJS](http://phantomjs.org/) 过程中浏览你的网页，然后分析它是否存在漏洞。根据[在你的网站上实现临界 CSS](https://dev.to/gaijinity/implementing-critical-css-on-your-website-37d5-temp-slug-6012917)的文章，这与临界 CSS 是如何产生的很相似。

在任何情况下，这都是相当多的错误，所以让我们一个接一个地处理它们！

```
 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nav-menu > div > div:nth-child(1) > div > div > a
   └── <a href="/"> 
```

这个问题可以通过添加一个`homepage`作为``的子元素来解决，这个子元素在全局站点标题中内联显示，所以有一些文本供屏幕阅读器解析。屏幕阅读器得到的链接应该说*链接会带他们去哪里*，或者*点击链接会做什么*，而不是描述链接里面的东西是什么(在这种情况下，是一个标志)。

想象一下，有人大声给你读网页，如果他们说“链接纽约时报 107 徽标”，你根本不知道点击它会有什么效果。如果他们说的是“链接主页”,那么你会得到它。

解决这个问题的另一种方法是将文本放在`<a>`标签中，这是肉眼看不到的，但是屏幕阅读器会看到。我们可以使用 Twit ter Bootstrap 的 CSS 通过`<span class="sr-only">`来实现这个功能:

```
 .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    border: 0;
} 
```

这样，屏幕阅读器就有东西可读了！

```
 • Error: This element has insufficient contrast at this conformance level. Expected a contrast ratio of at least 4.5:1, but text in this element has a contrast ratio of 2.93:1\. Recommendation: change background to #3c3d3f.
   ├── WCAG2AA.Principle1.Guideline1_4.1_4_3.G18.Fail
   ├── #nav-menu > div > div:nth-child(3) > ul > li:nth-child(2) > a
   └── <a href="/blog" class="nav-link">Blog</a> 
```

这说明我们的菜单链接和标题背景色之间的对比度不够高。建议将其从`#58595b`改为`#3C3D3F`以增加对比度。这是为了帮助视力有问题的人提高阅读能力。

因为这些是纽约大学的品牌颜色，所以在设计过程中，可以通过应用文章[中的提示来解决这个问题。](https://www.w3.org/WAI/gettingstarted/tips/designing) 

```
 • Error: This text input element does not have a name available to an accessibility API. Valid names are: label element, title attribute.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.InputText.Name
   ├── #searchbox
   └── <input type="text" id="searchbox" placeholder="search" autocomplete="off" class="autocomplete-input">

 • Error: This form field should be labelled in some way. Use the label element (either with a "for" attribute or wrapped around the form field), or "title", "aria-label" or "aria-labelledby" attributes as appropriate.
   ├── WCAG2AA.Principle1.Guideline1_3.1_3_1.F68
   ├── #searchbox
   └── <input type="text" id="searchbox" placeholder="search" autocomplete="off" class="autocomplete-input"> 
```

这意味着我们用于站点搜索的`<input>`元素缺少用于屏幕阅读器的`aria-label`属性。这可以通过将它们添加到我们的 [vue2-auto com plete](https://github.com/nystudio107/vue2-autocomplete) Vue 组件中来解决。

```
 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(1)
   └── <a class="social" href="mailto:info@nystudio107.com"><i class="icon-mail-alt"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(2)
   └── <a rel="nofollow" class="social" href="https://www.facebook.com/newyorkstudio107"><i class="icon-facebook"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(3)
   └── <a rel="nofollow" class="social" href="https://twitter.com/nystudio107"><i class="icon-twitter"></i></a>

 • Error: Anchor element found with a valid href attribute, but no link content has been supplied.
   ├── WCAG2AA.Principle4.Guideline4_1.4_1_2.H91.A.NoContent
   ├── #nys-footer > div > div > div > div:nth-child(2) > a:nth-child(4)
   └── <a rel="nofollow" class="social" href="https://github.com/nystudio107"><i class="icon-github-circled">...</a> 
```

这些都是指屏幕底部的社交图标，它们是 [Fontel lo](http://fontello.com/) 的定制图标字体 cour tesy(通过我们的 [Gulp](https://www.npmjs.com/package/gulp-fontello) 任务在 ed 生成 er)。与流行的观点相反，图标字体可以与可访问性一起工作。

我们只需要把链接改成这样:

```
 <a rel="nofollow" class="social" href="https://www.facebook.com/newyorkstudio107">
    <i class="icon-facebook" aria-hidden="true" title="Facebook"></i>
    <span class="sr-only">Facebook</span>
</a> 
```

属性`aria-hidden`使屏幕阅读器跳过该元素，属性`title`给可视人一个鼠标悬停工具提示。然后`<span class="sr-only">`使用来自 Twit ter Bootstrap 的 CSS 提供文本给屏幕阅读器解析:

```
 .sr-only {
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0,0,0,0);
    border: 0;
} 
```

这就是全部，我们现在有了很好的社交图标。

```
 • Error: Iframe element requires a non-empty title attribute that identifies the frame.
   ├── WCAG2AA.Principle2.Guideline2_4.2_4_1.H64.1
   ├── #Smallchat > iframe
   └── <iframe data-reactroot="" style="z-index: 999999999; position: fixed; right: 0px; bottom: 0px; border: 0px; background-image: none; transition: width 200ms cubic-bezier(0.25, 0.25, 0.5, 1), height 200ms cubic-bezier(0.25, 0.25, 0.5, 1); -webkit-trans... 
```

不幸的是，这个问题我们无法解决……因为它来自我们用来允许网站访问者通过 [Slack](https://slack.com) 联系我们的[小型聊天](https://small.chat/)库。然而，我们*所能做的*是联系小聊天的作者，并指出访问漏洞问题，以便他们可以修复它们。然后*各人*受益。

## 自动化接入能力测试

作为一个节点模块，很酷的一点是我们可以将它集成到我们的前端工作流程中，并自动匹配测试。就拿这个`a11y`大口任务来说:

```
 // Process data in an array synchronously, moving onto the n+1 item only after the nth item callback
function doSynchronousLoop(data, processData, done) {
    if (data.length > 0) {
        const loop = (data, i, processData, done) => {
            processData(data[i], i, () => {
                if (++i < data.length) {
                    loop(data, i, processData, done);
                } else {
                    done();
                }
            });
        };
        loop(data, 0, processData, done);
    } else {
        done();
    }
}

// Run pa11y accessibility tests on each template
function processAccessibility(element, i, callback) {
    const accessibilitySrc = pkg.urls.critical + element.url;
    const cliReporter = require('./node_modules/pa11y/reporter/cli.js');
    const options = {
        log: cliReporter,
        ignore:
                [
                    'notice',
                    'warning'
                ],
        };
    const test = $.pa11y(options);

    $.fancyLog("-> Checking Accessibility for URL: " + $.chalk.cyan(accessibilitySrc));
    test.run(accessibilitySrc, (error, results) => {
        cliReporter.results(results, accessibilitySrc);
        callback();
    });
}

// accessibility task
gulp.task("a11y", (callback) => {
    doSynchronousLoop(pkg.globs.critical, processAccessibility, () => {
        // all done
        callback();
    });
}); 
```

这只猪放弃了在[的一篇文章中的`pkg.globs.critical`设置，并使用了在](https://dev.to/gaijinity/a-better-package-json-for-the-frontend-1gal-temp-slug-5184040)[的文章中的`pkg.globs.critical`对象中已经有的 URL。](https://dev.to/gaijinity/implementing-critical-css-on-your-website-37d5-temp-slug-6012917)

我们已经有了每个主要模板的 URL，我们的网站使用这些模板来进行我们的批评，我们可以再次使用它们来对我们网站上的每个模板进行检查！

为此，您需要做`npm install pa11y -D`或`yarn add pa11y -D`来将`pa11y`添加到我们的 pro jec t 的`package.json`中，然后我们就可以开始了！

所以现在我们只需做`gulp a11y`，它将测试我们整个网站的访问漏洞问题。不错！

## 把乒包起来

就像你使用其他审计工具一样:你得到一个需要修复的问题列表，解决它们，然后重新运行测试。板条，冲洗，重复，直到你计划解决的所有问题都被解决。

从最佳实践的角度来看，一件非常好的事情是，如果我们已经按照[现代 SEO:蛇油与物质](https://dev.to/gaijinity/modern-seo-snake-oil-vs-substance-4l5k-temp-slug-8632935)的文章解决了 SEO 最佳实践，我们已经解决了许多访问漏洞问题。例如，SEO 和 acces 都希望图片有`alt`标签。很好。

想想也有道理；搜索引擎是基本的屏幕阅读器。但是很高兴看到这些技术融合在最佳实践的保护伞下。

就是这样！解决这些问题一点也不坏，而且我们很可能让一些人对我们的网站产生了很大的不同。

[![Freedom Through Web Accessibility](img/78f855f68732c196c692298616b83bdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_aN3VJas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/freedom-through-web-accessibility.jpg)

访问权限并不是很难解决的事情，特别是如果你将它作为设计和开发过程的一部分，并使用类似`pa11y`的工具来帮助你的话。

与任何事情一样，在构建项目时考虑可访问性要比追溯性地添加它容易得多。所以让它成为你设计和开发过程的一部分。

同样值得一提的是， [Pa11y](https://github.com/pa11y) 也有一些基于它的很酷的项目，如[pa1y dashboard](https://github.com/pa11y/dashboard)和[pa1y Web service](https://github.com/pa11y/webservice)，用于对网站的访问权限进行更多的自动监控。如果您提供 GUI 工具，请查看[Chrome access si bility developer Tools](https://chrome.google.com/webstore/detail/accessibility-developer-t/fpkknkljclfencbdbgkenhalefipecmb?hl=en)和 [HTML_ Codesniffer](http://squizlabs.github.io/HTML_CodeSniffer/) 。

去做一些包容性的网站吧！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计