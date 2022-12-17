# 我如何重写我的投资组合网站

> 原文：<https://dev.to/aspittel/how-i-re-wrote-my-portfolio-site-cg>

本周，我没有学习新的东西，而是有点痴迷于重写我的[作品集网站](http://aspittel.github.io/)。我决定写一篇关于这个过程和我在这个过程中学到的东西的博文，而不是关于一种技术的典型文章。在这个项目中，我使用了三个我过去没有用过的工具:P5.js、CSS 动画和 FlexBox。

本周早些时候，我在创意编码俱乐部的[网站](http://creativecoding.club/)上注意到一个我喜欢的动画。“创造性编码俱乐部”的标题在悬停时动态移动。我点燃了一支密码笔，开始尝试重新创建它。我做了一个非常不同的动画，字母落下，几秒钟后又回到原来的位置。我最初有一个在 hover 上运行的 CSS 动画；但是，移动鼠标后动画就结束了。我不得不使用 JavaScript 动态添加一个类，当动画结束时，这个类将被删除。

这是我第一次使用关键帧和 CSS 动画，它出奇的简单！相关代码看起来是这样的:

```
@keyframes myanimation {
    0% {
      top: 0px;
    }
    50% {
      top: 50px;
    }
    100% {
      top: 0px;
  }
}

.hovered {
  animation: myanimation 3s;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
Array.from(document.getElementsByClassName('letter')).forEach(letter => {
  letter.addEventListener("mouseover", (e) => {
    letter.classList.add("hovered")
  })
  letter.addEventListener("animationend", (e) => {
    letter.classList.remove("hovered")
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

在对 CSS 做了一些调整后，我最终得到了一个以我名字命名的原型代码笔。我最后真的很喜欢这个动画，我决定用它来运行，并重新设计我的网站的其余部分，以匹配彩虹的名字！无论如何，我都不是一个训练有素的设计师，虽然我可能应该这样做，但我通常不会提前画出我的网站的线框或者使用任何设计软件。我添加一个我预想的功能，然后调整它，直到它在页面上看起来不错。

#### 样机码笔

[https://codepen.io/aspittel/embed/xPgaGJ?height=600&default-tab=result&embed-version=2](https://codepen.io/aspittel/embed/xPgaGJ?height=600&default-tab=result&embed-version=2)

我还决定使用普通的 JavaScript(所以没有框架)，我自己的 CSS(同样没有框架)。

我最近还看到了一些使用 p5.js 的很酷的代码笔。我最喜欢的两个是[互动物理游乐场](https://codepen.io/dissimulate/pen/fhjvk)和[维基百科变化可视化器](https://codepen.io/halvves/pen/rrxakW)。我决定为这个项目尝试学习这个库。我刚刚浏览了 P5 网站上的基本[演示](https://p5js.org/examples/#demos)。我认为根据用户的输入画一堆随机的形状会很有趣。这样做是对网站上演示的一个非常简单的扩展。

#### 互动物理游乐场

[https://codepen.io/dissimulate/embed/fhjvk?height=600&default-tab=result&embed-version=2](https://codepen.io/dissimulate/embed/fhjvk?height=600&default-tab=result&embed-version=2)

#### 维基百科改变可视化器

[https://codepen.io/halvves/embed/rrxakW?height=600&default-tab=result&embed-version=2](https://codepen.io/halvves/embed/rrxakW?height=600&default-tab=result&embed-version=2)

#### 我的随机形状

[![](img/fcb4a6c195554d5e4f4ddfd8252ebcc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HuiVFJkn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/drh87mia4nazv0516m7a.png)

P5 的 JavaScript 代码是这样的:

```
const numShapes = 3
const maxSize = 200

let colors = []
function setup () {
  colors = [
    color(255, 143, 0, 80),
    color(255, 128, 171, 80),
    color(255, 193, 7, 80),
    color(76, 175, 80, 80),
    color(0, 188, 212, 80),
    color(171, 71, 188, 80),
    color(239, 83, 80, 80)
  ]
  createCanvas(window.innerWidth, document.body.offsetHeight)
  noStroke()
}

function randomNumber (size) {
  return Math.floor(Math.random() * size)
}

function randomChoice  (choices) {
  let index = randomNumber(choices.length)
  return choices[index]
}

function mouseClicked () {
  let sideLength = randomNumber(maxSize)
  fill(randomChoice(colors))
  let shapeType = randomNumber(numShapes)
  if (shapeType % numShapes == 0) {
    ellipse(mouseX, mouseY, sideLength, sideLength)
  } else if (shapeType % numShapes == 1) {
    rect(mouseX, mouseY, sideLength, sideLength)
  } else {
    triangle(mouseX, mouseY, mouseX + sideLength, mouseY, 
      mouseX + (.5 * sideLength), mouseY - sideLength)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

文件一加载，setup 函数就开始运行——它初始化一个 HTML 画布。当用户单击画布上的任意位置时，mouseClicked 函数运行。它运行几代随机数来得到大小、颜色和形状。我发现使用它非常简单，以后我还会这样做。

从那里，我不得不向下移动页面，添加关于，投资组合和联系我的部分。自从我在大多数项目中使用像 Bootstrap 和 Materialize 这样的 CSS 库以来，我最近一直在提高我的 FlexBox 技能。我真的很喜欢学习 FlexBox Froggy 和 CSS Grid Garden，如果你想在 CSS Grid 上领先的话。我用它把我的照片和我的简历分成两部分，还有展示我不同作品集的卡片。我仍然认为我在 Safari 和真正的大屏幕上有一些错误，但我认为我已经接近完全实现了！

总的来说，我在整个网站重写的工作中得到了很多乐趣，我的流量也大幅增加了(根据谷歌分析，21，416.7%！).当然，这在很大程度上可以归因于重写后社交媒体的使用，但人们对它的外观真的很好！

我一直采用一种更简约的方法来使用库，这加强了对标准 JavaScript 和 CSS 库的了解。我确实认为 React 或 Vue 会使一些 HTML 更加模块化——最终的 HTML 代码超过 400 行！

我真的很喜欢这个有趣又多彩的设计，尽管它可能会打破三色设计的规则！我认为它很好地代表了我的个性，也展示了我的作品。如果你想查看这个网站，它是 [aspittel.github.io](https://aspittel.github.io) ，代码在我的 [GitHub](https://github.com/aspittel/aspittel.github.io/tree/master) 上！

**我的一部分[关于学习新事物](https://medium.com/on-learning-new-things/learning-new-things-f4db7f16724)系列**