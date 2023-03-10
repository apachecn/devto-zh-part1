# 分析一年来通过 SQL、Spark、Spotify 和 Databricks 播放的电台歌曲

> 原文：<https://dev.to/paulleclercq/analyze-one-year-of-radio-station-songs-aired-with-sql-spark-spotify-and-databricks>

*注:本帖原载于[媒体](https://medium.com/@polomarcus/analyze-one-year-of-radio-station-songs-aired-with-sql-spark-spotify-and-databricks-835fcf73df6#.7o1uwd9vc)T3*

每当我开车或编码时，我都会听音乐，因为这种情况经常发生，为了找到新歌，我会听收音机或听 Spotify 的 discover weekly 播放列表，*这让我喜欢周一(因为他们每周一发布)*

一家名为[mediam trie](http://www.mediametrie.fr/radio/)的法国老派机构分析广播电台的歌曲。自从几年前我看到他们的研究(*，我再也找不到了*)，我就一直痴迷于创造自己的研究。

[![](img/e52d2cceb13a0553683343247e3c8f70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--krZsrAkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Aaic7vTL46SYNhhkdHgir9Q.png)T3】

<center>*Mediametrie's image you can find online â†’ old school*</center>

本文将通过 *fun* SQL 查询呈现 2016 年法国 4 家主要电台，然后我们将每首歌曲连接到 Spotify API 以创建电台的音乐档案。

我们将使用 [Databricks 社区版本](https://community.cloud.databricks.com/)来可视化我们的数据。[所有 SQL 查询和所有结果都可以在这个笔记本上找到。](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6937750999095841/1807085967979471/6197123402747553/latest.html)这里是本文的“后台”，如果我们可以说，神奇的事情就发生在这里。

**Protip** :不要错过文末的奖金

### 电台简介

我们都有一个最喜欢的电台，我的电台是新星电台，因为他们的多样性和幽默，作为一个嘻哈迷，这是唯一一个我们可以听到 T2 嘻哈歌曲的国家电台

[![](img/c1f7e1c3ef516a12b85d48d1f53b71e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hlHPRX4s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AnKeR6DQGjRIQj5a7.png)

Radio nova 在 2016 年 9 月拥有[1.4%的听众(PDF 从 Mediametrie 下载)](http://www.mediametrie.fr/radio/communiques/telecharger.php?f=b132ecc1609bfcf302615847c1caa69a)。

[![](img/63959befb7a8a4373f6af6c798340b49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IKSpW6_Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A_g363O12zm0KugNa_qcQ0g.png)T3】

<center>*Most 5 broadcasted songs on Nova in 2016*</center>

为了了解一个电台是如何成为第一名的，我们还将分析排名第一的音乐电台，名为 [NRJ](http://www.nrj.fr/) ，它拥有 10.8%的听众和另外两个:维珍(5%)，我们将看到，听起来像 NRJ，和天空摇滚(6%)，*不要介意这个名字，它是一个说唱电台…哈哈*

[![](img/12fac5160c309de2e9b82adca7b555f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PCTh_KHn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AiKd8P0iu3oe6C_iOYeYeAA.png)T3】

<center>*Most 5 broadcasted songs on NRJ in 2016*</center>

主要的问题是，在我们比较了这些收音机之后，我们是否应该根据 NRJ 的分析给 Nova 电台一些如何成为第一的提示？你说呢，新星？向最优秀的人学习，对吗？！

### 获取电台的歌曲数据

[![](img/c86412d6322d8934eb03aa93fdc1ef3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nHZQ92jV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Al8ljCalKR3Tpnpp9ZM9Kbg.png)T3】

<center>*“What was this title?” Nova page*</center>

为了提取歌曲列表、艺术家、歌曲标题和时间戳，我们将解析每个单选按钮“这首歌是什么？”除了 Skyrock，[有一个方便的 RESTful web 服务](http://skyrock.fm/api/v3/sound?search_date=2016-08-15&search_hour=04:59)。

提取的每首歌曲都将被转换成这个歌曲类，以便使用(Spark) SQL:
进行查询

```
 Song(timestamp:Int, humanDate:Long, year:Int, month:Int, day:Int, hour:Int, minute: Int, artist:String, allArtists: String, title:String) 
```

Enter fullscreen mode Exit fullscreen mode

2016 年收集了 30 万次广播:

*   Nova : 95K 播放 5000 首不同的歌曲
*   NRJ : 50K 播放 800 首不同的歌曲
*   维珍:6 万张 1200 首不同歌曲的唱片
*   Skyrock: 100K 播放 1000 首不同的歌曲

每首歌曲都以 [parquet 格式存储](http://spark.apache.org/docs/latest/sql-programming-guide.html#parquet-files)只提取一次数据(*不客气 radios servers :p* )和[加速 SparkSQL 查询](https://blog.cloudera.com/blog/2016/04/benchmarking-apache-parquet-the-allstate-experience/)。顺便说一句，如果你对这个文件感兴趣，我可以用 CSV 或拼花格式导出给你。

记住， [Spark doc](http://spark.apache.org/docs/latest/programming-guide.html#rdd-persistence) 说，如果你不得不一次又一次地使用同一个 SQL 表(或数据集/数据帧),提高查询速度的最好方法是将它们缓存在内存中(*感谢 6Go RAM 服务器的数据块！*同`dataframe.cache()`法。

现在让我们开始分析吧！

### 白天有多少首歌？

有些日子没有被收音机的历史系统记录下来，所以真实的数字应该会高一点。

[![](img/4174cb5cfc0babbed06832ae1d2060ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oP_559X8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A0tYTwHCCN6r2nq6EqJojdA.png)T3】

<center>*Songs broadcasted by day*</center>

有趣的是，两家电台在夏季播放了更多的歌曲(如果我们不考虑 8 月蓝色新星电台的一周故障)，这肯定是由于暑假。他们一整年都做得很好，所以，我想可以休息几天。

我们可以看到，Skyrock 和 Nova 每天播放相同数量的歌曲，而 NRJ 和 Virgin 则少一点，当然是因为更多的脱口秀或未跟踪的 DJ 夜间节目。

### 一天有多少首不同的歌？

真正的区别在于播放的不同歌曲的数量，自己看看每天不同曲目的数量:

[![](img/62d1ef81e3f71aa5439d25b0e07738aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--01onFSsp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AOmx2QwvONBJMBLmUOtD5Pw.png)T3】

<center>*Different songs by day*</center>

更多的主流电台，如 NRJ、Virgin 和 Skyrock 每天播放 100/120 首不同的歌曲，而 Nova 每天播放 280 多首。如果你想发现更多的歌曲，它显然在 Nova 上。

### 一个月有多少首不同的歌？

如果我们看看每月不同的歌曲，收音机之间的差距就更大了。

[![](img/b8fa32f6989c433b957427841c0d9f7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XsSQr2rb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A7WL3--Hb00WNPvjeM5if8w.png)T3】

<center>*Different songs by month in 2016*</center>

### 各电台播放的前 10 名标题

看看一年中“热门歌曲”的播放情况是很有趣的。

我们可以注意到夏季的热门话题:

*   [Kaytranada](https://open.spotify.com/track/0EM0yABJzbFOvZQkfvuvCy) 代表新星
*   [安立奎·伊格莱希亚斯](https://open.spotify.com/track/6YZdkObH88npeKrrkb8Ggf)NRJ
*   [肯特·琼斯](https://open.spotify.com/track/27PmvZoffODNFW2p7ehZTQ)和[德雷克](https://open.spotify.com/track/1xznGGDReH1oQq0xzbwXa3)为天石效力
*   [伊玛尼](https://open.spotify.com/track/1ZdWNpOXCJT1nmt40UuxWS)和[昆](https://open.spotify.com/track/0cAuqPI1R8RlFsXXWWO039)为处女。

我们可以看出，播放次数最多的歌曲大多是在夏季播放的。所以，艺术家们，放聪明点，在二月到六月间发布你的歌曲，是有更多的机会成为第一，还是有更多的人因为听了太多遍而讨厌你的音乐？

#### 新星

[![](img/7330a5feb0cb8497d6ff9b6a04193438.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g84xxPhM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A9mum9beXiQln5uSyfy2yww.png)

#### NRJ

[![](img/3bd8ca04fb42bd64681e9d6404db9e0a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7LCd76g_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AawiIMX1Bwu5rdS-_rkeeYg.png)

#### 长石

[![](img/6ae7cc647b2257c80fe7d24fc50c700a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gYmLaGLJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AZ4KvMZzWL7ltwtjwMM5Nkw.png)

#### 处女

[![](img/35f2515b200dbef0525f17a1db5e943e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7kJQt82r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AnZMYiYJttXEOaV7KugtjHA.png)

### 按天音乐百分比

如果我们取每天平均播放的歌曲和一首歌曲的平均持续时间，3.30 分钟，我们可以猜测每天音乐的百分比。另一部分很可能是脱口秀、广告或未被追踪的歌曲。

[![](img/e295561b5594b83f0870c8e36d177a05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X0zq6Dv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AxAIRqQ96Leq1zSD--o4akA.png)T3】

<center>*Percentage of music per day*</center>

为了更好地理解这些百分比，我们应该看看我们所分析的广播电台的正常一天是怎样的。

### 对于我们的广播电台来说，典型的星期一是什么样的？

让我们看看周一所有广播电台的平均歌曲数量

[![](img/e1f374ca2b03392a060b43d5a458e567.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mZorZyYF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Ae3ev4GNUCtKPcnq9SmgoFA.png)T3】

<center>*Average number of songs for Mondays*</center>

在每个广播电台的早间和晚间节目中，我们可以区分出两个间隙。*令人惊叹。*更严重的是，这里没有发现，众所周知的事实是，大多数收音机都有早间和晚间节目，在这些节目中音乐较少，谈话较多。

#### 广告时间

如果我们重新计算中午所有广播电台都没有节目时音乐的平均百分比，我们可以按小时估算广播广告的百分比。我们估计电台主持人在整个一小时中讲 5 分钟。我们必须注意到，当广播拥有更多听众时，它可能会在黄金时段做更多广告。

在 60 分钟的时间里，我们从 7 分钟的广告时间，到 15 分钟的广告时间，为天空摇滚，为维珍。详细来说，我们有这张表:

[![](img/df2470aae35aa60f54474d95e3da081c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FlWx-UCd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AVcuOm-luXT_e_q6d-5q9Zg.png)T3】

<center>*Average minute of music and advertising for every Monday at noon*</center>

### 对讲机洗脑？

我们有时对收音机有一种讨厌的感觉，那就是我们一遍又一遍地听着同样的歌曲。因为我们是相信科学而不是直觉的人，所以我们将使用基本的统计数据来验证这种奇怪的感觉。

#### 同一首歌在同一天播出多少次？

[![](img/3353ca82dc9bd9071600e511b5ddfcd7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UUszzGUw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AAhz21it10spW3txOSnPJew.png)T3】

<center>*Average number of times the same song isbroadcasted by day*</center>

下面的这些饼状图告诉我们很多关于电台的习惯，更多的主流电台，如维珍，NRJ 或 Skyrock，更倾向于多次播放同一首歌。

[![](img/e830ffe77a488af66b16bbe785224484.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bb-FzNzg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2At-xKlfHx8j5fWBSG5epeaw.png)T3】

<center>*How many times the same song is broadcasted?*</center>

#### 下一次我们在同一天听同一首歌是什么时候？

[![](img/73e6641eca49e87841e32183ab11be50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pUoRnNgR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AS-baYaxkztVV7znKPKYRhQ.png)T3】

<center>*Minimum difference in hours between the same songs broadcasted on same day*</center>

同样，最主流的电台，NRJ，Skyrock 和 Virgin 倾向于在同一首歌首次播出后的 2/3 小时内播放。Nova 的值多在 7/8 小时左右。

虽然我们有不同的分发方式，但我们 4 台收音机的平均时间在 7 到 8 小时之间。

[![](img/81d5bca20ceba87695f9366096383ac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9Z16F2ES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AVe3UMz8tRiDVmVnca4vVUw.png)

#### 增加了多少首新歌*什么时候？

*“新歌”是指 2016 年尚未播出的歌曲。

[![](img/142e3b217563b78b65c6e77e9b00cc4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ORTFhNV9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A5PC0QuOvyDTStuoDK7zzXw.png)T3】

<center>*New songs by month distribution*</center>

如果我们看看 2016 年 4 月后的平均值，我们会发现 Nova 领先，但不要忘记 Nova 每个月播放 2500 首不同的歌曲，所以从统计角度来说这是正常的

[![](img/d1c40307c8959454a2d56be7284e30f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0-pVVmOA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ANZfug_TEvZQhUR3zargkmA.png)T3】

<center>*Average new songs by month*</center>

新歌曲在一周内平均分配给所有电台

[![](img/c55d08384a866eef180e9c61e551ef32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B00jOJw3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AhwwdZoj1GRNnWuW7LokE7w.png)T3】

<center>*New songs per weekday*</center>

### 电台间常见的歌曲

在下表中，我们可以看到 NRJ 与 Virgin 的共同歌曲占 25%，与 Skyrock 的共同歌曲占 12%。

维珍在 NRJ 的占有率为 18%,而 Skyrock 在 NRJ 的普通歌曲占有率为 9%。

[![](img/793265435357fd9b05f3f781d76cf86c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nFnlabUy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Aw1sB-Z8zzoP19Lsg2V-U8A.png)T3】

<center>*Number of same songs broadcasted between radios*</center>

Nova 与其他电台有一些类似的歌曲，其中大多数是传奇艺术家，如鲍勃·马利、蠢朋克、芦荟·布拉克、卡文斯基、碧昂斯……如果你对完整列表感兴趣，请在[的“后台”也就是博客文章的 Databricks 笔记本](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/6937750999095841/1807085967979471/6197123402747553/latest.html)中查找“电台之间的类似歌曲”

当然，我们的四个电台是不同的，但是他们之间有共同的歌曲吗？令人惊讶的是，答案是肯定的

*   [王子——亲吻](https://open.spotify.com/track/28S2K2KIvnZ9H6AyhRtenm)
*   [C2C–快乐](https://open.spotify.com/track/255uSDEuvkWp1QyYnm82VJ)
*   [司徒迈——令人生畏](https://open.spotify.com/track/4z70Px77quweOupQRiaG2Q)

我会把这些歌曲归类为每个人都喜欢的歌曲，*你可以在你的派对上播放它们而不用担心被嘘*

如果我们对之前的表格进行可视化，它看起来像这样，蓝色条是相似的歌曲，橙色和绿色条是不同歌曲的总数。

[![](img/d26b9f251dc5617caa66b801fa62dafc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcJioqYA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AXnAgFeqhykf0mhYurSKXJg.png)T3】

<center>*Similar songs*</center>

### 有哪些秘诀可以成为#1？

我们根据艺术家姓名、歌名以及歌曲播放的日期和时间分析了 4 家电台。除了字母和数字，这三个值没有任何意义，如果我们想做更深入的分析，我们必须了解更多关于播放的歌曲:这首歌现在有多流行？这首歌是什么类型的？这位艺术家有多少追随者？

希望通过将每首歌曲连接到 Spotify API，我们将获得大量可以播放的数据:

> [https://api.spotify.com/v1/search?q=ARTISTTITLE&类型=轨迹&极限=1](https://api.spotify.com/v1/search?q=%24encodedQuery&type=track&limit=1)

2016 年，我们从收音机中收集了 8000 首不同的歌曲，因此要从 Spotify API 中获取艺术家、曲目和曲目的音频功能，我们必须:

> 歌曲数量 x ( [艺术家](https://developer.spotify.com/web-api/console/get-artist/) + [音轨](https://developer.spotify.com/web-api/console/get-track/) + [音频特征](https://developer.spotify.com/web-api/console/get-audio-features-track/) ) = 24K 请求

[![](img/434d5cf61ff92921ffb0ca1862a57dba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P-FpTlOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2Aj0EeLA19ElJmN_ae.jpg)T3】

<center>*From [HTTP STATUS DOGS](https://httpstatusdogs.com/)*</center>

那太多了。另外，Spotify 有时间限制，所以我们必须慢慢来，每 2 秒钟 20 个请求，为什么不呢？

但是，在这种缓慢的速度下，我没有计划的一件事是，当我们请求歌曲的艺术家时，我们可以看到关注者的数量发生变化，因为大多数艺术家都有多首歌曲被广播，艺术家信息被请求两次到 10 次。没问题，对吧？不…这会弄乱我们的艺人和曲目数据之间的 SQL 连接，因为艺人信息上的差异[由于`followers.total`是假的](https://www.youtube.com/watch?v=1IDF-8khS3w)

我不得不说这让我疯狂，因为我加入后比以前有了更多的歌曲哈哈

[![](img/c2eb465dcf1a4ab75e578793a073d7c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kSDiE_UW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AUlBp9dA-JJyY3aJ-klrsBg.png)T3】

<center>*Spotify API Stats from
[https://developer.spotify.com/my-applications](https://developer.spotify.com/my-applications)*</center>

### 歌曲受电台欢迎程度

#### 由 Spotify 定义

> 流行度是通过算法计算的，并且在很大程度上基于该曲目的总播放次数以及这些播放的最近时间。

[![](img/a0a789d2f487cda691b69d1cb8652b11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ug1iXi8---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AvVSuuElWOqbngrTlj4SNdw.png)T3】

<center>*Percentage of songs popularity distribution by radio*</center>

这并不奇怪，主流电台 NRJ、Virgin 或 Skyrock 倾向于播放更流行的歌曲，这就是为什么我用“主流、聪明”这个词，对吗？

[![](img/376b02b8fdd7f348ec9ddf09252fb841.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yiYGQvZs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AFwnhWdDxbYIQ_YhIjkLspA.png)T3】

<center>*Popularity average*</center>

但真正的问题是:这首歌在电台播出之前是否很受欢迎？

### 音频特性

Spotify API 提供了从歌曲的声波中提取的[音频特征，通过这些特征，我们可以显示每个收音机的音乐档案:](https://developer.spotify.com/web-api/get-audio-features/#tablepress-215)

[在我看来](https://www.youtube.com/watch?v=pWdd6_ZxX8c)，最有意义的音频特性是:

*   可跳舞性*描述了一首曲目根据音乐元素的组合适合跳舞的程度，包括速度、节奏稳定性、节拍强度和整体规律性。*
*   能量*是从 0.0 到 1.0 的度量，代表强度和活动的感知度量。通常，高能轨道感觉起来很快，很响，很嘈杂。*
*   效价*描述了一首曲目所传达的音乐积极性。高价曲目听起来更积极(例如，快乐、愉快、欣快)，而低价曲目听起来更消极(例如，悲伤、沮丧、愤怒)。*

让我们看看它们的平均值和分布，在收音机的音轨中，*作为平均值有时会误导*。由于 Nova 比其他公司有更多不同的歌曲，我们将使用百分比来比较我们的电台，以增加更多的统计信息。

[如果你读过我的脸书面试之旅，](https://medium.com/@polomarcus/my-facebook-interview-journey-5205e111155f#.r3w705wo3)你会知道这是我在 SQL 面试中失败的地方，这段代码是专门为你准备的，亲爱的面试官先生，请不要难过:p

```
SELECT ROUND( (COUNT(t.*) / subTotal.total_radio * 100),2) AS percentage_of_songs,
      subTotal.total_radio,
      ROUND(popularity / 10) * 10 AS popularity,
      t.radio
FROM AudioFeatureArtistTrackRadios t
JOIN (
    SELECT COUNT(*) AS total_radio, radio
    FROM AudioFeatureArtistTrackRadios
    GROUP BY radio
) AS subTotal
ON subTotal.radio = t.radio
GROUP BY subTotal.total_radio, ROUND(popularity / 10) * 10, t.radio
ORDER BY popularity 
```

Enter fullscreen mode Exit fullscreen mode

<center>*Brilliant SQL code*</center>

#### 能量

[![](img/9c267893008cceebca8b12dd09747115.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MUneSZ7F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AY20xDfW6m6x4PQ-gdtMouA.png)T3】

<center>*Energy by radio distribution*</center>

主流收音机倾向于播放更有活力的歌曲，*我猜有更容易听的？*充满活力的歌曲有:[《我们是你的朋友》](https://open.spotify.com/track/6Unf6X6PC7KZtrRXH3dFHg)，[《踏脚石》](https://open.spotify.com/track/7yI4qKGEFHNId1B893XopS)，当然，经典中的经典[挺起胸膛——凯撒大帝](https://open.spotify.com/track/1bx7OUl2UmAnA5oZkm9If7)，我第一次听到它是在 GameCube 8 上玩 [SSX3 的时候](https://www.youtube.com/watch?v=-0x3aZEPcMA)

[![](img/89a969f90a9478ea157d1b717582edef.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RhVZzTgy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AIySsIzTaReMzd1OZ9PxXWw.png)

#### 可舞性

[![](img/45621ee351ca2368c126349ed1fbb392.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B04zli6L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AP4erZSV3quz3__oR2c24bA.png)

这张图表告诉我们两个电台播放的是同一种适合跳舞的歌曲。一些适合跳舞的歌曲有[欺骗我——凯丽思](https://open.spotify.com/track/3aImJnJlAgcE7bJ1NxthGt)、[环游世界——蠢朋克](https://open.spotify.com/track/1pKYYY0dkg23sQQXi0Q5zN)或[蟒蛇——尼基·米娜](https://www.youtube.com/watch?v=LDZX4ooRsWs)

[![](img/ea971c06627540f1feeaefdf2b61f521.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZGH-0e5y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJpPrbAzb0U13iMZK7PihXQ.png)T3】

<center>*Danceability average*</center>

#### 化合价/正性

[![](img/8cff838960c4080d4126edbb6151d8f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EtLn5MfV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AujgMyDVxmhj2YlgfIM11HA.png)T3】

<center>*Valence / Positiveness By Radio distribution*</center>

和可跳舞性一样，两个电台都播放同一种积极的曲目。一些例子:[九月——地风&火](https://open.spotify.com/track/5nNmj1cLH3r4aA4XDJ2bgY)，[斯卡曲风——波波——达巴——斯卡利特人](https://open.spotify.com/track/4BLu47sbjr3aJZwxZujgXT)或[嘿呀！–out kast](https://open.spotify.com/track/5WQ1hIc5d2EVbRQ8qsj8Uh)

[![](img/92829c9435eb00c487e8a9c25d8d1507.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GrgROZqb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AJaa8mmCC84LsxskMJjbuNg.png)T3】

<center>*Valence / Positiveness average by radio*</center>

其他有趣的数据，不是 Spotify ( [Echo Nest](http://the.echonest.com/) )特有的，是 BPM(每分钟节拍数)和歌曲时长

#### 速度/每分钟节拍数

[![](img/29077687b6cb4f7ac197fb6729ebdf47.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--oNCphCQ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AXkq3Q3yjJaNowJkhzXP1FA.png)

#### 持续时间

[![](img/24f61c2161ad787fe0a2a312fc046f45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NYZ7jVEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A67Kr4JwQOCrc2u6Obz3nMg.png)T3】

<center>*Duration distribution*</center>

Nova 播放的曲目或短或长，似乎与其他电台有所不同。维珍，NRJ 和 Skyrock 真的很喜欢 3 分钟的曲目。

当我第一次看到这个图表时，我不禁想起了这首名为“Voyage immobile ”(静止的旅程)的歌曲，以及这句关于我们单一音乐环境的话:

> "我只看到岩石里有三分钟长的碎片" "而且是为了我自己的目的" " I only saw three minutes long blocks haircut in the rock and for my own purpose . "
> 
> “我只能从同一个基地看到同样目标的 3 分钟街区”

[![](img/d74a46459e9cbf17aee5074be40076bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1ob0Jy85--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Afpld7Zi9lOJDPKWDdrcFpw.png)T3】

<center>*The duration average in minute by radio*</center>

### 音乐流派

[![](img/da167788f9f470a2961a08c4b2bcdd1b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A5Ej6Dby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AsgT6naW21Wu0M7sYaL2qMQ.png)

Spotify 有一些非常奇怪的音乐流派，你有没有注意到“后青少年流行音乐”，“流行圣诞节”，“我猜你在圣诞节听的流行歌曲”？哈哈

我们可以清楚地看到，NRJ 和 Virgin，*非常相似*，更多的是关于流行/舞蹈/电子音乐，他们的前三大流派是:流行、舞蹈流行和热带屋。Nova 是关于灵魂，放克和独立音乐，而 Skyrock 更多的是关于说唱，舞蹈和流行音乐

[![](img/cb8327a611c6c915765a5c91b3e9a1cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y-TmFSLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A_5E7highTXSqJ8rS7oFI0Q.png)T3】

<center>*Number of different music genres by radio*</center>

#### 嘻哈流派

[![](img/ecc61673cb387367b4c596b87471718e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aP_BBGA2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2A58-RGcDqjhx8XLD3.jpg)T3】

<center>*First on rap*</center>

Skyrock 以其座右铭“说唱第一”而闻名，让我们比较一下 Hip hop/Rap 流派(名称中带有“说唱”、“Hip”或“hop”的流派)与其他电台。

[![](img/2354ed16273cd95bb2117e37f9ef5de8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tJ-FAFlV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AOGs_m824iH2tMza0ziY8JQ.png)T3】

<center>*Number of hip hop, rap songs by radio*</center>

好了，这是 Skyrock 和 Nova 之间的一场势均力敌的比赛，现在让我们来比较一下内部的嘻哈流派。

我真的不关心流派，但嘻哈和说唱之间有很多混淆，嘻哈是一种文化，说唱是说唱的事实，如果你想了解更多，请查看这个[维基百科章节](https://en.wikipedia.org/wiki/Hip_hop#Culture)，我还推荐优秀的[网飞的纪录片《嘻哈的演变》](https://www.netflix.com/title/80141782)

[![](img/a881d15bf91010164d58c4be28c264d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mpOP0nLc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AAsTuELi8z5QUmnG25FfUmA.png)T3】

<center>*Genres with Hip or Hop or Rap by Radios*</center>

橙色的 Nova 更倾向于独立/另类/地下嘻哈音乐，蓝色的 Skyrock 更倾向于法国说唱/陷阱/嘻哈和流行说唱。所以让我们用“法国说唱第一”来修正 Skyrock 的座右铭吧哈哈

### 音乐分类器对收音机的选择思路

在我的上一篇文章中，我解释了如何借助这些音频功能创建自己的音乐推荐系统。

一个[有趣的项目](https://en.coursera.org/learn/progfun1) ( *)这个链接是向 Scala 大师[马丁·奥德斯基](https://medium.com/u/9a80872a98bb)致敬，他倾向于说太多遍他的 Scala 练习是有趣的，而他们是大脑融化哈哈*)将创建一个算法，帮助音乐选择器找到 radios 风格的歌曲。

#### Spotify 推荐系统

Spotify 的系统不仅仅是基于我们之前看到的音频功能。它还分析其他类似用户听的内容。这张幻灯片包含一个很好的图表，解释了他们的整个系统。

### 接下来是什么？

由于这个项目，我已经建立了坚实的基础，可以在 Scala 中查询 Spotify API，通过 Spark SQL 处理它，并通过 Databricks 可视化它。我觉得更多的项目即将到来，再加上 Spotify 刚刚发布，2017 年 3 月，这个新的端点[“最近播放的曲目”](https://developer.spotify.com/news-stories/2017/03/01/new-endpoint-recently-played-tracks/)和创意来了。

### 数据块利弊

#### 优点

*   带 6Go RAM 服务器的免费社区版
*   令人惊叹且易于使用的[数据，即](https://docs.databricks.com/user-guide/visualizations/index.html)

#### 弊(或者更多，什么可以更好)

*   使用 GROUP BY 时，最多只能可视化 10 个元素，其他元素将被归入一个名为“其他”的类别
*   不可能选择实体的颜色，所以一个单选按钮在一个图上可能是蓝色的，而在另一个图上可能是红色的，这有时会令人困惑
*   无法将图形导出为 iframe，因此我们必须从交互式图形中导出图片
*   无法在数据 Viz 接口上修改 SQL

### 感谢

*   数据块，因为他们令人敬畏的平台
*   Spotify 易于使用的 API 和人类可读的文档(T2)
*   作为顶级音乐人 [selecta](http://www.urbandictionary.com/define.php?term=selecta) ，如果没有你，我不会听到今天我听到的音乐
*   马克·赫利米(Marc H ' limi)，新星电台的顾问，为我们带来交流
*   Pierre Trussart，工程师兼 DJ， [Benjamin Thuillier](https://twitter.com/b_thuillier) ，scala rockstar，Nicolas Duforet，数据科学大师，Justine Mouron，工程师
*   我的朋友经常听我谈论这个项目

### 奖励–Spotify 播放列表

为了感谢您的阅读，我创建了 4 个播放列表，播放了大约 200 首歌曲，按播放次数排序如下:

*   [新星与卡利普索·罗斯、布里莎·罗尚、凯特拉纳达、鲁特、米·雅、处女……](https://open.spotify.com/user/cpolos/playlist/64DWZ46dFb50FaWs9eALIu)
*   与德雷克、阿隆佐、雷泽少校、汀布莱克、索波诺、PNL 合作的天空摇滚乐队，7 月 …
*   维珍与伊玛尼、21 名飞行员、新航、昆士、朱利安·佩雷塔 …
*   NRJ 与安立奎·伊格莱希亚斯，女高音，酷玩，孔，阿米尔，MHD，塔尔 …