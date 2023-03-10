# 共享可变性:小心处理！！

> 原文：<https://dev.to/saurabhgiv/shared-mutability-handle-with-care>

这篇文章最初发表在 Medium 上。你可以在这里看一下。

让我们把这两个词分开来，一个一个地理解它们。
*共有*和*易变性*

共享
在计算机科学中，共享是指在多个进程之间共享 I/O、数据等资源。在数据作为资源的情况下，共享可能意味着允许多个进程读取和/或更新它。

*   *共享读取*
    允许多个进程读取一个公共资源，但一次只有一个进程可以修改它。

*   *共享修改*
    多个进程一次可以读取以及修改一个特定的资源。

**易变性**
易变性是指改变的能力。在计算机科学术语中，它可能指以下任何一种:

*   *改变某个变量的值*

```
a = 5
a = 10        # changing the value of variable 
```

*   *修改数据结构*
    在下面的例子中，二叉树的一些节点被删除。
    [![alt text](img/cc8989d50bfdbd29b9824bd391f6926a.png "Logo Title Text 1")T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--REDrKfmH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Avu8VVmvcYJYicfJXxLxerw.png)

*   *修改保存在数据结构*
    中的数据在下面的例子中，链表的第四个节点的值从 100 改为 50。
    [![alt text](img/7780aad8e0ef3be98b2e9a38d8d83d85.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--b7ualpx4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ASS7zqOz_i2A2Wp042ikB7g.png)

> 只要允许单个进程一次修改数据，可变性是很好的。只要多个进程进行共享读取而不是共享修改，共享就没问题。

当上面提到的两个关键词*共享*和*可变性*走到一起，问题就来了。
换句话说，当允许多个进程读取和修改一个公共数据时，问题就出现了。

怎么会？？？？
我们借助一个例子来理解一下:
有一家杂货店

```
groceries = [apple, banana, orange, strawberries, cherries] 
```

和每个杂货店一样，有一个篮子

```
basket = [] 
```

以及从杂货店购买食品的功能

```
def get_groceries():
    for item in groceries:
        if item not in basket:
            basket.append(item)
        print basket 
```

看起来像一段很好的代码！！汤姆来了|拿起篮子|开始从杂货阵列中挑选杂货|把它放进篮子里。

汤姆的生活充满了快乐！！

当他正在购买食品杂货时，杰里来了并且遇见了汤姆。汤姆请杰瑞帮他买食品杂货。杰瑞也开始使用`get_groceries`功能将食品杂货放入**汤姆的篮子**。
[![alt text](img/53b49d71ee1796daf9aa8d8285ad339c.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--uLEFah4M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AFXstOcfLAr2yCDbLRgZDww.png)

通过分析上面的例子，我们可以看到，由于汤姆和杰里都使用同一个篮子，他们都看到香蕉不在篮子里，因此他们都把香蕉放了进去。现在我们篮子里有两根香蕉。

Basket 作为一个共享资源，被多个进程访问，就像 Tom 和 Jerry 一样。

如果杰里只负责挑选篮子，而不往里面放东西，那么汤姆将是唯一一个往里面放东西的人。在这种情况下，上述问题就不会发生。**【类似于共享阅读】**

> *一次一个进程的可变性*

不仅两个进程*共享*篮子**(公共资源)**而且同时*变异* it **(往篮子里添加杂货)**。**【类似于共享修改】**

因此术语**共享可变性**！！