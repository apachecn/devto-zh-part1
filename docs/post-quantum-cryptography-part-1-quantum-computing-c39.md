# 后量子密码术，第 1 部分:量子计算

> 原文：<https://dev.to/artis3n/post-quantum-cryptography-part-1-quantum-computing-c39>

*这篇文章最初发表在[我的博客](https://blog.artis3nal.com/posts/quantum-mechanics)上。*

> 任何能够不晕头转向地思考量子力学的人都没有理解它。

- *尼尔斯·玻尔，量子力学之父之一*

我想讨论两个目前被看好的后量子密码术提案。我还想区分后量子密码学和量子密码学。为此，我必须从量子力学的简要概述开始。这篇文章将涵盖这一简史，并触及量子计算对经典加密标准的影响。后续文章将讨论后量子密码术的提议。我没有经历过量子力学的正规学习，但我会尽力说明相关的观点。这些信息基于我读过的论文和文章；我并不自称是量子力学专家。

### 双缝实验

1801 年，一位名叫托马斯·杨的科学家做了一个实验，实验涉及光和当光照射到有两个狭缝的屏幕上时所表现出来的行为。杨观察到，当光线对着屏幕上的两个狭缝时，并没有像他预期的那样显示出两条光条，而是以明暗条纹的形式呈扇形散开。杨假设光是波的一种形式，这可以解释条纹的图案。当光波击中屏幕时，它在狭缝周围弯曲并与自身碰撞，产生了条纹图案。杨的[双缝实验](https://en.wikipedia.org/wiki/Double-slit_experiment)证明了光的行为像波。

然而，阿尔伯特·爱因斯坦有一个不同的理论。他认为，只有当光实际上是由微小粒子——光子——组成时，条纹图案的光电效应才能得到解释。如果光是一系列光子，那么射到屏幕上的光束会散射光子束。单个粒子在狭缝的另一边相互作用，产生了明暗相间的条纹图案。爱因斯坦在 1905 年提出了这一假设，尽管当时没有人相信他。爱因斯坦最终因为他关于这个主题的论文获得了诺贝尔奖。我们现在知道，事实上，光既可以表现为波，也可以表现为粒子。这个概念被称为[波粒二象性](https://en.wikipedia.org/wiki/Wave-particle_duality)。

现代物理学家重现了杨氏实验的一种形式，通过双缝屏幕发送单个光子。我们的期望是条纹行为模式不会发生，因为一次只有一个光子通过屏幕。然而，结果显示了相同的条纹图案，就好像光子在早期实验中相互作用一样。这让物理学家感到困惑。这种行为在经典物理学中没有解释。

相反，它可以用量子物理学来解释。

量子物理学家中有两种理论来解释单光子实验的条纹行为。

### 叠加

第一种理论是[叠加](https://en.wikipedia.org/wiki/Superposition_principle)。这个想法集中在我们对光子的了解上。我们知道它会离开原来的灯丝，我们知道它会在某个地方碰到屏幕。关于光子状态的其他一切都是未知的。因为光子的路径是未知的，叠加态表示光子同时通过两个狭缝。它的两种状态相互干涉，就好像两个光子在碰撞，这就是为什么会观察到条纹行为。单光子如何能同时通过两个狭缝？叠加论认为，从本质上讲，如果我们不知道粒子在做什么，那么它就在做所有的事情。光子处于[叠加态](http://physics.gmu.edu/~dmaria/590%20Web%20Page/public_html/qm_topics/superposition/superposition.html)。这个想法以埃尔温·薛定谔的猫思维实验的形式广为人知。[薛定谔的猫](https://en.wikipedia.org/wiki/Schr%C3%B6dinger%27s_cat)是一只猫在一个装有氰化物的盒子里的状态未知的想法。要么这只猫还活着，要么它已经踩碎了瓶子，死了。因为状态未知，所以猫同时是活的和死的。它处于叠加态。只有在移开盖子，直接干涉观察时，状态才会集中在一种可能性上。

### 多世界

你开始头晕了吗？双缝实验的另一种量子理论也同样奇怪。它被称为多世界理论。该理论本质上使用薛定谔的思想实验作为对宇宙行为的字面解释。多世界理论指出，当光子(或猫)的状态变得未知时，宇宙分成多个宇宙，每个宇宙代表一种可能的状态。在一个宇宙中，光子通过左边的狭缝。在另一个宇宙中，光子通过右边的狭缝。这两个宇宙以某种方式相互干扰，导致了光的条纹图案。这种字面解释被称为[埃弗雷特公设](https://arxiv.org/pdf/quant-ph/9709032v1.pdf):

> 所有孤立的系统都按照薛定谔方程演化

### 量子计算机

不管哪个理论是正确的，量子力学回答了许多关于我们宇宙状态的问题。这也激发了英国物理学家大卫·多伊奇开始研究量子计算的概念。人们普遍认为，计算机是根据经典物理学运行的。多伊奇认为他们应该服从量子物理学，因为量子物理学的法则更基本。在多伊奇关于这个主题的第一篇论文中，他解释了量子计算机是如何运作的。为了描述这一点，我要借用西蒙·辛格的优秀著作[《密码本](https://simonsingh.net/books/the-code-book/)中的一段话:

> 想象你有一个问题的两个版本。要用普通电脑回答这两个问题，你必须输入第一个版本并等待答案，然后输入第二个版本并等待答案。换句话说，一台普通的计算机一次只能处理一个问题，如果有几个问题，它必须依次处理。然而，有了量子计算机，这两个问题就可以组合成两个状态的叠加，并同时输入——机器本身就会进入两个状态的叠加，每个问题一个状态。或者，根据多世界解释，机器将进入两个不同的宇宙，并在不同的宇宙中回答每个版本的问题。不管解释如何，量子计算机可以通过利用量子物理定律同时解决两个问题。

正如经典计算机以比特(0 或 1)表示数据一样，量子计算机以[量子比特](https://en.wikipedia.org/wiki/Qubit)或量子比特表示数据。一个量子位同样代表 0 或 1，但通常是通过光子的偏振来实现的。当然，一个量子位可以同时是 0 和 1，处于叠加态(或多重宇宙)。量子计算机可以一次处理 x <sup>n</sup> 个运算，而不是一次处理一个运算，其中`x`是系统振动或旋转光子的方向数，`n`是系统可用的光子数。根据你的量子理论，量子计算机可以同时执行 x <sup>n</sup> 运算，或者实际上是 x <sup>n</sup> 计算机，每个都在一个独立的宇宙中，每个都在执行一项计算。你现在头晕吗？

很容易理解为什么量子计算会对现代加密技术构成如此大的威胁。大多数加密算法都是基于“硬”计算机科学问题，如[分解大素数](https://en.wikipedia.org/wiki/Integer_factorization)或解决某些[离散对数问题](https://en.wikipedia.org/wiki/Discrete_logarithm)。如果你能同时进行大部分的计算，这些问题会变得*明显*不那么困难。然而，一段时间以来，没有人真正确定如何制造量子计算机，或者准确地说，如何利用量子计算机危及当前的加密标准。彼得·肖尔在后一项努力中发挥了重要作用。1994 年，Shor 开发了一种可以由量子计算机运行的算法来分解一个巨大的数字，比如一个大素数。运行在合适的量子计算机上的 Shor 算法可以用来破解 RSA。与此同时，Shor 提出了另一种算法，可以被量子计算机用来解决离散对数问题。 [Lov Grover 的算法](http://cryptome.org/qc-grover.htm)同样可以被量子计算机用来破解 [3DES](https://en.wikipedia.org/wiki/Triple_DES) 。我们已经用经典方法为 Grover 处理了 3DES，但是这并没有减少量子密码分析对现代加密的威胁。

然而，我不会在这里深入研究量子密码分析或密码学，因为我这个系列的目的是讨论后量子密码学。量子密码和后量子密码有什么区别？我将在本系列的下一部分讨论这一点以及超奇异同源 Diffie-Hellman。

 <small>:他做了一系列实验来得出这些结论，但是我们在简化。↩</small>
 <small>:有趣的事实:多伊奇是多世界理论的信徒。[↩](#f2)T12】</small>