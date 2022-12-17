# ES6 的新增功能-第 3 部分

> 原文：<https://dev.to/taiwo_xyz/whats-new-in-es6part3-5jg>

这是 ES6 系列新功能的最后一部分。很棒吧？

箭头功能

箭头函数只是我们都知道的另一种编写函数的方式。使用箭头函数有两个主要好处。

一个是它们可以帮助你写出更短的语法，并帮你节省一些击键的时间。

第二个也是更重要的原因是它们允许你在词汇上绑定“this。在过去，你可能不得不把这个变量重命名为其他的名字，或者把它存储在这个变量中，但是在 ES6 中，这已经很好地解决了。让我们看一个例子。

考虑下面的片段。

[https://cdn-images-1 . medium . com/max/800/1 * P8-rst 228 dqo6f-28 xle _ w . png](https://cdn-images-1.medium.com/max/800/1*p8-rSt228dqo6F-28xle_w.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * 9 qyjt 83 cypgskd 9 PE-qsw . png](https://cdn-images-1.medium.com/max/800/1*9QYQJT83cypgskd9PE-QSw.png)

我们得到一个错误，因为第 9 行。它不能识别这里使用的“this”关键字，即使我们在第 4 行中定义了它。解决这个问题的唯一方法是在 return 语句之前创建一个变量，如下所示。

[https://cdn-images-1 . medium . com/max/800/1 * Nun-k b-LQ 31 cxentpgqcva . png](https://cdn-images-1.medium.com/max/800/1*Nun-kB-lq31CxENtPgqCVA.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * zamqzt 7 aus Xu 1 稍 0nQ.png](https://cdn-images-1.medium.com/max/800/1*ZAmqZt7aUSXU1umDPEu0nQ.png)

我们现在可以看到，错误消息没有像以前一样再次出现。难道只有我一个人认为这太多代码行了吗？让我们看看 ES6 是如何处理这一点的。

[https://cdn-images-1 . medium . com/max/800/1 * uqmppc 7 LJ 4s 8 kvd we 6 rkzq . png](https://cdn-images-1.medium.com/max/800/1*UqMPPC7lj4s8kVdWE6RkzQ.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * zamqzt 7 aus Xu 1 稍 0nQ.png](https://cdn-images-1.medium.com/max/800/1*ZAmqZt7aUSXU1umDPEu0nQ.png)

我们可以看到，用更少的代码行，我们仍然能够得到和以前一样的结果。

让我们看看语法的另一个例子。首先，我们将把它写在常规的 JavaScript 中，然后我们将看到 ES6 版本。

[https://cdn-images-1 . medium . com/max/800/1 * u72 zjrwelfnitticefbrw . png](https://cdn-images-1.medium.com/max/800/1*U72ZJrweLfNiTtrCeufBrw.png)

我们都应该知道这段代码的输出是 4。现在让我们来看看 ES6 版本，比较一下哪个版本更快、更容易操作。

[https://cdn-images-1 . medium . com/max/800/1 * yjtxvtip 2 jccakrjrboag . png](https://cdn-images-1.medium.com/max/800/1*YJtXVTiP2jCCAKrjRBoaUg.png)

我们到底改变了什么？首先，我们删除了 function 关键字，并将其替换为等于和大于符号(被视为箭头函数)。注意:如果我们在函数中只有一行声明，那么我们也应该去掉函数的花括号。但是因为我们有 3 行代码，我们使用了花括号。

承诺。

现在，我们将着眼于承诺。承诺到底是什么？

Promise 对象用于延迟和异步计算。听起来很大，对吧？是的，我也猜到了。

我想说的是，promise object 表示一个尚未完成的操作，但预计它将在未来完成。听起来好多了。

让我们看一个例子。

[https://cdn-images-1 . medium . com/max/800/1 *或 9Ctxtr0BopYjvYkQBFoMg.png](https://cdn-images-1.medium.com/max/800/1*O9Ctxtr0BopYjvYkQBFoMg.png)

这只是一个基本的例子，展示了承诺是如何运作的。这将在 3 秒钟后向控制台输出 7。这段代码可能真的没有什么帮助，但它只是向您展示它是如何工作的。让我们做一些更有用的事情。我们用它从 API 获取数据怎么样？

[https://cdn-images-1 . medium . com/max/800/1 * 1 my PAM 4 yclxkdhqrm 2 abzw . png](https://cdn-images-1.medium.com/max/800/1*1MYpam4YClXkdhQRM2ABzw.png)

Woops..这是相当多的代码行。注意:我们在这里使用了一个虚拟的 rest API。

输出:

[https://cdn-images-1 . medium . com/max/800/1 * 78 jeqtipfh 3 py 9 ptfigoqw . png](https://cdn-images-1.medium.com/max/800/1*78JeqtIpFh3py9PtfIGoQw.png)

我们得到的结果是一个显示待办事项列表的对象数组。

现在，让我们将结果打印在网页上，而不是记录到控制台。我们将注释掉控制台语句并打印输出。

[https://cdn-images-1 . medium . com/max/800/1 * a24 和 17yatoumeiw 7 JH 5 jw . png](https://cdn-images-1.medium.com/max/800/1*a24E17yaTouimEiw7jh5Jw.png)

在我们的 HTML 页面中，我们将创建一个包含用户 id 的无序列表。

[https://cdn-images-1 . medium . com/max/800/1 * ig 122 EGA 30 sha 7 dq 0 taaia . png](https://cdn-images-1.medium.com/max/800/1*Ig122eGa30shA7Dq0TaaIA.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * nen 0 cbzp 3 oiz 8 izi 8 和 9Jg.png](https://cdn-images-1.medium.com/max/800/1*NeN0CBzp3OiZ8iz8yCi9Jg.png)

太棒了。我们使用了 ES6 包的几个组件，从模板文字、for-of-loop、promises 到 let 声明。干得好。

发电机。

ES6 中的生成器基本上是可以被传递然后被恢复任意多次的函数。在每个暂停时间，它可以产生或返回一个值。创建生成器类似于创建一个生成器。

将函数转换成生成器的方法是在函数名之前或函数关键字之后加一个星号。让我们来看看它是如何工作的。

[https://cdn-images-1 . medium . com/max/800/1 * aubsdcsgsahjrexpbjzlfg . png](https://cdn-images-1.medium.com/max/800/1*AubSDcsGsAhjrexpBjzLfg.png)

让我们看看第 10 行。如果我们试图调用 gl 函数而不把它存储为一个变量，我们不会得到我们想要的结果。让我们看看输出。

输出:

[https://cdn-images-1 . medium . com/max/800/1 * xqihnj 37k 1 月 1 日 Ajzwo8qQ.png](https://cdn-images-1.medium.com/max/800/1*xqiHnJ37k1IeM_Ajzwo8qQ.png)

注意到创建的对象了吗？它有一个 done 属性，返回一个布尔值，告诉我们产出是否完成。因为我们有 2 个收益，所以它给了我们错误的状态。我们也可以打印出值，而不是我们用……得到的对象。值紧跟在下一个方法之后。让我们尝试修复输出，这样我们就可以使完成状态为真。

[https://cdn-images-1 . medium . com/max/800/1 * 6ja 43 zfdwwnpcpgjqsqziq . png](https://cdn-images-1.medium.com/max/800/1*6ja43ZFDWWNPCpgJQsQziQ.png)

我们可以看到，我们必须分别调用我们的 console.log，以便可以更改我们的 done 状态。我们也注意到如何使用……value 获取存储在对象中的值。return 关键字也用在了我们的生成器函数中。让我们看看我们的输出。

输出:

[https://cdn-images-1 . medium . com/max/800/1 * cerczbd 1 CEM _ zdtz 5 mvhng . png](https://cdn-images-1.medium.com/max/800/1*CERczbD1cem_zDtZ5mVhNg.png)

我们可以利用迭代器来处理这个问题，而不必分别使用 console.log 并打印出返回语句。让我们看看如何使用 for-of-loop 来实现这一点。

[https://cdn-images-1 . medium . com/max/800/1 * Y-sz2m 3 baps 6 rcz q 4 me 5 pg . png](https://cdn-images-1.medium.com/max/800/1*Y-Sz2m3baPs6rCZq4ME5pg.png)

输出:

[https://cdn-images-1 . medium . com/max/800/1 * LRR 66 ogbcv 71 uf 2 egfex 2q . png](https://cdn-images-1.medium.com/max/800/1*lRr66OGBCv71uf2egFEX2Q.png)

多田。我们现在可以看到，我们的 return 语句没有打印出来，我们不必像以前那样编写那么多的 console.log 语句。

在 ES6 中，使用生成器可以做很多事情。

最后，我们已经到了 ES6 系列的结尾

准备好迎接更多挑战。
感谢阅读。