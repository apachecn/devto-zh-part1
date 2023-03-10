# 哪种线性模型最好？

> 原文：<https://dev.to/realest8agent/which-lm-is-best>

[![trains-railroad_00389817](img/868ac4ad9a3dd53f4bb5d77bb7bf1cdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YoG2lWGf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://realdataweb.files.wordpress.com/2017/05/trains-railroad_00389817.jpg)

*最初发表于:* [Wordpress。](https://realdataweb.wordpress.com/)

最近我一直在做一个 Kaggle 竞赛，参与者的任务是预测俄罗斯的房价。在为挑战开发模型的过程中，我遇到了一些为给定数据集选择最佳回归
模型的方法。让我们加载一些数据来看看。

```
library(ISLR)  set.seed(123)  swiss  <-  data.frame(datasets::swiss)  dim(swiss) 
```

Enter fullscreen mode Exit fullscreen mode

```
## [1] 47  6 
```

Enter fullscreen mode Exit fullscreen mode

```
head(swiss) 
```

Enter fullscreen mode Exit fullscreen mode

```
##              Fertility Agriculture Examination Education Catholic
## Courtelary        80.2        17.0          15        12     9.96
## Delemont          83.1        45.1           6         9    84.84
## Franches-Mnt      92.5        39.7           5         5    93.40
## Moutier           85.8        36.5          12         7    33.77
## Neuveville        76.9        43.5          17        15     5.16
## Porrentruy        76.1        35.3           9         7    90.57
##              Infant.Mortality
## Courtelary               22.2
## Delemont                 22.2
## Franches-Mnt             20.2
## Moutier                  20.3
## Neuveville               20.6
## Porrentruy               26.6 
```

Enter fullscreen mode Exit fullscreen mode

该数据提供了关于瑞士生育率和一些社会经济指标的数据。假设我们想预测生育率。每个观察值
是一个百分比。为了评估我们的预测能力，创建
测试和训练数据集。

```
index  <-  sample(nrow(swiss),  5)  train  <-  swiss[-index,]  test  <-  swiss[index,] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，快速看一下数据。

```
par(mfrow=c(2,2))  plot(train$Education,  train$Fertility)  plot(train$Catholic,  train$Fertility)  plot(train$Infant.Mortality,  train$Fertility)  hist(train$Fertility) 
```

Enter fullscreen mode Exit fullscreen mode

[![swissplots](img/c26c27d649ef531c9afeb1d665efcec4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wfj_RBga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://realdataweb.files.wordpress.com/2017/05/swissplots.png)

看起来这里可能有一些有趣的关系。接下来的代码块
将采用模型公式(或模型矩阵)并
搜索预测值的最佳组合。

```
library(leaps)  leap  <-  regsubsets(Fertility~.,  data  =  train,  nbest  =  10)  leapsum  <-  summary(leap)  plot(leap,  scale  =  'adjr2') 
```

Enter fullscreen mode Exit fullscreen mode

[![leap plot](img/23998f8748b8a6cd51f5d96386e59a3d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--BoUku15S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://realdataweb.files.wordpress.com/2017/05/leap3.png)

根据调整后的 R 平方值(越大越好)，最好的
两个模型分别是:有全部预测器的模型和全部预测器少
检验的模型。两者都将 R 平方值调整到了 0.69 左右，相当合适。拟合这些模型，以便我们可以进一步评估它们。

```
swisslm  <-  lm(Fertility~.,  data  =  train)  swisslm2  <-  lm(Fertility~.-Examination,  data  =  train)  #use summary() for a more detailed look. 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将计算均方误差(MSE)。

```
mean((train$Fertility-predict(swisslm,  train))[index]^2) 
```

Enter fullscreen mode Exit fullscreen mode

```
## [1] 44.21879 
```

Enter fullscreen mode Exit fullscreen mode

```
mean((train$Fertility-predict(swisslm2,  train))[index]^2) 
```

Enter fullscreen mode Exit fullscreen mode

```
## [1] 36.4982 
```

Enter fullscreen mode Exit fullscreen mode

看起来有所有预测的模型稍微好一点。我们在这里寻找一个低 MSE 值。我们还可以查看 Akaike
信息标准(AIC)以了解更多信息。这里越低越好
也一样。

```
library(MASS)  step1  <-  stepAIC(swisslm,  direction  =  "both") 
```

Enter fullscreen mode Exit fullscreen mode

```
step1$anova 
```

Enter fullscreen mode Exit fullscreen mode

```
## Stepwise Model Path 
## Analysis of Deviance Table
## 
## Initial Model:
## Fertility ~ Agriculture + Examination + Education + Catholic + 
##     Infant.Mortality
## 
## Final Model:
## Fertility ~ Agriculture + Education + Catholic + Infant.Mortality
## 
## 
##            Step Df Deviance Resid. Df Resid. Dev      AIC
## 1                                  36   1746.780 168.5701
## 2 - Examination  1 53.77608        37   1800.556 167.8436 
```

Enter fullscreen mode Exit fullscreen mode

这里，没有`Examination`的模型比完整的
模型得分低。这两种型号似乎势均力敌，尽管我可能
倾向于使用没有`Examination`的型号。

由于我们对原始数据进行了采样，以生成训练和测试数据集，
这些测试中的差异会根据所使用的训练
数据而发生变化。我鼓励任何想测试自己的人，把开头的
`set.seed`改一下，重新评估一下上面的结果。甚至
更好:用你自己的数据！

如果你学到了什么或有问题，请随时留下评论或给我发电子邮件。

快乐建模，

基弗