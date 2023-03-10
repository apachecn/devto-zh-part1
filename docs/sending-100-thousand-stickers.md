# 我们在世界各地邮寄了 100，000 张贴纸，一路上犯了一百万个错误

> 原文：<https://dev.to/thepracticaldev/sending-100-thousand-stickers>

为了庆祝我们在 2 月份的第 100，000 名 Twitter 粉丝，我们向全球用户免费发送了 dev .贴纸:

> ![unknown tweet media content](img/ec92d48d1f5eb92b5099b4971ab98976.png)![The Practical Dev profile image](img/cd8bc9e57a1ef8164ccae9c8dd8a498a.png)实用 dev[@ the practical dev](https://dev.to/thepracticaldev)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)‌‌
> 如约而至—免费贴满所有
> 
> [dev.to/freestickers](https://t.co/AijhZVWdFm)
> ‌‌2017 年 2 月 22 日下午 16:29[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=834439977220112384)

感觉这是一个很好的机会来回报我们的忠实追随者，给他们一点东西，把他们和 DEV 社区联系起来。另外，寄一些信封能有多难？

## 问题 0:规模

在发布这条推文的几分钟内，我们就被来自世界各地的注册信息淹没了；一天结束时，我们收到了来自不同国家的贴纸包请求。

[![](img/55de1703fe7b5a9f5e541840dc1b0d62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XUC9CaMI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DDE2CE8XgAAq6Z8.jpg)

看着这些订单堆积如山令人兴奋...但也有点令人不安，因为那时我们的贴纸分发范围是到处分发给与公司有联系的各种人。现在，我们不得不弄清楚几十个国家的邮政特点，获得数百磅(或者更确切地说，考虑到我们的全球客户)的原材料，并在我们已经繁忙的日常开发中挤出近 1000 个工时。这项我们显然低估了的重大任务一开始就不顺利...

## 问题 1:报名表无效

我们的注册页面的原始版本有一个错误，导致任何选择男女通用的 t 恤尺寸的表格注册为无效，信息从我们的数据库中删除。更糟糕的是，我们没有向用户暗示他或她的表格还没有寄出，所以很多用户都没有意识到他们的贴纸请求已经消失得无影无踪。我们只是在提交了几百份表格后才意识到，没有男女通用的尺寸(最受欢迎的尺寸)。

[![](img/60b67a1991f14a10a063fafe86f720f7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6t-eS14G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/C_98HM7VYAIMiam.jpg)

这导致了一个有点尴尬的折中方案，即联系那些可能已经和 T2 签约的人，敦促他们重新填写表格。即使我们修复了这个`unisex_m`错误，我们的表单仍然没有办法...

## 问题 2:验证地址

在这个夏天开始的时候，[dev.to/freestickers](https://dev.to/freestickers)将地址验证嵌入其中，这样当你填写答案的时候，验证过的地址就会被建议出来:

[![](img/d186c04e506a2a272aed67d2cc858c78.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zxEIZB6w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5s33rm18a4fdo653laex.png)

但是最初我们推出了一个更简单的自由格式的模板，让用户承担提供有效地址的责任。

现在你可能认为大多数人都知道他们住在哪里，但是不同的国家有不同的标准和惯例，不一定完全符合我们偏向美国的地址/城市/州的格式。因此，当我们得到类似`DespuÃ©s de hogares crea, segunda entrada mano derecha, Ãºltima casa verde`(根据谷歌翻译，关于右边最后一个绿色房子的一些东西)的输入时，我们认为我们必须验证这些地址。

[![](img/b3c8abe099bd1d96a1dd64695ebb7fbd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---CTDXtUz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DB9axcoXgAAZbsM.jpg)

我们求助于在线地址处理器 SmartyStreets 来帮助我们清理和验证用户输入的信息。令我们高兴的是，它报告说我们的美国地址几乎 100%有效，但我们的国际收益率接近 80%，并包括一些令人困惑的裁决，如“部分”和“模糊”。理论上，我们可以要求这些用户重新输入他们的地址，但是我们丢失了他们的 id，因为...

## 问题三:版本控制

最初，我们假设这种努力可以由单个 Excel 文件来完成，但是在验证了几批用户之后，很明显这是一种不可行的方法。每个用户不仅有一行用于存储他们提供的运输信息，还有一行用于存储稍加修改的版本以满足 SmartyStreets 的要求，还有一行用于存储验证器的输出。将美国和非美国用户的不同数据集混合在一起，我们简单的 Excel 文件突然变成了半打表格。很快，如果不召唤可怕的死亡之轮，我们就无法改变一个细胞。

[![](img/96728ba7048f574da74ec278f92452c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EpjUdY3a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DB1Ocr7XkAAgH4D.jpg)

不幸的是，当我们意识到单个 Excel 文件是不够的时候，我们已经多次将数据复制和粘贴到 SmartyStreets 和我们的标签打印机中，而没有带着用户 id(我们的主键)。因此，即使我们有一个未经验证的地址列表，我们也必须将它们与数据库中其他稍有不同的列进行匹配，以便检索 id。留给我们的是一堆重叠的 Excel 文件，其中只有一部分有用户 id:

> ![unknown tweet media content](img/8e5c1897d23e0be19ef5ad636a11749b.png)![Walker Harrison profile image](img/f4b58a27a74f05ac5a8a94fcaf6721e1.png)沃克哈里森@ walkwearscrocs![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)如果有人想知道傻逼电子表格版本控制是什么样子的...2017 年 3 月 23 日下午 16:05[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=844943213257469953)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=844943213257469953)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=844943213257469953)

即使我们有完整的、经过验证的地址，我们也必须处理...

## 问题四:字符编码

在只有一小部分字符串数据没有被[A-Z][0-9]覆盖的情况下，对字符进行编码的方式可能是一个微不足道的选择。但是当你给墨西哥的阿瓜市、乌克兰的奥什、德国的门兴格拉德巴赫、巴西的圣若桑和中国的奥什发邮件时，编码就是真实位置和纯乱码之间的区别。

[![](img/16a2ba129810a181f4594ce97b302c2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--02TIjLu4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DBH0T2tUAAY0HKh.jpg)

但是我们直到事后才把这些联系起来。为了打印地址，我们在 Excel 中打开数据，复制并粘贴到 SmartyStreets web 应用程序中，将结果复制并粘贴回 Excel 中，保存为 CSV 格式，在 R 中进一步处理，然后加载到我们的梁永能标签打印软件中。到那时，它已经使用各种标准被编码和重新编码了六次，使得一些地址处于完全不连贯的状态:

> ![unknown tweet media content](img/87e94bcc9b7a4a5a53c673f85d2cbd89.png)![Walker Harrison profile image](img/f4b58a27a74f05ac5a8a94fcaf6721e1.png)沃克哈里森@ walkwarescrocs![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)天哪我在想为什么这些地址不会验证20:28PM-24 2017 年 5 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=867477449285750785)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=867477449285750785)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=867477449285750785)

许多地址不得不被废弃，因为我们认为邮递员不知道门牌号码在哪里。而且，正如你所料，有很多地址介于完美和难以辨认之间，我们把它们放入了未知的邮政信箱，祈祷好运。当他们到达时，我们的用户很感激，尽管有点好笑:

> ![unknown tweet media content](img/ec6ce247eeab508fc779ff0fa9868bdf.png)![Chrales 🦔 profile image](img/236d52cc2f258c65c5587bd1197b3c6d.png)Chrales🦔@ loderunnr![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ thepractical dev](https://twitter.com/ThePracticalDev)[@ dot conferences](https://twitter.com/dotConferences)[@ github](https://twitter.com/github)虽然有人在文本编码上失败了，😜2017 年 06 月 02 日上午 07:36[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=870544745008857090)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=870544745008857090)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=870544745008857090)

即使我们成功地对每个字符进行了清晰的编码，有时我们仍然需要担心...

## 问题 5:格式化地址

同样，我们对国际邮件标准的无知被证明是一个主要的弱点。按照美国标准，我们的表单要求两个地址行和一个城市和州。但是有更多的方法来定义我们边界之外的物理位置，包括地点、省、地区、区、地带和区域。SmartyStreets 在其回复 CSV 中填写了这些字段，但我们需要一个一揽子策略来将这些字段转换为运输标签——如果只有少数收件人有地址行，我们就无法将“地区”映射到地址行。

[![](img/5115872265a2543202023ac04a9741cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ubwAazPT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/DD_3EeBXgAIY1wp.jpg)

此外，两个地址行在许多地方是不够的，有时验证器会返回多达六七个地址字段。由于我们无法合理地在一个运输标签上容纳这么多行，我们不得不编写一个 janky Excel 公式，为地址行创建一组镜像字段，这些字段不仅仅是城市或邮政编码的重复，然后将它们连接成一个地址行。

这奏效了...算是吧。偶尔，这些“包罗万象”的地址行会变得很长，以至于标签制造商不得不将文本做得小得难以阅读:

[![](img/8b5a4f60a9d9e6d5daca0ca8171f8794.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Gapqb2jy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqc4ggxsw5t0esoi8e9w.jpg)

如果一个地址幸运地通过了验证、编码和格式化，它仍然需要被打印并粘贴在一个贴满标签的信封上，这需要...

## 问题六:包装材料

一些原材料很重，但很容易获得:大箱的信封、纸板插页、标签和贴纸被送到我们的门口，然后储存在我们有限的办公空间可以容纳它们的任何地方——书架上、冰箱旁边，或者(比我们愿意承认的更常见)随便堆放在房间中央。

其他东西看起来不起眼，但很难买到，比如数千枚月球邮票，必须由持有身份证的人亲自到位于城市另一边的纽约市美国邮政总局领取。

[![](img/d343952404754e6bcdb39c8ed21365dc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1quZhvGF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/C8rApZTXUAEFIzG.jpg)

然后我们必须把这些不同的部分变成可邮寄的贴纸包。开发人员可以在业余时间制作一些信封。打包一万件东西是我们需要帮助的。几个朋友自愿花了几个小时，但是为了在合理的时间内完成工作，我们需要雇佣一些劳动力。我们求助于 TaskRabbit，在那里我们招募了具有高度组织能力的人。我们的任务负责人很好——友好的人们一次努力工作几个小时——但是在办公室里陌生人比我们多是我们必须习惯的事情。

最终，包装是这个过程中压力最小的方面之一，我们中的许多人都从盯着显示器的状态中解脱出来，听听音乐，时不时地帮帮忙。但有几天，我们不得不要求每个人都在家工作，因为打包过程将我们朴素的办公室变成了一场丢弃标签和邮票的龙卷风:

[![](img/7fc5f1e476fea404f7563aa6a805c65f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D5fYQFQK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vz5e76qx3f5xu1dxjc17.png)

当然，照片中的所有袋子都必须去邮局，这就让我们...

## 问题 7:发货

有时候，我们会安排收件人来清理我们办公室里即将寄出的贴纸包，但当我们真的想尽快寄出时，除了步行五六个街区到当地邮局之外，没有其他选择。你有没有背过装满一千个信封的袋子？太重了。

此外，我们第一次把这些袋子拖过来时，柜台的那个女人告诉我们，我们的国际信件不会寄出去，因为我们使用了三个字母的代码，而不是拼出国家名称。事实证明她错了，但有一两天，我们怀疑自己是否把每一封寄出的邮件都搞砸了。

[![](img/31b18bfd635940af668edfee2d8436b5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wA2iMG2i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/C8l9YmtXcAEbPla.jpg)

即使我们没有因为没有写下国名而受到惩罚，邮局的员工通常也会给我们一些疑惑的眼神。谁能责怪他们呢？肩上扛着装满黑色信封的巨大袋子，我们呻吟着，看起来就像是圣诞老人和死神的怪异组合！

而且，正如你所想象的，如果你发出了许多不完全有效、编码不良、格式不规范的信件，你会得到许多回复:

[![](img/40738194e7d50e1f7554bf350d8c1d4f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jVScwoV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/shy1rnziudsfc4xxr0aq.JPG)

不过，有时这些信会因为其他原因被退回。你知道美国暂停了对危地马拉的邮政服务吗？我们没有！

尽管有这些相对的失误，我们很高兴地报告，超过 95%的信件到达了它们的最终目的地。尽管如此，仍然有很多人非常耐心地等待他们的包裹，但却没有收到。这就是为什么我们想道歉，并通过解释来弥补...

## 如果贴纸从未送达，如何领取

我们已经结束了新用户的免费贴纸活动。但是，如果你已经是一个申请了贴纸但从未收到过贴纸的开发者用户，你可以访问[dev.to/freestickers](https://dev.to/freestickers)并(重新)验证你的地址，这样我们就可以最终将你的贴纸发送给你。

如果你是在 7 月份订购的，在标记自己未完成并重新要求贴纸之前，给它一个星期，因为我们刚刚发出一批，他们可能正在路上。

注意:虽然我们可以知道是谁最初请求了贴纸，但我们无法验证他们是否收到了，所以我们依赖用户诚实地回答他们是否收到了自己的贴纸。此外，供应品*是有限的，所以任何想得到第二套的人实际上只能从没有任何供应品的人那里得到。ðŸ˜"*

* * *

最后我要说几句谢谢。首先我们要感谢赞助我们原创贴纸批次的公司: [Clarifai](https://www.clarifai.com/) 、 [Circle CI](https://circleci.com) 和 [Cloudinary](http://cloudinary.com/) 。他们最初的投资确实有助于这场运动的开展。

我们还要感谢印制了我们所有可爱贴纸的贴纸员。我们尝试了一堆不同的贴纸公司，并得出结论，他们有最好的质量和服务的结合。

我们要感谢 dev.to 用户耐心地等待他们的贴纸，其中一些从未到达。事实上，这是一项昂贵的努力，从原材料，到各种运输困难，到从包装信封到回复主题为`stickers???`的电子邮件等一切事情上耗费的劳动力。

但是看到你们把贴纸的照片贴在你们的笔记本电脑上(或[摩托车头盔](https://twitter.com/_ado/status/878542824949653504)或[保险杠](https://twitter.com/MacRuskov/status/873516683197132803)或[车门](https://twitter.com/MohammadDurrani/status/872940197675245568)或[四肢](https://twitter.com/michaelsoolee/status/872479835733602317)或[铭牌](https://twitter.com/sahildua2305/status/870631305523998720)或[自行车](https://twitter.com/thibmaek/status/870023385908207617)或[水瓶](https://twitter.com/richdotjs/status/869744832234700800)或[充电器](https://twitter.com/d3vHero/status/882695717344743424)或[文件柜](https://twitter.com/grmpyprogrammer/status/849703315726381056)或[床头柜](https://twitter.com/antillesheel/status/849347878220910592)或[办公桌](https://twitter.com/CrandellDesign/status/879397402540691458)或[服务器架)ðŸ'•](https://twitter.com/brunn3is/status/877623921633366017)