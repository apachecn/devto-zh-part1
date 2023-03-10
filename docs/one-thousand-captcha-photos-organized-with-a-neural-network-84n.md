# 用神经网络组织的一千张验证码照片

> 原文：<https://dev.to/clarifai/one-thousand-captcha-photos-organized-with-a-neural-network-84n>

在这篇文章中，我们将通过视觉相似性的三个步骤更深入地组织照片:通过神经网络嵌入，通过 t-SNE 进一步降维，以及通过解决分配问题将事物捕捉到网格。然后，我们将通过在您自己的 Clarifai 应用程序上调用我们的一个端点来引导您自己完成这项工作。

下图显示了 Sivakorn、Polakis 和 Keromytis 在“我不是人类:破解 Google reCAPTCHA”中使用的 1024 张验证码照片，这些照片排列在 32×32 的网格上，使得视觉上相似的照片在网格上彼此靠近。

[![1000captcha](img/a8b4ecf66e5defeb9b60a26b67172ddc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kzapsBHt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.clarifai.com/wp-content/uploads/2017/08/1000captcha.jpeg)

## 我们是怎么做到的？

为了从验证码照片集合到上面的网格，我们采取了三个步骤:通过神经网络嵌入，通过 t-SNE 进一步降维，最后通过解决分配问题将东西捕捉到网格。图像自然是非常高维的对象，即使是“小”的 224×224 图像也需要 224*224*3=150，528 个 RGB 值。当天真地表现为巨大的像素向量时，视觉上相似的图像之间可能有巨大的向量距离。例如，左/右翻转将生成视觉上相似的图像，但很容易导致翻转版本中每个像素的值与原始值完全不同的情况。

*备注:这一切的代码都在这里:[https://github . com/clari fai/public-notebooks/blob/master/gridded _ tsne _ blog _ public . ipynb](https://github.com/Clarifai/public-notebooks/blob/master/gridded_tsne_blog_public.ipynb)T3】*

[![](img/8589d48b3956d05b62fea65bef36aaf0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ngl96jbQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/imtagco/blog/2x2captcha.png)

### 第一步:用神经网络从 150528 维减少到 1024 维

我们的照片从 224x224x3 的 RGB 值数组开始。我们将每张图像通过一个现有的预训练神经网络，Clarifai 的[通用嵌入模型](https://developer.clarifai.com/models/general-embedding-image-recognition-model/bbb5f41425b8468d9b7a554ff10f8581)，它为我们提供了来自网络顶层之一的激活。使用神经网络的更高层为我们提供了富含语义信息的图像表示——视觉上相似图像的向量在 1024 维空间中彼此接近。

### 第二步:用 t-SNE 从 1024 维减少到 2 维

为了把事物带到一个我们可以开始绘图的空间，我们必须再次降低维度。我们有很多选择。一些例子:

#### 嵌入学习的归纳方法

像非常难以谷歌的 LIM 博士或具有三重损失的连体网络这样的技术学习一种功能，可以在更少的维度上嵌入新图像，而无需任何额外的再培训。这些技术在基准数据集上表现非常好，非常适合必须索引以前未见过的图像的在线系统。对于我们的应用程序，我们只需要在一个大的、缓慢的步骤中得到一组简化为 2D 的固定向量。

#### 直推式降维方法

我们可以通过学习从高维空间到 2D 的映射来更直接地解决我们的问题，这种映射尽可能地保持高维空间中的距离，而不是学习可以将新点指向几维的函数。有几种技术可供选择:t-SNE 技术和 T2-拉吉维斯技术等等。其他方法，如 PCA，没有针对距离保持或可视化进行优化，往往会产生不太有趣的图。t-SNE，即使在收敛的时候，也能产生非常有趣的剧情(比较一下 [@genekogan](https://twitter.com/genekogan) [这里](https://vimeo.com/191187346)的这个演示)。

我们使用 t-SNE 将我们的 1024D 向量向下映射到 2D，并生成上面网格中的第一个条目。回想一下，我们这里的高维空间是来自神经网络的 1024D 向量嵌入，因此最接近的向量显示对应于视觉上相似的照片。如果没有神经网络，t-SNE 将是一个糟糕的选择，因为初始 224×224×3 向量之间的距离是不感兴趣的。

### 第三步:用 Jonker-Volgenant 算法捕捉网格

t-SNE 嵌入的一个问题是，如果我们直接在相应的 2D 点上显示图像，我们会留下大片空白空间和图像相互重叠的拥挤区域。我们通过构建一个 32×32 的网格来解决这个问题，并将 t-SNE 点移动到网格中，以使移动的总距离达到最优。

事实证明，这个操作非常复杂。有一个完整的数学领域，运输理论，涉及各种情况下最优运输问题的解决方案。例如，如果一个人的目标是最小化所有行进距离的平方和，而不是简单地最小化行进距离的总和(即 l2 Monge-Kantorovitch 传质问题)，则可以通过将指派问题重新转换为计算流体动力学中的指派问题并通过[求解相应的 PDEs](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.7.6791&rep=rep1&type=pdf) 来找到最佳映射。[赛德里克·维拉尼](https://en.wikipedia.org/wiki/C%C3%A9dric_Villani)，2010 年菲尔兹奖获得者，写了一本关于最佳运输理论的伟大的[书](http://cedricvillani.org/wp-content/uploads/2012/08/preprint-1.pdf)，当你厌倦了公司机器学习博客时，值得一看。

在我们的设置中，我们只是希望 t-SNE 点以一种视觉上吸引人并且尽可能简单的方式捕捉到网格。因此，我们通过[线性分配问题](https://en.wikipedia.org/wiki/Assignment_problem)来寻找最小化行进距离总和的映射。教科书上的解决方案是使用[匈牙利算法](https://en.wikipedia.org/wiki/Hungarian_algorithm)，然而，这也可以通过使用 [Jonker-Volgenant](https://blog.sourced.tech/post/lapjv/) 和[开源工具](https://github.com/src-d/lapjv)很容易且更快地解决。

## 我们能有多容易做到这一点？

很简单。除了上面列出的笔记本之外，我们还设置了一个 API 端点，它将为现有的 Clarifai 应用程序生成一个与上面类似的图像。这里我们假设你已经通过访问[https://clarifai.com/developer/account/applications](https://clarifai.com/developer/account/applications/)创建了一个应用程序，并通过调用资源*[https://api.clarifai.com/v2/inputs](https://api.clarifai.com/v2/inputs)*将你喜欢的图片添加到其中。那么你要做的就是这个:

### 第一步:启动异步网格 t-SNE 可视化

由于生成可视化需要一段时间，所以我们异步生成。我们通过调用

```
POST https://api.clarifai.com/v2/visualizations/ 
```

您应该会得到类似下面的响应，通知我们一个“待定”的可视化将被计算。

```
{
    "output": {
        "id": "ca69f34d53c742e1b4a1b71d7b4b4586",
        ...
    }
} 
```

注意 id*ca 69 f 34d 53 c 742 E1 B4 a1 b 71d 7 B4 b 4586*。我们将使用该 id 来获得我们刚刚开始的可视化。

### 第二步:查看可视化是否完成

打电话

```
GET /v2/visualizations/ca69f34d53c742e1b4a1b71d7b4b4586 
```

返回的可视化将“挂起”一段时间，但最终，我们应该会得到这样的响应:

```
{
    "output": {
        "data": {
            "image": {
                "url": "https://s3.amazonaws.com/clarifai-visualization/gridded-tsne/staging/your-visualization.jpg"
            }
        },
        ...
    }
} 
```

最后，`output.data.image.url`包含了你的 t-SNE 可视化网格。

如果你对帖子有任何疑问，可以联系 hackers@clarifai.com。此外，如果你想分享你的 t-SNE 可视化发送给我们！