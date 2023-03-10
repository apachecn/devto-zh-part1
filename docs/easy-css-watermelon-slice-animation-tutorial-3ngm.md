# 简易 CSS 西瓜片动画教程

> 原文：<https://dev.to/munamohamed94/easy-css-watermelon-slice-animation-tutorial-3ngm>

* *这个帖子以前在 Medium 上发布过。

最近，我开始使用 HTML 和 CSS 创建小的 CSS 动画，我认为这个简单有趣的教程对初学者来说很棒。另外，您将在本教程中学习的 CSS 属性将允许您创建各种其他 CSS 图像或动画。可能性是无限的！

## 设置

所以首先我们需要弄清楚构成一片西瓜的成分。我们有:

*   皮肤
*   肉
*   种子

## 超文本标记语言

记住了组成西瓜的组件，我们现在可以开始编写 HTML 代码了。

```
<div class="skin">
  <div class="flesh">
    <div class="seeds-container">
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
      <div class="seed"></div>
    </div>
  </div>
</div> 
```

这里我们有一个名为 skin 的 div 类，它不仅代表西瓜的**皮**，还将作为西瓜组件的容器。

然后在皮容器中，我们有代表西瓜红色果肉的**果肉**。

接下来，我们有一个名为 **seeds-container** 的 div 类，它将充当种子的容器。使用种子容器，这将允许我们同时控制所有的种子，而不是必须处理每个单独的种子。

这就把我们带到了西瓜的最后一个组成部分。**种子**自己。这取决于你想要西瓜有多少种子，但在这里，我用了 15 个单独的种子。

## 半铸钢ˌ钢性铸铁(Cast Semi-Steel)

### 皮肤

接下来，我们必须对 HTML 代码进行样式化，以创建西瓜。通常，我会使用 SCSS (CSS 预处理器)，但由于这是一个初学者教程，我将使用 CSS。

我们将从设计西瓜皮开始。

```
 .skin {
  margin-top: 150px;  
  margin-right: auto;
  margin-left: auto;
  display: block;
  width: 400px;
  height: 200px;
 -webkit-border-bottom-right-radius: 400px;   
 -webkit-border-bottom-left-radius: 400px;
 -moz-border-radius-bottomright: 400px;   
 -moz-border-radius-bottomleft: 400px;  border-bottom-left-radius: 400px;
  border-bottom-right-radius: 400px;
  box-shadow: 2px 2px 5px black;
  background-color: green;
  transition: all 1s;
  cursor: pointer;
} 
```

我将解释为什么我将每个属性应用于。下面的皮肤。(对于宁愿跳过这一部分的人，向下滚动！)

*Margin-top: 150px* →这将移动。距页边距顶部 150 像素的外观等级。

*Margin-left&Margin-right:auto*→这两个属性一起集中在皮肤上

*宽度:400px，高度:200px* →西瓜片的形状是半圆形，因此宽度是高度的两倍。

*Border-bottom-left-radius:400 px，Border-bottom-right-radius:400 px*→为了创建半圆形状，我们需要应用这两个属性，这两个属性将针对我们的右下角和左下角。皮肤容器；这将使. skin 的底部两个角变圆。不要忘记添加前缀。

盒子阴影:2px 2px 5px 黑色 →为了给人一种轻微的 3D 效果，我给。皮肤容器。

*背景色:绿色* →因为西瓜的皮是绿色的！

*过渡:全 1*→这将有助于我们稍后对西瓜片应用过渡；这将减缓并使过渡平稳进行。

*光标:指针* →这样当我们将鼠标悬停在西瓜片上时，光标会变成指针(即手指指向光标)。

