# 代码片段:全 CSS (scss)箭头圆圈满足您的分页需求

> 原文：<https://dev.to/washingtonsteven/snippet-all-css-scss-arrow-circles-for-your-pagination-needs>

所以我为我的网站做了这个，并认为这是一个有趣而简单的分享片段。没有什么太花哨的，但只用 CSS 来做一些表象的东西只会让我头晕！

这里是标记:

```
<span class="page-link prev"><a href="/prev"></a></span>
<span class="page-link next"><a href="/next"></a></span> 
```

轻松点。唯一稍微奇怪的是，`a`标签中没有文本。我们可能想给标签添加一个`title`属性，以确保每个人都知道我们在谈论什么。

还有魔法！在 Sass (scss)中，因为我们都*现代*:(评论中的解释)

```
$circle_diameter: 50px;
$dark_gray: rgba(74, 77, 81, 1);
$yellow: rgba(255, 199, 0, 1);
.page-link {
  a { 
    // We will make this empty a tag a circle
    // Set equal width and height.
    width: $circle_diameter; 
    height: $circle_diameter;

    // This curves in the corners to make a circle.
    border-radius:50%; 

    // Make it look nice.
    background-color:$yellow;

    // We're going to be doing some funky positioning stuff!
    position:relative;

    // Yes! We are using both :before and :after pseudoclasses! Each will be a "leg" of the arrow
    &:before, &:after { 
      // Gotta have this to make the pseudoclasses work.
      content:"";
      // You can mess with these values to change how thick and long the arrow legs are.
      // But you will have to mess with positioning below, as well.
      width:30%;
      height:12%;
      // Something to contrast the $yellow.
      background-color:$dark_gray; 
      // Here it comes...
      position:absolute; 
      // Oh, just center it. 
      // transform will take care of fine tuning the positioning.
      top:50%; 
      left:50%;
    }
  }

  // Here's all the crazy positioning stuff, handled by the "transform" directive.
  // :before becomes the top of the arrow, and :after is the bottom (see the second param of translate()).
  // Note how the values flip between .prev and .next (and the first param in translate value flipping over -50%)!
  // You can tweak these values to make different angles of arrows! 
  // But if you do you will probably have to change the size of the legs above.
  &.prev {
    a:before{
      transform:translate(-60%, -100%) rotate(-45deg);
    }
    a:after {
      transform:translate(-60%, 0) rotate(45deg);
    }
  }

  // Second verse, same as the first!
  &.next {
    a:before{
      transform:translate(-40%, -100%) rotate(45deg);
    }
    a:after {
      transform:translate(-40%, 0) rotate(-45deg);
    }
  }
} 
```

这里的`:before`和`:after`伪类是救命恩人。

这是它最终的样子:

[![](img/69ab85c505b67d2c3b47cc1aa33efabc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yvHOHh_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://washingtonsteven.net/wp-content/uploads/2017/09/Screen-Shot-2017-09-10-at-8.31.17-PM.png)

嗯，至少我认为这很棒。

*本帖最初发表于[washingtonsteven.net](http://washingtonsteven.net/snippet-all-css-scss-arrow-circles-for-your-pagination-needs/)T3】*