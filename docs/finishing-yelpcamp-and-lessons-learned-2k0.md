# 完成 YelpCamp 和经验教训

> 原文：<https://dev.to/tiffany/finishing-yelpcamp-and-lessons-learned-2k0>

所以我已经完成了 Web 开发者 Bootcamp 和最终项目 YelpCamp，一个用 Node、Express、Mongo、Mongoose、Passport.js 和 Bootstrap 构建的全栈 Yelp 克隆。

我花了四个月和一些变化完成了 45 小时的课程，并完成了 YelpCamp。以下是我对这个过程的一些想法。

## 复习基础知识

我一开始学到的很多东西是对我已经学过的主题的复习。我还是做了，只是为了确保我对基础知识的理解是完全可靠的。

我认为这是非常重要的一步:有些东西我不是很清楚，比如 DOM 操作和 CSS 类等等，这些东西我应该在上课之前就掌握了。很明显，从一开始我就应该继续复习这些介绍性的部分，因为我真的需要它。

## 肉和土豆

最终，我们开始研究 Node、Express 等。我发现，虽然这些是中间话题，但是这些库使得使用 JavaScript *变得更加容易*。它们抽象出了编写 JavaScript 的如此多的繁琐，以至于我希望我能先学会它们，这从来都不是一个好主意 <sup id="fnref-2624-1">[1](#fn-2624-1)</sup> 。

这些部分是最长的，也是解释得最匆忙的。对于新来的人来说，这可能是一个倒胃口的事情，但科尔特在 Udemy <sup id="fnref-2624-2">[2](#fn-2624-2)</sup> 上有最高评级的课程之一，所以我相信新来的人不会太害怕这些部分的*。*

 *## 建立 YelpCamp

首先，让我说:我有点高兴。这是我的第一个全栈应用程序，我和 Colt 一起开发的。我最近添加了一些修改，和伊恩·施罗夫一起，我将添加更多的修改。在我的另一个全栈应用程序 Check Yo Self 中，它也帮助了我，因为我可以将我在 YelpCamp 中学到的知识应用到那个应用程序中。

问题是，这门课程是三年前推出的。Web 开发以极快的速度变化着。一切都过时了。以这个片段为例:

```
//INDEX - show all campgrounds
router.get("/", function(req, res) {
  if (req.query.search && req.xhr) {
    const regex = new RegExp(escapeRegex(req.query.search), 'gi');
    // Get all campgrounds from DB
    Campground.find({
      name: regex
    }, function(err, allCampgrounds) {
      if (err) {
        console.log(err);
      } else {
        res.status(200).json(allCampgrounds);
      }
    });
  } else {
    // Get all campgrounds from DB
    Campground.find({}, function(err, allCampgrounds) {
      if (err) {
        console.log(err);
      } else {
        if (req.xhr) {
          res.json(allCampgrounds);
        } else {
          res.render("campgrounds/index", {
            campgrounds: allCampgrounds,
            page: 'campgrounds'
          });
        }
      }
    });
  }
}); 
```

这是 2017 年 <sup id="fnref-2624-3">[3](#fn-2624-3)</sup> 不再存在的一堆回调地狱。ES6 有*承诺*，ES7 有*异步/等待*。我确信柯尔特现在知道这一点，并在他的新课程中提到了这些特点。然而，这个应用程序可能是几个模块上的一千多行 JavaScript。我没有时间，也不想重构它。这个想法不是我想出来的，但它在我的投资组合中看起来确实不错。

## 结束游戏

我的任务是以我可以成功的速度和学习方式学习全栈 web 开发的基础知识。我已经做到了。现在，它把我学到的东西运用到自我检查中。跳到 YelpCamp 上，看一看。说说你的想法。

* * *

1.  有些人认为不学基础就能马上学会库。这是错误的想法。如果你是大三学生，像我一样，学习基础知识会让你被录用。在称自己为专业 JavaScript 开发人员之前，您需要完全理解一些概念。就这么简单。↩
2.  他的最新课程《高级 Web 开发人员训练营》也有着类似的高评分，尽管它并没有经过深思熟虑，这也体现在他之前课程的学生的评分中。该课程使用了几个老师，但更现代，并使用了 React、ES6 等等。我打算在它再次打折时购买它。↩
3.  难道↩不应该

帖子[结束 YelpCamp 和经验教训](https://tiffanywhite.tech/finishing-yelpcamp/)首先出现在 [Tiffany R. White 博客](https://tiffanywhite.tech)上。*