[https://codepen.io/Munamohamed94/embed/VMdLdR%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Munamohamed94/embed/VMdLdR%20?height=500&default-tab=result&embed-version=2)

### 肉

接下来是西瓜片的果肉。我们将在这里使用的属性与我们用于皮肤的属性非常相似。

```
.skin .flesh {
  margin-right: auto;
  margin-left: auto;
  width: 350px;
  height: 180px;
  background-color: red;
 -webkit-border-bottom-right-radius: 350px;   
 -webkit-border-bottom-left-radius: 350px;
 -moz-border-radius-bottomright: 350px;   
 -moz-border-radius-bottomleft: 350px;  
  border-bottom-left-radius: 350px;
  border-bottom-right-radius: 350px;
} 
```

我们使用 margin-right 和 margin-left 来使肉居中，这样它就在皮肤容器中居中。与皮肤容器相比，肉容器的宽度和高度较小。这样我们就可以同时看到西瓜皮和果肉。背景颜色是红色的，因为西瓜的果肉(即内部)是红色的。果肉容器的左下角和右下角的边框半径为 350 像素。这将创建创建半圆形状的圆形边界。不要忘记前缀！

这是我们目前掌握的情况:

[https://codepen.io/Munamohamed94/embed/ZXRGdx%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Munamohamed94/embed/ZXRGdx%20?height=500&default-tab=result&embed-version=2)

### 种子-容器和种子

最后，我们把种子加到西瓜片上。我们首先定义种子容器。这让我们决定种子将出现在西瓜片的什么地方。我们知道西瓜的种子出现在果肉上，所以我们制作了一个与果肉大小完全相同的种子容器。因此，我们将用来定义种子容器的属性将与肉容器上使用的属性相似，如果不是相同的话。

```
.skin .flesh .seeds-container {
  width: 350px;
  height: 180px;
 -webkit-border-bottom-right-radius: 350px;   
 -webkit-border-bottom-left-radius: 350px;
 -moz-border-radius-bottomright: 350px;   
 -moz-border-radius-bottomleft: 350px;
  border-bottom-left-radius: 350px;
  border-bottom-right-radius: 350px;
  overflow: hidden;
} 
```

我们在这里添加的额外属性是 overflow，这是我们没有在肉容器上添加的。我们将溢出设置为隐藏；这是为了当我们稍后插入种子时，如果有任何种子跑出种子容器，它们将被隐藏。

我还使用了 border 属性来确保种子容器和果肉容器的大小完全相同，并且覆盖了相同的空间区域。

现在，种子(我们快到了，我保证！).

```
.skin .flesh .seeds-container .seed {
  margin-left: 18%;
  margin-top: 10%;
  display: inline-block;
  width: 5px;
  height: 2px;
  background-color: black;
 -ms-transform: rotate(60deg);
 -webkit-transform: rotate(60deg);  
  transform: rotate(60deg);
} 
```

*左边距:18%* →这是为了在种子之间创造空间；每个种子和它左边的下一个种子之间将有 18%的间隙。

*margin-top: 10%* →这是为了在上一行和下一行的种子之间留出空间。

*display: inline-block* →这将每个种子定义为一个块，并将它们组织成 inline。

*宽度:5px，高度:2px* →这些宽度和高度值反映出种子相对较小。

*背景色:黑色* →西瓜籽倾向于黑色。

*变换:旋转(60 度)* →将种子顺时针旋转 60 度。不要忘记添加前缀，以便它可以在不同的浏览器上工作！

[https://codepen.io/Munamohamed94/embed/KXedzZ?editors=1100%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Munamohamed94/embed/KXedzZ?editors=1100%20?height=500&default-tab=result&embed-version=2)

## 过渡

因为这是一个初学者的教程，我觉得过渡应该是简单的，但同时也很好。

当光标悬停在西瓜片上时，我们将应用的动画将增加西瓜片图像的比例。我们将使用 transform 属性来实现这一点，确保不要忘记添加前缀。

```
.skin:hover {
   -webkit-transform: scale(1.1); 
   -ms-transform: scale(1.1); 
    transform: scale(1.1);
} 
```

翻译一下上面的代码块，当用户将鼠标悬停在西瓜片的 CSS 图像上时，图像会变大；它将按 1.1 的比例增加。尽管我们将悬停应用于皮肤容器，但 transform 属性将影响皮肤容器的全部内容，因此所有组件的比例都会增加。

正如您所看到的，使用我们之前应用到皮肤的过渡属性，过渡应用了一个均匀的计时函数；这种变化在开始和结束时都很缓慢，只有在中间才会加速。

就是这样！下面你可以看到完成的西瓜片动画！

[https://codepen.io/Munamohamed94/embed/ZXormx%20?height=500&default-tab=result&embed-version=2](https://codepen.io/Munamohamed94/embed/ZXormx%20?height=500&default-tab=result&embed-version=2)

如果你能走到这一步，恭喜你！我希望你喜欢这篇文章！我最初确实打算让它尽可能简单和简短；那不太管用。下次见！👋