# 先走后跑

> 原文：<https://dev.to/dwd/on-walking-before-running>

飞行平安无事，直到最后几分钟。当皮托管——一种测量空速的仪器——冻结时，它开始出问题，导致自动驾驶仪关闭。回想起来，很明显，当时正在飞行的初级飞行员博宁发出了他的经历的信号，当时他喊道:"我正在起飞复飞，对吗？"在大西洋上空 37500 英尺的地方。当机长返回飞行甲板并告诉机组人员拉起时，其他机组人员才意识到，飞行员回应道:“但我一直在拉起！”。过了一会儿，小笠原说，“但是发生了什么事？”这是他在 AF-447 坠入大海，杀死机上所有人之前说的最后一句话。

我有点担心我们的行业，这并不是因为我们有没有经验的开发人员编写代码。毕竟，就目前的情况来看，博宁并非完全没有经验——他当时在该机型上工作了 807 小时，作为飞行员工作了近 3000 小时。

不，我担心的是，博宁没有在高空手动驾驶飞机的经验，在他的职业生涯中几乎没有手动驾驶过轻型飞机，所以主要是起飞和着陆，或者通过自动驾驶仪操作飞机。换句话说，他没有基本面的经验。这意味着他对飞机每分钟下降 10，000 英尺的反应是越拉越远——飞机指向 41.5 度。

我认识很多令人印象深刻的开发人员，他们不知道任何汇编程序，或者不了解虚拟内存是如何工作的。我对那些不理解非阻塞调用和阻塞调用之间的区别，或者不会编写自己的事件循环的开发人员感到非常惊讶。并不是说任何人都需要写一个事件循环(连我都不需要，我是一个系统程序员)。我至少有几十年没碰 ASM 了，现在感觉好多了。

但是当我开始编码的时候——是的，我是一个脾气暴躁的老人，请容忍我——人们必须了解这些事情，因为我们真的没有选择。出于需要，我的第一个“真正的”编程不得不包含一点机器码——那是直接输入十六进制代码，甚至连汇编程序都看不到！当我开始尝试事件驱动编程时，我不得不自己编写一个事件循环，因为没有库为我做这件事。这不是自夸；这只是我四十多岁的样子。如果我再大一点，那么早些年根本不会有更高级别的语言。此外，这是上山的方式。

很多时候，这种浪费在栈底的青春，对我来说毫无用处。但是当事情出错时，对底层技术的一点理解和经验给了我一个真正有用的背景。为了举例说明，也因为我对空难的古怪执着，我想讲述一下 1983 年加拿大航空公司 143 航班的故事。

由于一系列有点奇怪的事件，143 号航班陷入困境，导致燃油表不起作用，燃油不足，但机组人员确信飞机燃油充足，可以飞行。

143 号的机长和副驾驶都有丰富的经验——他们总共工作了 22000 多个小时——沉着应对了这一局面。起初，他们以为一个燃油泵坏了，准备转向温尼伯。接下来，他们正确地猜测到，伴随着完全失去动力的响亮的“bong”意味着所有的发动机都停止了工作(由于燃料耗尽),并部署了名副其实的冲压空气涡轮，或“rat”。

第一名军官昆塔尔随后认出了他曾经在附近服役过的一个旧的加拿大皇家空军基地。曾被称为皇家空军吉姆利站，昆塔尔不知道的是，它当时被称为吉姆利赛车公园。随后，皮尔逊机长平静地将一架 767 飞机滑行到一个不熟悉的跑道上。

在着陆前不久，机组人员意识到他们没有襟翼或缝翼来减缓飞机的速度，他们飞得太高太快了——皮尔森进行了一次称为向前滑行的滑翔，并在比赛当天安全着陆。再看一下标题照片:

[![Pedal fast, kids](img/4bc567777312230dff044059630a1617.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DkB5AfAs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szm75wollhk515vixj6q.jpg)

一个地勤人员小组从温尼伯开车来发现机体的状态。最讽刺的巧合是，他们的货车在路上没油了。

皮尔逊机长从未接受过如何驾驶波音 767 的训练。标准训练不包括所有发动机关闭时的飞行，机组人员以前甚至从未听到过警告声，即使是在模拟器训练中。然而，他是一个经验丰富的轻型飞机和滑翔机飞行员，可以重新应用这一经验来处理这种情况。

不仅没有人受伤(10 人在着陆后的疏散中受伤)，而且飞机被修复并继续飞行了 25 年。

基础知识在紧急情况下是很有价值的——缺乏基础知识真的会造成伤害。