# 最佳预测值

> 原文：<https://dev.to/horia141/optimal-predictors-7ac>

这篇文章是关于几个月前我在一次采访中被问到的一个简单的问题。我很高兴自己做对了，尤其是在压力下。然而，为了保护无辜者，我修改了问题陈述的一些部分。

假设我们有一台汽车发动机，希望知道它的当前温度 t，我们可以使用温度传感器来获得温度的测量值。然而，这些读数并不精确。前一分钟我们可能得到一个值，下一分钟可能得到一个稍微不同的值。出于这个原因，法规规定我们应该使用至少两个温度传感器，来自两个不同的供应商，以便获得更可靠的读数。然而，关于如何组合传感器，规则是模糊的。然而，我们希望做好工作，并寻求最佳的测量方法。

为了便于讨论，我将重点讨论最简单的情况，即两个传感器。

第一个障碍是用数学语言正确描述这个问题。我们可以用随机变量 t1 模拟从第一传感器获得的测量值，用随机变量 T2 模拟从第二传感器获得的测量值。我还将假设传感器是良好的，并实际测量温度，没有任何偏差，例如\mathbb{E}[T_1] = t 和\mathbb{E}[T_2] = t，最后，我将假设随机变量完全由它们的方差\mathbb{V}[T_1] = s_1 <sup>2 和\mathbb{E}[T_2] = s_2 <sup>2 表征，这是传感器质量的一种度量。这种差异是已知的，在现实生活中，它将由制造商提供。</sup></sup>

我们现在可以用一种更好的方式来阐述这个问题。我们的目标是找到 f <sup>\star，其中 T = f <sup>\star(T_1，T_2)，使得给定的质量度量 Q[T]最大化。由于我们已经声明，如果给定传感器的方差较低，则其质量较高，因此我们可以使用所得估计量的方差作为质量度量，因此 Q[T]=-\ mathbb { V }[T]=-\ mathbb { V }[f(T _ 1，T_2)]。问题就变成了:</sup></sup>

f<sup>\ star</sup>= \ arg \ min _ { f } \ mathbb { V }[f(T _ 1，T_2)]

我们在模拟这个问题上取得了巨大的进步。但是 f 的形式还是很模糊。应该是什么样子？我们可以使用 f_1(T_1，T_2) = T_1 或 f_2(T_1，T_2) = T_2，也就是说，默认为一个或另一个传感器，甚至 f_{max}(T_1，T_2) = X_{\max(s_1 <sup>2，s_2 <sup>2)}，但这感觉像是一种逃避，基本上是违反规定的。</sup></sup>

原则上，f 可以看起来像任何东西。然而，你会同意我的看法，一个“任意的”函数，比如 f(T_1，T_2) = T_1 <sup>{\cos{T_2}}看起来有点古怪。最自然的方法可能是对两个测量值取平均值，如 f(t1，T2)= t1+T2。这是朝着正确方向迈出的一步，但是，它没有考虑到传感器的不同质量。更好的方法是 f(T_1，T_2) = \alpha_1 T_1 + \alpha_2 T_2 -两者的线性组合。然后可以修改目标，这样我们选择\alpha_1 和\alpha_2 来最小化方差。然而，有一个小问题。阿尔法可以是负数或者非常大等等。同样，减去一个测量值似乎是不自然的。我们应该限制系数为正。更好的是，我们应该强制它们成为凸组合，这样它们的和总是 1。因此，最终测量值是传感器 1 的一点点测量值和传感器 2 的一点点测量值。问题就变成了:</sup>

%\ begin { equation }
\ begin { split }
\ alpha _ 1<sup>\ star，</sup>\ alpha _ 2<sup>\ star</sup>&= \ arg \ min _ { \ alpha _ 1，\ alpha _ 2 } \ mathbb { V }[\ alpha _ 1t _ 1+\ alpha _ 2t _ 2]\
= \ arg \ min _ { \ alpha _ 1，\ alpha _ 2 } \ alpha _ 1<sup>2</sup>\ T

