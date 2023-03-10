# 人工智能:2d 平台中的简单导航

> 原文：<https://dev.to/lightest/ai-simple-navigation-in-2d-platformers>

假设你有一个游戏。游戏是一个 2d 平台，NPC 应该可以来回走动。他们还应该知道如何不从平台边缘掉下来。我们如何解决这个问题？

乍一看，我们可能只是让它们朝预期的方向走，当它们足够接近边缘时，让它们掉头。但是如果平台之间可以有差距呢？差距小到 NPC 人更合适的行为是跨过它们？你如何教你的人工智能识别他们应该继续前进还是掉头？最重要的是，我们如何在拥有良好性能的同时解决这个问题？孤独的 NPC 困惑地站在站台上:

在 [CodePen](https://codepen.io) 上看到最轻([@最轻](https://codepen.io/lightest))的笔 [AI 导航篇 part-0](https://codepen.io/lightest/pen/Kmjpvw/) 。

举一反三建议建立某种视觉系统。然后，NPC 会在一定距离处检测它的周围环境，然后它会识别一个足够近的平台，并与它当前行走的平台处于同一水平，然后它可以继续行走。每个 NPC 实例都必须每秒至少处理 60 帧。虽然现代计算机速度很快，但似乎有更好的方法来做出“去还是不去”这样简单的决定。

按照基本原则思考意味着从简单的事实中推理。我们知道每个 NPC 应该做的原子操作是决定是继续前进还是掉头。让我们从只有一个平台的基本情况开始。正如我们先前假设的那样，将 NPC 的位置与平台的每个边缘进行比较就足够了。如果 NPC 足够近，即 NPC 的位置和平台边缘之间的差异足够低，它可以掉头。在下面的例子中，`this`引用 NPC，`this.x`是左边缘，`this.right`是右边缘:

```
if(this.x - floor.x < game.settings.platformEdgeThresh) {
  this.x = floor.x + game.settings.platformEdgeThresh;
  this._direction *= -1;
} else if (floor.right - this.right < game.settings.platformEdgeThresh) {
  this.x = floor.right - this.width - game.settings.platformEdgeThresh;
  this._direction *= -1;
} 
```

Enter fullscreen mode Exit fullscreen mode

请参见 [CodePen](https://codepen.io) 上最轻([@最轻](https://codepen.io/lightest))的笔 [AI 导航文章 part-1](https://codepen.io/lightest/pen/GmbpOg/) 。

知道了 NPC 行走的表面的界限，就可以进行简单的检查来做出决定。如果我们能够以某种方式找出由间隙分离平台形成的一些公共表面的极限，我们就可以实现更复杂的行为，同时保持计算的简单性。确定哪些平台可以统一在一个单一的表面上是我们的出发点。我们通过检查两个平台是否有相同的 Y 位置(这是它们的顶边)以及它们之间足够小的间隙来做到这一点。

```
 loadPlatforms (platforms) {
      var i, j, gap;
      for(i = 0; i < platforms.length; i++) {
        this.addObject(platforms[i]); // adds platform to the global storage
        for(j = i + 1; j < platforms.length; j++) {
          if(platforms[i].y !== platforms[j].y) {
            continue;
          }
          if(platforms[j].right < platforms[i].x) {
            gap = platforms[i].x - platforms[j].right;
          } else if(platforms[j].x > platforms[i].right) {
            gap = platforms[j].x - platforms[i].right;
          }
          if(gap <= game.settings.surfacePlatformGap) {

          }
        }
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们选择多大的差距取决于 NPC 的平均大小和他们的移动速度。非常快，瘦的 NPC 可以跳过大的空隙，而胖的，慢的 NPC 会用他们的身体覆盖空隙。在我们的例子中`game.settings.surfacePlatformGap = 10`。这个间隙连同 Y 等式一起形成了一个**安全条件**。

下一步是找出 NPC 访问公共地表信息的最佳方式。在常规的 2d 平台中，由于碰撞检测，NPC 总是知道它在哪个平台上行走。一旦 NPC 接触到地板，某种处理功能就会被触发，导致 NPC 总是有关于它走过的平台的最新数据。这意味着查询地板平台本身的现有公共表面是我们要走的路。让我们完成`loadPlatform`函数:

```
if(gap <= game.settings.surfacePlatformGap) {
  if(platforms[i].surface !== undefined && platforms[j].surface !== undefined &&
     platforms[i].surface !== platforms[j].surface) {
    surface = this._mergeSurfaces(platforms[i].surface, platforms[j].surface);
    platforms[i].surface = surface;
    platforms[j].surface = surface;
  } else if(platforms[i].surface !== undefined) {
    platforms[i].surface.expand(platforms[j]);
    platforms[j].surface = platforms[i].surface;
  } else if (platforms[j].surface !== undefined) {
    platforms[j].surface.expand(platforms[i]);
    platforms[i].surface = platforms[j].surface;
  } else {
    surface = this._instantiateNavigationSurface(platforms[i].x, platforms[i].y);
    surface.expand(platforms[i], platforms[j]);
    platforms[i].surface = surface;
    platforms[j].surface = surface;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过根据平台的安全状况扩展公共表面，我们实现了 100%安全行走的公共表面。因为表面实例与常规平台具有相同的字段，所以我们以相同的每 NPC 计算成本实现了**可感知的更智能的**行为。这种技术的想法是我在危险的戴夫副本开发过程中产生的——这是我在 2015 年进行的一次编程练习。复制品可以在[这里](http://agafnik.com/testchambers/webdave)买到。当时，我将这种表面命名为“导航表面”，因为 NPC 人可以参考这种表面来了解如何在世界上导航。几天后，我发现完全相同的想法被用在 3d 游戏中，只是名字有点不同——“导航网格”，因为表面的形状更复杂，用网格来描述。更多信息请点击查看[。](https://en.wikipedia.org/wiki/Navigation_mesh)

现在让我们把它们放在一起。NPC 一接触地板，碰撞处理功能就被调用并提供地板平台实例。通过向其查询导航表面，NPC 然后将针对该表面执行边缘接近度检查，或者在不存在的情况下针对地板平台本身执行边缘接近度检查，并且最终将做出去/不去决定。在下面的例子中，`this`引用了 NPC，而`object`是 NPC 此刻与之冲突的东西:

```
handleCollision (object) {
  var floor;
  if(object.y > this.y) {
    this._touchingFloor = true;
    if(object.surface !== undefined) {
      floor = object.surface;
    } else {
      floor = object;
    }
    if(this.x - floor.x < game.settings.platformEdgeThresh) {
      this.x = floor.x + game.settings.platformEdgeThresh;
      this._direction *= -1;
    } else if (floor.right - this.right < game.settings.platformEdgeThresh) {
      this.x = floor.right - this.width - game.settings.platformEdgeThresh;
      this._direction *= -1;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

NPC 安全地走过被各种缝隙隔开的平台:

请参见 [CodePen](https://codepen.io) 上最轻([@最轻](https://codepen.io/lightest))的笔 [AI 导航篇 part-2](https://codepen.io/lightest/pen/EXYaKe/) 。

导航图面显示为带黑色边框的矩形。你可以玩平台的定位，看看我们的功能从他们创造的各种表面。还要确保看到如果`game.settings.surfacePlatformGap`设置得太大会发生什么:)