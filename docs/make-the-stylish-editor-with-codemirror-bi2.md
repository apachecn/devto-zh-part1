# 用 CodeMirror 制作时尚的编辑器。

> 原文：<https://dev.to/daniel123/make-the-stylish-editor-with-codemirror-bi2>

我一直在使用 Codemirror 制作一个名为 [Boostnote](https://github.com/BoostIO/Boostnote) 的 markdown 编辑器。
使你能够在编辑器中切换主题，在 Markdown 预览中切换代码块，我在这篇文章中写了如何实现这个功能。

## 特征

*   在 Boostnote 的 markdow 预览中的编辑器和代码块中切换主题
*   用户可以选择自己喜欢的主题。

[![image-gif](img/4c3aa467f0797afd2d5211f9a9e6aef8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nj-snNQp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cshkx1chcbm25rskcghe.gif)

## 如何制作

**①安装代码镜。**

由于我们在制作 Boostnote 时主要使用 React，所以使用 npm 安装包。
`npm install codemirror --save`

**②检查已安装的 codemirror 包的内容。**T3`cd node_modules/codemirror/theme`

许多种类的主题是自动安装的。

它们和这里的一样。[https://github.com/codemirror/CodeMirror/tree/master/theme](https://github.com/codemirror/CodeMirror/tree/master/theme)

**③介绍已安装的主题！**
1 .默认
[![1](img/3475f83800c9e8fc77fef4f9bb96f568.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GXYNv69n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AVJ3TYrUGqs58R-OqgcTg8g.png)

2.3024 日
[![2](img/0b1cd1c1dabf17661d3d2fe51c9ae203.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FC7qqg1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5sqholNW_D7arEQpE57Nbw.png)

3.3024 夜
[![3](img/a19b97fe65badc0006c0a30a5fb520f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ENFBTrwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ANtbqsLnywkoHusq4g6z02A.png)

4 . abcdef
[![4](img/6c5b74af997f5ec6d1e3eca82dd62c77.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--YOdVcwUo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AAcycwSf3UGbNhSqDC2hLAQ.png)

5 .氛围-移动
[![5](img/52257d33fac99984c7c7b7b973a21b8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mndIdhW3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJ-cduD9dbkAKseP7fp4wXw.png)

6 .环境 [![6](img/46f2478c304e3e9dbabc55cd1c0fae96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4EDaHQI4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AKq70sJHfGJURPHVhEYKK5w.png)

7 . base 16-黑暗
[![7](img/c1a51a20e10c5902e7ed019501e31577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BnOMRF4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AvctjGY8uaJ3-akHV9VLHAg.png)

8.base16-light
[![8](img/2c155ec44ccf162f441f615882f834d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nsoa3fjQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ADPIcCB4-GGYrktRuamJBzg.png)

9 .贝斯平
[![9](img/26ab49311ba537f47ad0e683b521723d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7cvaFW-l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJbn7ZhzWH8rBfm-EE6a7Fw.png)

10 .黑板
[![10](img/3036b19cc9fe0ab0457f3b67fe16b2ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8AQGKip6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AtRFpOcfZoyfARE2cbcwuPQ.png)

11 .钴
[![11](img/57579d74d7f1ab483a9cb337675eb277.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R4V2f4rb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A8sf9R7Vx690yBegSiALpHg.png)

12.colorforth
[![12](img/1eb10b390b6283d50ffe7ecd74964a75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fw0x3ZYd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AA6xpEtcYjk5I5ZcRJlI1hQ.png)

13 .德拉库拉
[![13](img/323d9a20bf58033e06d8671ff65e1722.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--97s9jraI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYu5Nh61_iztVLrNcqv_f6A.png)

14 .双色调-暗
[![14](img/6fc1a50bc324a25c18a2aaec66591273.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--px7wCnkS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AaOIPXJ5D3KOJR2wjc_ooYw.png)

15 .双色调光
[![15](img/ee2b8bb654b6d86762a231b1c8f8f193.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EQyW92VR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ASv3mj1Ln3rUvhd5uiR6cSw.png)

16 .日食
[![16](img/477f8508566f43a11f4448c9436523ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jppAqsfb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AMHHnqXrWDEwKgFUKEdkgiw.png)

17 .优雅的
[![17](img/4a383ae79129d4cf1b6c9093521bf39e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7z0cANHH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwOZKwk5mrdnCsfo7smRckQ.png)

18 .二郎神
[![18](img/0cf361261dde9f18f5d7daaed30370f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WQPRwe63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A3JduF6Xd9_gfcjuGOcFPlA.png)

19 .跳房子
[![19](img/dafa611dacadeb298e34ed514fe23709.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--6d39jcGa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AwbQ0XZ-7qFadn8fjkruUqg.png)

20 .编码器
[![20](img/1fe0178a97214a64197e57063173860b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvG9SKt8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AtfSE85EdvZa1qTzDQ0ONAg.png)

21 .同位素
[![21](img/2e52547d278823146774bf69addda68b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--WV9LRpmG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A7qRCWrMmJF_oincElQtkTQ.png)

22 .稍暗
[![22](img/d007a23d449d7848c391b28410067b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TzaphrDB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9kA9AazYa0DBfCXoGFAEuA.png)

23 . liqui byte
[![23](img/9a20123c724f31b0725d177db041ecaa.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zwYo30M8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2An4OkjRWjdWYmpIt8v-zjeg.png)

24 .材料
[![24](img/cfda11214a4596763f588e4e5500f574.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0NSCg2zS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AWcz57YBkbNv1yVvDYH_BKg.png)

25.mbo
！25]([https://cdn-images-1.medium.com/max/1600/1*O-UBKKw-7t _ ehrmxg 8 p-g . png](https://cdn-images-1.medium.com/max/1600/1*O-UBKKw--7t_EhRMXG8p-g.png))

26 . MDN-like
[![26](img/df72cf9fa106ce43c60bce856c06413a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zdR2tC-I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXQN9HS0y5fmFJAWpqqdULQ.png)

27 .午夜
[![27](img/20dbc9888c6ba46ed803fdc98251f8a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZU9O5Nas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJaP_zk1Dc4UTX8BMu1EukA.png)

28 .单凯 [![28](img/345c3cd892d8e5abca3233c88c4fb484.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HXLHewp6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AatjLqJJQVfW0Gi_gLku8SA.png)

29 .整齐的
[![29](img/60d0f05119dbd0a5ff4402e11ea5191c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g3Alz8go--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AYwTD32wXxqtXS2iQUZBGuQ.png)

30 . neo
[![30](img/35531201d7d7b7bd460992f4cd31e45b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--U7hgKfkD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AfBdx9j5Mr89ju7-J3vQoNQ.png)

31 .夜晚
[![31](img/8474305e488dede2094bd3be9ce3201d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pb7pU2WJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AV5bQYJCnllNhdMVwWVq1nA.png)

32 . panda-语法
[![32](img/c4d466c3fb8c69f235b811368da910fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OJL8NicO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Adnv7bHpXSL1LzF-i-XekvQ.png)

33 .黑暗
[![33](img/f8d978941d2bf838426c5e74b4a889ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZbSRYqUt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArhUgARD5H0yXzpbCibDpng.png)

34 .等光
[![34](img/01799c7c2bd1eac4f7a8fc6fbdf358c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LjB-AKzI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ac_QZVOLz-jddT4ggvtieRA.png)

35 .深色粉彩
[![35](img/5e984ec3dab0f04a3811bf0cab20e48e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--APx4kHRh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AxoHSaV5osj252Rr2a1NRZA.png)

36 .铁路运输
[![36](img/91197792588934507c4a43ad42265f44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X6_SSA1s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ApQr2EssZEBdZHtUsZdUw8A.png)

37 .卢布(t0)[![37](img/06a6cc10bda499ad52ca45154b644904.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UsXh49oE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AlMJVtElvHVroqJylKd82Fw.png)

38.seti
[![38](img/b468feb121d35c4fb820ad9b2693e093.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j3nefKHh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AOpywh8SxoCOyB9mwop8BFQ.png)

39 .日晒雨淋的黑暗
[![39](img/47372a777f4127f74842fec803db481b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SW-teLd5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AZbe9zQADaGSPsGpOgQ8uYg.png)

1.  曝晒灯![40](img/2c9620420310bb36bd1ed8fd925f635e.png)

41 .矩阵
[![41](img/f98b2a372d14912c8247d1f79b311b22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LIbbj7j1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Ap-N8GC_pg6W1kO5TKwz6jA.png)

42 .明日之夜
[![42](img/72ab4724dd46fdab30280223fe63e13a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GPV6PvxI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AnQZhCel6WnTqzU3MemdAAw.png)

43 .明晚八十年代
[![43](img/14d755ccc643b012bd134f7378c5e58f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dYZMafcm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ADRsqtqP8svVhgkBrHIRgsg.png)

44 . ttcn
[![44](img/a2a163b4b7e96989c29c8550164ffa44.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iavblUe---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AzDZLWkjRkLyJ3mEQdeyyMw.png)

45 .暮色
[![45](img/db754d9bcc2958f50b689cf72c9250da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pc25P_yH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2ArIZkuakixo6XhKFPJNF86g.png)

46 .活力墨
[![46](img/1a19b6b01c0d8b7cfa67d4781d6e9d4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oSWN61Ki--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXKESiYLaBLiJZZH3Fcq32Q.png)

47 . xq-黑暗
[![47](img/017aac6546783fc6777f0e5eda2fc1ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v9EjcH4X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AJ7PD7iT84crWffjEB1NoPw.png)

48.xq-light
[![48](img/ae2de6c2c841301aee8b296e0161146e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iwbRYJvU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AMsLu4hcp6UZGN2-Z8OT6ng.png)

49.yeti
[![49](img/ba2a35ecbb6478d8fa46505ad0ec71f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hvOdqOEB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AzCiIfRyHcr8gSYxGEdHeHg.png)

50.zenburn
[![50](img/6ef462c7a571809622fb3cd365258ead.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dvx-QHFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A9fxsr9mXgUnm02iIfdmf5w.png)

仅此而已；)

CodeMirror docs 在这里是。

享受 CodeMirror 和 [Boostnote](https://boostnote.io/) ！

* * *