在第一行中，我们使用了方差的一个性质，即，\ mathbb { V }[aX]= a<sup>2 \ mathbb { V }[X]，以及独立随机变量的方差的“几乎”线性。该问题是等式约束凸优化问题。解决这类问题的标准工具是[拉格朗日乘数](https://en.wikipedia.org/wiki/Lagrange_multiplier)。优化问题可以表述为:</sup>

\alpha_1 <sup>\star，</sup>\ alpha _ 2<sup>\ star</sup>= \ arg \ min _ { \ alpha _ 1，\ alpha _ 2 } \ alpha _ 1<sup>2</sup>s _ 1<sup>2</sup>+\ alpha _ 2<sup>2</sup>s _ 2<sup>2</sup>+\ lambda(1-\ alpha _ 1-\ alpha _ 2)

这种形式可以通过无约束凸优化问题的常规工具来攻击。最小值将是沿着\alpha_1、\alpha_2 和\lambda 的梯度为零的点。如果我们看一下\alpha_1，我们有:

%\ begin { equation }
\ begin { split }
\ frac { \ delta } { \ delta { \ alpha _ 1 } }(\ alpha _ 1<sup>2</sup>s _ 1<sup>2</sup>+\ alpha _ 2<sup>2</sup>s _ 2<sup>2</sup>+\ lambda(1-\ alpha _ 1-\ alpha _ 2))&= 0 \
\ frac { \ delta } { \ delta { \ alpha _ 1 } } \ alpha _ 1
s _ 1<sup>2</sup>2 \ alpha _ 1-\ lambda&= 0 \
\ lambda&= 2s _ 1<sup>2</sup>\ alpha _ 1 \
\ end { split }
\ end { equation } %]>

对\alpha_2 应用相同的过程，我们得到 2s _ 2<sup>2</sup>\ alpha _ 2-\ lambda = 0。用前面的表达式替换\lambda 得到:

%\ begin { equation }
\ begin { split }
2s _ 2<sup>2</sup>\ alpha _ 2-\λ&= 0 \
2s _ 2<sup>2</sup>\ alpha _ 2-2s _ 1<sup>2</sup>\ alpha _ 1&= 0 \
s _ 2<sup>2</sup>\ alpha _ 2-s _ 1<sup>2</sup>(1-\ alpha
s _ 2<sup>2</sup>\ alpha _ 2-s _ 1<sup>2</sup>+s _ 1<sup>2</sup>\ alpha _ 2&= 0 \
\ alpha _ 2(s _ 1<sup>2</sup>+s _ 2<sup>2)</sup>&= s _ 1<sup>2</sup>\
\ alpha _ 2&= \ frac { s _ 1

因此，根据我们知道的量(S1 和 S2)，我们得到了α2 的形式。由于\alpha_1 和\alpha_2 的总和必须为 1，因此它们的最终形式为:

%\ begin { equation }
\ begin { split }
\ alpha _ 1&= \ frac { s _ 2<sup>2 } { s _ 1<sup>2</sup></sup>+s _ 2<sup>2 }</sup>\
\ alpha _ 2&= \ frac { s _ 1<sup>2 } { s _ 1<sup>2</sup>+s _ 2<sup>2 }</sup>\</sup>

这绝对是一个非常美好和令人满意的结果。对于非负的 S1 和 S2，α是酉值。因为 ss 是标准差，我们自然可以期望它们满足这个标准。这种依赖的工作方式也很有趣。如果一个传感器的方差相对于另一个传感器的方差较大，则一个系数较小，而另一个传感器的系数较大。有一些边缘情况值得一提。如果 S1 = S2，我们有\alpha_1 = \alpha_2 = 1/2，这是我们所期望的。如果 s_1 = 0，我们有\alpha_2 = 0，也就是说，如果一个传感器提供了完美的信息，我们就不用麻烦另一个传感器了。作为旁注，当两个传感器都完美时，该模型就失效了，尽管它至少表明系数应该是相同的。

我们从一个实际问题开始，通过一系列建模决策和假设，我们想出了一种非常好的方法来组合传感器，并完成了其参数的分析形式。虽然这是一个玩具问题，但它仍然是统计建模和凸优化的一个有趣的应用。它也可以自然地扩展到 k > 1 个传感器的情况，但这是另一个时间的问题。