# ES6 的新增功能—第二部分

> 原文：<https://dev.to/taiwo_xyz/whats-new-in-es6part2-420>

我们从 ES6 新特性的第 1 部分开始。
字符串和数字方法
ES6 中有不少字符串和字符串方法的新特性。它们用于定位字符串内部的表达式。我们将处理以下问题:

开始于()

endsWith()

包括()

让我们举个例子来说明它是如何工作的。

[https://cdn-images-1 . medium . com/max/800/1 * ks 7 GD _ 2-gsmpzohzehpxrq . png](https://cdn-images-1.medium.com/max/800/1*Ks7GD_2-GsMPzohZEhpxRQ.png)

在上面的代码片段中，我们使用了 ES6 提供的三种新方法之一 startsWith()。这个方法的作用是检查提供的字符串值是否以一个匹配的字符串开始，这个匹配的字符串通常是括号内的字符串。因此，我们正在检查 fullString 变量是否以 Hello 开头。该方法返回一个布尔值，如果相同，则返回 true，否则返回 false。

注意:它区分大小写。这意味着“你好”不同于“你好”。

[https://cdn-images-1 . medium . com/max/800/1 * jgqlsk 6 lvzqxbki 1 F3 xeq . png](https://cdn-images-1.medium.com/max/800/1*jGQlSk6LLVZQxbki1f3xEQ.png)

在上面的第二个例子中，我们可以看到，在使用第二个方法 endsWith()时，我们正在检查字符串的值是否以粒子文本或值结尾。你应该已经知道，即使我们的论点是游戏，它返回假。这是因为就像我前面说的，它们是区分大小写的，所以我们希望我们的结果是假的。

[https://cdn-images-1 . medium . com/max/800/1 *-kk9g 6 f 6 _ tut 8 xyd 6 w-CMW . png](https://cdn-images-1.medium.com/max/800/1*-kk9g6F6_TUT8xYD6W-Cmw.png)

在上面的第三个例子中，我们可以看到在使用第三个方法------------------------------------------------------------------- includes()时，我们正在检查传递的字符串值是否在主字符串的任何位置。因为我们的字符串 William 可以在 fullString 中找到，所以我们得到了一个 true 值。我们还可以在 ES6 中进行一些数字操作，如二进制、八进制等。

[https://cdn-images-1 . medium . com/max/800/1 * 5y yiyyah 98 pwwn-pgssygwg . png](https://cdn-images-1.medium.com/max/800/1*5YyiSyH98PwWN-pGSSyGWg.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * vcdzfxulcs S3 iwu 5 hxscvw . png](https://cdn-images-1.medium.com/max/800/1*VcdZfXuLcSS3iWu5hXScVw.png)

让我们来看看数字函数。

[https://cdn-images-1 . medium . com/max/800/1 * PS 6 ukbhj 7 aaorvz 9 uku 29 q . png](https://cdn-images-1.medium.com/max/800/1*pS6UKbhJ7AaORVz9UKu29Q.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * 7 azhyk 5 o 0 pfqboqdfjw-rg . png](https://cdn-images-1.medium.com/max/800/1*7azhYk5O0PfQBOqdFJw-rg.png)

还有很多数字方法，你可以自己检查并多加练习。

默认参数和扩展运算符。

ES6 中的默认参数的工作方式与 PHP 和其他软件非常相似。当我们想使用一个缺省值的时候，比如说当我们运行一个函数或者其他什么的时候，我们所要做的就是在我们传递它的参数中赋值。下面是一个例子。

[https://cdn-images-1 . medium . com/max/800/1 * yzh 6 F3 uztasuvi 1 q 41 rhfw . png](https://cdn-images-1.medium.com/max/800/1*YZH6F3uzTasuvI1Q41RHFw.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * aso 9 lw wild xvw 1 couca . png](https://cdn-images-1.medium.com/max/800/1*ASo9LwwiLDIoXvW1CoUccA.png)

我们可以将一个 undefined 设置为 hold，或者设置一个缺省参数，使它看起来比我们得到的缺省 undefined 更好，而不是输出一个 undefined。这是一个例子。

[https://cdn-images-1 . medium . com/max/800/1 * fk 3 dprjcnr 4 erd-1 hcfxew . png](https://cdn-images-1.medium.com/max/800/1*fk3dPRjCnR4ErD-1HCfXew.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * rrq 7 thhayvynnqr 4 xhvvow . png](https://cdn-images-1.medium.com/max/800/1*rrq7tHhaYvyNNQR4xHVvow.png)

我们所做的就是在函数中设置一个默认参数来获得输出。如果我们从 ES5 开始，我们可能会有这样的东西。

[https://cdn-images-1 . medium . com/max/800/1 * uhmhqdatzwrsxyhbun 2 TEG . png](https://cdn-images-1.medium.com/max/800/1*uhMhQDaTzwRSXyhbuN2Teg.png)

您可以确认，使用 ES6，要编写的代码要少得多。

让我们来看看价差操作符。

spread 运算符用三个点(…)表示，用于允许表达式在需要多个参数的地方进行扩展。让我们快速浏览一下。在 ES5 中，如果我们想要将一个数组作为参数传递给函数，我们将使用 apply 方法，如下所示。

[https://cdn-images-1 . medium . com/max/800/1 * cqdh 6 l _ lo 8 hphk 0 awplw . png](https://cdn-images-1.medium.com/max/800/1*cqecDH6L_lo8hPHk0aWPLw.png)

然而，在 ES6 中，我们不需要使用 apply 方法。我们所需要的是利用如下所示的三个点。

[https://cdn-images-1 . medium . com/max/800/1 * wct ns 0 f 9 h8 amoezcmf 9t 8 w . png](https://cdn-images-1.medium.com/max/800/1*WctNS0f9H8AMOeZcmF9T8w.png)

这与前面的输出相同，但是要编写的代码要少得多。不错是吗？

让我们看看有两个变量，并使用传入的参数。

[https://cdn-images-1 . medium . com/max/800/1 * Gk-lzqcrb 1 ugp 0 ARB 0180 w . png](https://cdn-images-1.medium.com/max/800/1*Gk-lZQCRb1ugP0ArB0180w.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * tfx kb-Hdk8m _ hy ilr 3 gvmq . png](https://cdn-images-1.medium.com/max/800/1*Tfxkb-Hdk8m_HyILR3GvmQ.png)

我猜是喜欢新功能吧。

映射和设置数据结构。

set 对象允许我们存储任何类型的唯一值。可以是原始值或对象引用。

让我们来看看这是如何工作的。

[https://cdn-images-1 . medium . com/max/800/1 * Ynb _ uz1 bqgcl 119 jkibqiw . png](https://cdn-images-1.medium.com/max/800/1*Ynb_uz1bQGCl119jKIbqIw.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * g 07 ihqdo 21 ryiwcka 2677g . png](https://cdn-images-1.medium.com/max/800/1*G07ihQdO21RyiWcKA2677g.png)

考虑上面的片段。我们创建了一个名为 myArr 的数组。然后，我们创建了 Set 方法的一个对象，并传入了我们创建的数组，最后将该对象记录到控制台。我们可以看到，在输出部分，set 甚至告诉我们，在我们传入的数组中包含了哪种元素。让我们尝试对创建的 Set 对象进行一些操作。

[https://cdn-images-1 . medium . com/max/800/1 * sV8x _ cmmtgotzivpccnrla . png](https://cdn-images-1.medium.com/max/800/1*sV8x_cmMTGOtZIvPCcNRLA.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * MGS 4 xan 5 sfbn 5 jeucvqeoa . png](https://cdn-images-1.medium.com/max/800/1*Mgs4xAn5SfBn5jeUcvqeoA.png)

[https://cdn-images-1 . medium . com/max/800/1 * fy 7m 55 oud _ okgpuzyfudia . png](https://cdn-images-1.medium.com/max/800/1*fy7m55ouD_OKgPuZyfuDIA.png)

如果我们仔细看看我们试图实现的目标，我们向集合中添加了一个字符串值，还向集合中添加了一个对象。我们还从集合中删除了一个元素。您可以从上面生成的两个输出中看到，集合“数组、字符串和对象”中存储了什么类型的值。

你也可以自己尝试这样的方法:

myset . clear()；//这将清除整个集合
my set . size；//这将返回设置对象的大小。

还有其他方法可以让你熟悉 set。让我们试着用设置的对象执行循环。

[https://cdn-images-1 . medium . com/max/800/1 * ihqm 3 JV 2 zktkem 8 urzwqq . png](https://cdn-images-1.medium.com/max/800/1*ihqm3Jv2VZkTkeM8URzWqQ.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * dona 57 yunbbl 6 zm6 ovduitq . png](https://cdn-images-1.medium.com/max/800/1*DOA57YuNbl6zm6OvDUOItQ.png)

我们可以在输出中看到，我们的 set 对象遍历了 set 列表中的每个元素，并将结果记录到控制台中。

让我们深入地图。

映射是键值对，而集合是单个的值。让我们看一个例子。

[https://cdn-images-1 . medium . com/max/800/1 * H7 ewugxh 2 w1 tjr H9 ku 48 na . png](https://cdn-images-1.medium.com/max/800/1*h7EWugxH2W1TJrh9kU48nA.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * xpzbktcxbe 3 wffabaulpcw . png](https://cdn-images-1.medium.com/max/800/1*xpzbKtCXBE3WffABaUlPCw.png)

在上面的例子中，我们创建了一个简单的 map 对象，它有一个数组，每个键(a1，b1)分别有自己的值(Hello，How are you)。我们还可以向对象添加元素或其他键值对。让我们来看看。

[https://cdn-images-1 . medium . com/max/800/1 * 1 mdoeyydaabjs 0 nrfs 3 rfw . png](https://cdn-images-1.medium.com/max/800/1*1mdOEyyDaabjs0nrfs3RFw.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * 4vq9ct 0 ofcob 0 l5 pdhsn 8g . png](https://cdn-images-1.medium.com/max/800/1*4Vq9cT0OFcob0l5PdHSN8g.png)

还有其他方法可以用于 map 对象。

一些包括:清除，删除，forEach，散列，大小等。

为了删除，我们使用键。让我们来看看。

[https://cdn-images-1 . medium . com/max/800/1 * J3 oorvansntfkdozq _ SG . png](https://cdn-images-1.medium.com/max/800/1*J3OORVaGnsntQfkdOzQ_sg.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * xpzbktcxbe 3 wffabaulpcw . png](https://cdn-images-1.medium.com/max/800/1*xpzbKtCXBE3WffABaUlPCw.png)

除了集合和地图，我们还有 WeakSet 和 WeakMap。这些都与物体有关。让我们来看看它是如何工作的。

[https://cdn-images-1 . medium . com/max/800/1 * mt 0 xlk 6 cwwhhtb9 uzfp _ w . png](https://cdn-images-1.medium.com/max/800/1*mt0Xlk6CWwHHtba9uzFP_w.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * qyr 1 fxxt 8 dq 79 addu 1 w29 a . png](https://cdn-images-1.medium.com/max/800/1*Qyr1fxxT8dQ79AdDu1w29A.png)

我们创建了一个 Weakset 对象，并将其命名为 carWeakSet。然后，我们创建了一个名为 firstCar 的对象，其属性为 make 和 model。然后，我们使用 add 方法将 firstCar 对象添加到 carWeakSet 对象中，如上所示。

让我们用 WeakMap 试一个例子。

[https://cdn-images-1 . medium . com/max/800/1 * 0 和 0x XB 1 g29 y qqmodby 0 CQ . png](https://cdn-images-1.medium.com/max/800/1*0y0zXb1G29YQQmodObY0CQ.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * 9 ootaxuf 17 e 8 azjqzptfba . png](https://cdn-images-1.medium.com/max/800/1*9oOTaXuf17E8aZjqzPtFBA.png)

我们可以看到，创建的键也有一个赋值。集合和映射的方法可以分别应用于弱集合和弱映射。

请继续关注 ES6 新特性的最后部分。

感谢阅读。