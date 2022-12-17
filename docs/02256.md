# 使用降价来提高您的生产力。

> 原文：<https://dev.to/issuehunt/boost-your-productivity-using-markdown-1be>

## 什么是降价？

> Markdown 是一种具有纯文本格式语法的轻量级标记语言。它被设计成可以使用同名工具转换成 HTML 和许多其他格式。
> 来自[维基百科](https://en.wikipedia.org/wiki/Markdown)

介绍降价格式⛷

## 1。标题

```
# h1
## h2
### h3
standard 
```

Enter fullscreen mode Exit fullscreen mode

[![heading](img/791fca27ff3bc4bdaa5f057ada12c245.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PG8q8eti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z85ljqb25ipweosug5jo.png)

## 2。强调

```
*Italic type*
**Bold**
~~Negative~~ 
```

Enter fullscreen mode Exit fullscreen mode

[![emphasis](img/52e3e2ee44c089eaf22d9e2a9d21f4b9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DrZbCjqe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c4jdk7glw593wef9gg2y.png)

## 3。折叠

折叠长句。

```
<details><summary>Boostnote is a notepad corresponding to markdown notation, which is a tool for organizing and sharing information.</summary>
- Features - <br>
· Search function to find memos in one shot
· Supports markdown notation <br>
· Support for Mac, Windows, Linux, iOS, Android <br>
· Export and import to Plain text (.txt), Markdown (.md) format <br>
· Supports PDF saving <br>
· Can be used offline <br>
· Synchronize to dropbox etc. with setting <br>
· Supports theme colors and numerous fonts <br>
</details> 
```

Enter fullscreen mode Exit fullscreen mode

[![fold-1](img/43edc71db674c601b2510ed952a6e34d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bgZPQPLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3t5cv83ii65lnprjo4uu.png)
[![fold-2](img/7d1e8a2f6b52ad8e8f608de1a65883d3.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--caRKNHSt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8x1l3ggget7yqqwjovem.png)

## 4。目录

```
- List 1
- List 2
* List 3 
```

Enter fullscreen mode Exit fullscreen mode

[![list](img/cf6f769fbf3932f90464e2f3e3d92d06.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZHfLK7Gy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5otbla8dwfgwfvm623bt.png)

## 5。环

在左边放一个文本，在右边放一个 url。

```
[Boostnote](https://boostnote.io) 
```

Enter fullscreen mode Exit fullscreen mode

[![link](img/102344ed15dabce6935738ba3a17a27c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hDzLgGMi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/latuh50tdcjyp9lfwyte.png)

## 6。复选框

```
- [x] Task 1
- [ ] Task 2 
```

Enter fullscreen mode Exit fullscreen mode

[![check](img/9da6e9667e2eb40a2cbbc3ab69671d38.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CCYO2ru_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6i7o3659tbzn1p8s174l.png)

## 7。行情

```
> Quotation
> Quotation Quotation 
```

Enter fullscreen mode Exit fullscreen mode

[![Quote](img/e47436e1329b22f14650bbe36956ba21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--p6kKkGvf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p532jx86ez6axbx3jmvn.png)

## 8。水平线

```
* * *
***
-------- 
```

Enter fullscreen mode Exit fullscreen mode

[![horizontal](img/d75a324e6978e04de0585fadaa27ce7a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kaxT6vMT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d08vl263tycxm42aoi0j.png)

## 9。图像

```
![Image title](https://boostnote.io/assets/img/logo.png) 
```

Enter fullscreen mode Exit fullscreen mode

[![image](img/40c630617964cc8e729d9274be5dfa35.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cm8waIno--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mn2yc6d2thkutmcybof6.png)

## 10。源代码

```
Render: function () {
  Return (
    <div className="commentBox">
      <h1> Comments </h1>
      <CommentList data={this.state.data} />
      <CommentForm onCommentSubmit={this.handleCommentSubmit} />
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[![code](img/d122b9e45ef94d340799cd26840ddc2e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W25WOPLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2m3740q6ekvi961mgt7h.png)

## 11。桌子

```
|Fruits|Price|
|:--|:--|
|Apple|1$|
|Grapes|4$|
|Orange|2$|
|Lemon|1$|
|Peach|3$|
|Melon|20$| 
```

Enter fullscreen mode Exit fullscreen mode

[![table](img/2ed71d54be9cba46163bfc308f87cec9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jDe28Hkx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ohhocqsxbbydpeje4f8x.png)

* * *

这些是基本的降价格式。
除此之外，你还可以在 [Boostnote](https://boostnote.io/) 中设置如下复杂格式。

## 乳胶

数学格式化。

```
$$$
\mathrm{e}^{\mathrm{i}\theta} = \cos(\theta) + \mathrm{i}\sin(\theta)
$$$ 
```

Enter fullscreen mode Exit fullscreen mode

[![latex](img/cc1ec8bce5a8d98314861d9c1e1d5ea0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9NSbY0Jv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sw1erl3npefy8k8mo3lw.png)

## 流程图

```
st=>start: Start:>http://www.google.com[blank]
e=>end:>http://www.google.com
op1=>operation: My Operation
sub1=>subroutine: My Subroutine
cond=>condition: Yes or No?:>http://www.google.com
io=>inputoutput: catch something…
st->op1->cond
cond(yes)->io->e
cond(no)->sub1(right)->op1 
```

Enter fullscreen mode Exit fullscreen mode

[![flowchart](img/284938b7cbd25d194fc5deaa80e27642.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QPJSJmGZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zlk5nkr1ppz6zlf60d7e.png)

## 序列

```
Title: Here is a title
A-> B: Normal line
B -> C: Dashed line
C -> D: Open arrow
D -> A: Dashed open arrow 
```

Enter fullscreen mode Exit fullscreen mode

[![sequence](img/09dce792417220ca00c73db41018b3e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6apa2l05--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pysmuc1ozzjjrzi1e79t.png)

* * *

# 为团队提振备注

我们已经公布了一项新的协作维基服务，名为 [BoostHub](https://boosthub.io/) 。

它将是 Boost Note 的协作版本，但功能更强大。该功能称为“自定义块”，允许您定制 react 组件，与 Github 和 Trello 等各种第三方应用程序 API 集成，并将其嵌入 MDX 等 markdown 内容中。

要了解更多信息，请查看这张幻灯片。

[![how-to-use-boosthub](img/bc0c91fd8c6f5085678febaa8dde4f43.png)T2】](https://docs.google.com/presentation/d/e/2PACX-1vTXPf0PLDQs8e6sLpyyQ43r59RIc-X_ez7YzgW4nKH-klWfhcAaJQkhOwS8W0WbgpiqspV3Gvbx_78S/pub?start=false&loop=false&delayms=3000)