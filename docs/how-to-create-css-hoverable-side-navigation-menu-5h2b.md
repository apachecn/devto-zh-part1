# 如何创建 CSS 可悬停侧导航菜单

> 原文：<https://dev.to/parthp1808/how-to-create-css-hoverable-side-navigation-menu-5h2b>

在本指南中，我将解释如何使用 Bootstrap、CSS 和可选的 Javascript 创建垂直侧菜单。即使你是一个初学者，你也能够轻松实现并创建一个可悬浮的垂直导航菜单。

[![](img/7844f4b3e218b7f7482ea7879a3659d9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--55zC-cy6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.parthpatel.net/wp-content/uploads/2017/09/vertical-menu.png)

有多种方法可以实现悬浮式垂直菜单，尽管这取决于一些因素。

1.  每个菜单项下的子项数量是否相等？
2.  你想只使用 css 还是也可以使用 jquery 插件？

如果这些问题让你困惑，不要担心。以后，当你看到不同的例子时，你就会明白这些问题的含义了。

### 方法# 1——仅使用 CSS 创建具有相同数量子项目的垂直侧悬浮菜单

这是最灵活的垂直菜单，有很好的用户体验，尽管有一个缺点，你只能在每个菜单项下保持相同数量的子项目，这通常是不常见的。

#### 步骤 1–HTML 标记

首先，我们将创建一个垂直菜单的基本结构，包含 3 个菜单项和每个菜单项中的 3-3 个子项。

请注意，这里我也使用了 fontawesome，但是您不需要使用它。如果你确实想使用 fontawesome 图标，那么不要忘记在标题中添加 font-awesome.css 文件。

```
<nav>
    <div class="menu-item">
      <h4><a href="#"><i class='fa fa-gear'></i> Settings</a></h4>
      <ul>
        <li><a href="#">Account</a></li>
        <li><a href="#">Profile</a></li>
        <li><a href="#">Security</a></li>
      </ul>
    </div>

    <div class="menu-item">
      <h4><a href="#"><i class='fa fa-question-circle'></i> Help</a></h4>
      <ul>
        <li><a href="#">FAQ</a></li>
        <li><a href="#">Submit a ticket</a></li>
        <li><a href="#">Network Status</a></li>
      </ul>
    </div>

    <div class="menu-item">
      <h4><a href="#"><i class='fa fa-envelope'></i> Contact</a></h4>
      <ul>
        <li><a href="#">Phone</a></li>
        <li><a href="#">Email</a></li>
        <li><a href="#">Location</a></li>
      </ul>
    </div>
</nav> 
```

#### 步骤 2–菜单布局

我们将从移除一些默认保证金开始。然后，我们将添加一些基本的字体样式，空白，和垂直导航菜单的宽度。我们将保持菜单宽度为 200 像素。

对于菜单标题，也称为顶级菜单项，我们将设置基本样式，如颜色、字体大小、背景、边框底部等。这将给标题一个独特的外观，使其区别于子菜单项。

```
* {
  margin: 0px;
  padding: 0px;
}

nav {
  font-family: Helvetica, Arial, "Lucida Grande", sans-serif;
  margin: 50px auto; 
  width: 200px;
}

.menu-item {
  width: 200px; 
}

/*Menu Header Styles*/
.menu-item h4 {
  color: #fff;
  font-size: 15px;
  font-weight: 500;
  padding: 7px 12px;
  background: #b90329;
  border-bottom: 1px solid white;
}
.menu-item h4 a {
  color: white;
  display: block;
  text-decoration: none;
  width: 200px;
}

.menu-item h4:hover {  
  background: #cc002c; 
} 
```

#### 步骤 3–子菜单

首先，我们将子菜单作为一个整体。我们将设置子菜单的背景色为白色。然后，我们将设置其他基本样式，如字体大小，行高，填充等。这里可以看到我们将 ul 元素的高度设置为 0。这意味着，默认情况下，子菜单将被隐藏。我们将只在悬停时显示子菜单。此外，我们将列表样式类型设置为无。

对于动画，我们使用高度动画，这将增加使用动画的子菜单的高度。这里我们将悬停时子菜单的高度设置为 93px。但是在您的情况下，这个值可能会不同。如果您使用的子菜单项多于 3 个或少于 3 个，则必须进行相应的设置。

```
/*ul Styles*/
.menu-item ul {
  background: #fff;
  font-size: 13px;
  line-height: 30px;
  height: 0px;
  list-style-type: none;
  overflow: hidden;
  padding: 0px;

  /*Animation*/
  -webkit-transition: height 1s ease;
     -moz-transition: height 1s ease;
       -o-transition: height 1s ease;
      -ms-transition: height 1s ease;
          transition: height 1s ease;
}
.menu-item:hover ul {
  height: 93px;
}
.menu-item ul a {
  margin-left: 15px;
  text-decoration: none;
  color: #aaa;
  display: block;
  width: 200px;
}
/*li Styles*/
.menu-item li {
  border-bottom: 1px solid #eee;
}

.menu-item li:hover {
  background: #eee;
} 
```

[演示](https://www.parthpatel.net/demo/menu-1.html)

这里需要注意的一点是，我们不能将高度设置为自动，因为出于某种原因，这会禁用动画。如果我们可以将高度设置为自动，我们可以在菜单项下使用不同数量的子项目。这就是为什么，我还将展示另一种方法，使用悬停在垂直菜单中显示不相等数量的子项目。

### 方法 2——使用 CSS 和 Javascript 创建侧面悬浮菜单

这是使用 CSS、jQuery 和另一个名为 [jQuery-hoverIntent.js](https://github.com/briancherne/jquery-hoverIntent) 的 jQuery 插件创建垂直扩展菜单的另一种方法。

使用这种方法，保持相同数量的子项没有任何限制。因此，这是实现悬停扩展功能的一个很好的方法，但问题是你需要加载特殊的 jquery 插件来实现。在正常情况下，这应该不是问题。

好，让我们看一个例子。

#### 步骤 1–HTML 标记

首先，我们将创建一个基本结构，它将包含一个有 3 个菜单项和不相等数量的子菜单项的垂直菜单——这是一个正常现象。

```
<div id="container">
  <ul id="nav1">
    <li><a href="#">Portfolio</a>
    <ul>
        <li><a href="#">Web</a></li>
        <li><a href="#">Print</a></li>
        <li><a href="#">Other</a></li>
        <li><a href="#">Print</a></li>
        <li><a href="#">Other</a></li>
    </ul>
    </li>
    <li><a href="#">About</a>
      <ul>
      <li><a href="#">History</a></li>
        <li><a href="#">Meet The Owners</a></li>
        <li><a href="#">Awards</a></li>
    </ul>
    </li>
    <li><a href="#">Menu Heading 3</a>
    <ul>
      <li><a href="#">Stage1</a></li>
      <li><a href="#">Stage2</a></li>
      <li><a href="#">Stage3</a></li>
      <li><a href="#">Stage4</a></li>
    </ul>
    </li>
</ul>
<div> 
```

#### 第二步-菜单布局(CSS)

现在我们将使用一些 css 来设计它。这只是一个演示，所以你可以使用任何你想要的 css 样式，根据你的要求来设计。

请注意，默认情况下，我们隐藏了子项列表。在下一步中，我们将使用 jquery 插件在鼠标悬停时显示它们。

```
ul,
li,
a {
  padding: 0px;
  margin: 0px;
}

#nav1 a {
  color: #FFFFFF;
}

#nav1 li ul li a:hover {
  background-color: #394963;
}

div ul li ul {
  background-color: #4a5b78;
  list-style: none
}

#nav1 > li > a {
  background-color: #343434;
  padding: 16px 18px;
  text-decoration: none;
  display: block;
  border-bottom: 2px solid #212121;
  background: linear-gradient(top, #343434, #111111);
}

#nav1 li ul li a {
  padding: 10px 0;
  padding-left: 30px;
  text-decoration: none;
  display: block;
}

div {
  background-color: #000000;
  background-color: #343434;
  width: 280px;
}
/* Hide Dropdowns by Default */
#nav1 li ul {
  display: none;
} 
```

#### 第三步——使用 jQuery 悬停展开菜单

这里我们使用 jQuery 和 jQuery plugin-jQuery . hover intent . js 来使子菜单在悬停时向下滑动和向上滑动。我们可以只用 jquery 来完成，而不需要加载另一个 jquery 插件，但是这会导致闪烁问题。因此，这个插件的存在就是为了解决这个问题。

你可以从[这里](https://github.com/briancherne/jquery-hoverIntent)
获得 jquery-hoverintent.js 文件

```
$(document).ready(function() {
  $("#nav1 li").hoverIntent(
    function() {
      $(this).find('ul').slideDown();
    },
    function() {
      $(this).find('ul').slideUp();
    });
}); 
```

[演示](https://codepen.io/parthp1808/pen/KvXJmX)

### 结论

有一些其他的技巧可以达到类似的效果，但是它们有一些用户体验问题。我个人认为这些方法是迄今为止最好的。

如果你知道任何其他更好的方法来创建这样的悬浮垂直扩展菜单，请在下面评论。我想在这里补充一下。

帖子[如何创建 CSS 悬浮式侧边导航菜单](https://www.parthpatel.net/create-vertical-hover-expandable-menu/)最早出现在[网站开发者 Parth Patel](https://www.parthpatel.net)上。