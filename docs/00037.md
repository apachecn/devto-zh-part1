# 拉斯维加斯的强化学习

> 原文：<https://dev.to/michaelmior/reinforcement-learning-for-las-vegas-1523>

在一个部门棋盘游戏之夜，我们正在玩拉斯维加斯的游戏，一个玩家朋友说他想知道游戏中的人工智能会如何表现。由于我一直打算花些时间学习实现神经网络技术，这似乎是一个很好的机会。首先浮现在脑海中的是 DeepMind 团队的一篇[论文](https://arxiv.org/abs/1312.5602)，内容是关于使用深度神经网络实现一种变体 [Q-learning](https://en.wikipedia.org/wiki/Q-learning) 。经典 Q-learning 背后的要点是维护一个表，其中包含给定状态下特定动作的预期效用。在玩游戏时，该表会根据观察到的奖励进行更新。

深度 Q 学习背后的想法是使用神经网络来取代传统使用的表格。一个很大的优势是，使用神经网络可以处理更大的状态-动作空间。第一步是决定如何表示游戏状态。对于不熟悉拉斯维加斯的人来说， [Yucata](http://www.yucata.de/en/Rules/LasVegas) 很好地概括了在线游戏的规则和机制。简而言之，玩家轮流掷骰子，并把它们放在不同编号的赌场上，试图获得最高的现金奖励。

我首先用 Python 为游戏构建了一个简单的类结构来表示所有的游戏状态，并编写了几个函数来实现游戏逻辑。下一步是决定如何将状态输入网络。在最初的深度 Q 学习论文中，作者使用了一个[卷积神经网络](https://en.wikipedia.org/wiki/Convolutional_neural_network)从游戏视频中输入帧。相反，我选择使用以下值显式表示状态:

*   游戏中的玩家数量
*   当前游戏回合数
*   每个玩家当前持有的现金
*   每个赌场当前的骰子数量
*   每个赌场可用的钱
*   当前掷骰子中每个值的骰子数

显式表示状态也会导致网络本身的不同结构。输入层只是接收上述状态值的规范化向量。第二个完全连接的层仅仅是第一层的一半大小。前两层都使用了一个 [ReLU](https://en.wikipedia.org/wiki/Rectifier_(neural_networks)) 激活函数。最终的输出层也是完全连接的，但是具有线性激活函数，并且大小为 6 以表示每个可能的骰子的选择。在训练人工智能对抗 4 个随机的对手后，人工智能能够赢得大约 50%的比赛，鉴于游戏固有的随机性，我对此非常满意。然而，这一评价并不可靠，肯定需要改进。

我后来使用 [Hyperopt](https://github.com/hyperopt/hyperopt) 库实现了超参数优化。在更多的训练之后，我试图优化奖励值、[折扣因子](https://en.wikipedia.org/wiki/Q-learning#Discount_factor)，以及其他特定于深度 Q 学习的参数。这让我把内核初始化器改成了 [LeCun uniform](https://keras.io/initializers/#lecun_uniform) ，把前两层的激活函数改成了 [sigmoid 函数](https://keras.io/activations/#sigmoid)，优化算法从 [RMSprop](https://keras.io/optimizers/#rmsprop) 改成了 [Adam](https://keras.io/optimizers/#adam) 。这主要是为了多一点乐趣，尽管在我尝试的一些简单评估中，它似乎提供了大约 20%的性能改进。

由于这只是一个有趣的附带项目，我议程上的下一件事是使用 [boardgame.io](http://boardgame.io/) 实现一个 UI，这样我就可以感受一下人工智能的“感觉”总而言之，这是一个非常有趣的项目。任何想玩的人都可以在 GitHub 上[获得源代码。](https://github.com/michaelmior/lasvegas)