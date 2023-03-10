# 从头开始构建你的感知神经网络

> 原文：<https://dev.to/smakosh/build-your-perceptron-neural-net-from-scratch-4co>

*这篇文章最初发表在[媒体](https://medium.com/@ismailghallou/build-your-perceptron-neural-net-from-scratch-e12b7be9d1ef.)T3 上*

让我们先从定义术语开始，Ai(人工智能)、ANN(人工神经网络)、机器学习和深度学习。

人工智能研究领域将自己定义为计算机科学的一个领域，致力于赋予机器看起来像智能的能力，这是我们还没有达到的，因为神经科学还没有解决智能问题。

**ANN 或神经网络**是一种计算系统，其灵感来自构成生物大脑的生物神经网络，但它们看起来如此不同。

[![ON the left: The biological neuron graph & on the right: the artificial neural network](img/38403eb455c2d632b08a434af603f2d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qU0s38XH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AqJGkvQB_okmAVeX3k68D_A.jpeg)

**机器学习**是人工智能中的一个领域，它专注于设计可以从给定数据和结果(我们称之为“训练数据”)中学习的算法，以根据新输入数据中的给定数据进行预测。

**深度学习**是机器学习的一个子领域，专注于相同的目标，但使用神经网络&深度神经网络。

[![Picture taken from Nvidia](img/d27a533afa97b50ef6368dfbd10e8992.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gL2-qLrH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2AvdHyRQfjt9__LKGgJsC2Kg.png)

那么感知器是什么呢？感知器是一种用于监督学习的算法(这意味着我们知道我们试图获得的结果，比如输入房子的大小和位置来预测价格，另一方面，还有一种无监督学习，用于从由输入数据组成的数据集得出结果，而没有标记的响应)。

感知器接收乘以随机权重的输入数据，并添加偏差值，放入激活函数以获得结果，如果结果值是错误的，它使用反向传播和梯度下降返回并调整权重以获得正确的结果。

[![It can be more than 2 inputs !](img/531ca7ce0147db1ce4e829e82021dbde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S7tVyOXr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AobgiEBA8R8Bse2CW9_WN6g.jpeg)

我知道有些术语你没有理解，所以让我们继续慢慢解释，假设你是一个农民，你想手动分类两种类型的花，手动分类的最好方法是拿起每朵花的长度和宽度，在图表上表示出来，如下图所示:

[![Data was taken from Kaggle.com](img/ccf9d5b3991c6983794dfe32444ffdb2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8dIf6e3X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A17HpgWaZYbJTsCKfw7WyAQ.jpeg)

注意到未知的花，我们正在尝试确定它的类型，正如你在图表中看到的，它出现在 2 类花中，这意味着它是 2 类花。

您刚刚手动完成的操作称为线性回归，您已经在图表上表示了作为训练数据的所有花朵，然后您注意到了该模式并在两种类型之间画了一条线。

[![pattern](img/f652ca7ac1e7f963bb7edf8b9f71e99a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--A9n4SNGy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A3nUzKdMe3HJ-8VsD7plCtw.jpeg)

现在让我们使用我们自己从头构建的感知器来自动化它，我们将使用的激活函数是 Sigmoid，它在图上看起来是这样的:

[![Sigmoid function](img/18519d0fc1e30303da26fd7494728813.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uj_jCuaP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1200/1%2A_ek5I9jLpMREpmPygtN0KA.jpeg)

现在让我们了解反向传播和梯度下降的工作原理，为此我们需要:

**成本函数**或又称平方误差函数也叫成本函数:

[![Cost, loss or squared error function](img/6d6675942753e419fcd8cd85fbebac09.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H9jJ7Fmm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AyjUSvPUQmsXiF1QaAK3i1Q.jpeg)

**sigmoid 函数的导数**

[![Derivative of the sigmoid function](img/7310aba93dfd455dc7623bb184650d4e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JbaCjUF8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AHq37bJswK6WICT1vuEVE2g.jpeg)

**成本函数的导数**

[![Derivative of the cost function](img/4af26ea96a71fc21d15f0c81a61b6c44.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1m_aZcrK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AVwm6Qp0WiWgXZ-D5jJyFIA.jpeg)

**成本函数的斜率**为:成本函数的导数乘以 sigmoid 的导数。
**学习率**:权重的导数为 0.2
**，这是输入值，因为常数乘以非常数的导数是常数**偏差的导数**是 1，因为偏差是非常数乘以 1。
然后我们将能够得到**新的权重&偏差**值，如下所示:**

[![& weâ€™re done :D](img/daf1f449257b82becd4af4109bc2215e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--B7GYBvHT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AzAbS_BSLKUktsc6KyPwzTw.jpeg)