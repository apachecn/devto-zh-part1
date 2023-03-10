# 利用 Javascript 的力量。地图和。过滤器

> 原文：<https://dev.to/vincecampanale/harnessing-the-power-of-javascripts-map-and-filter>

函数式编程近来一直是热门话题。JavaScript 是一种多范例语言，我们这些幸运的 JS 开发者可以使用的范例之一是函数式范例。如果你认为这东西很酷，当你看完这篇文章后，去谷歌 Elm)

声明:我自己还在学习，所以我使用的一些术语可能不精确或不符合学术/适当的定义。如果你发现一个错误，请在评论中抛出一个异常。也就是说，我对这篇文章的主旨很有信心，所以继续读下去吧！

映射和过滤是函数式编程中的两个重要方法。你不需要了解任何关于函数式编程的知识来知道如何使用它们，我向你保证这两个小函数*将会*改进你的代码。

让我们通过例子来学习。这个练习直接取自我目前正在学习的教程“Javascript 中的函数式编程”，该教程旨在介绍 RxJs(反应式扩展)。[当你读完这篇文章后，来看看](http://reactivex.io/learnrx/)。我将用几种不同的方法来解决同一个问题，以展示`.map()`和`.filter()`的真正魅力。

这里我们有一些包含 JSON 格式的新上映电影列表的数据:

```
//Excellent test data from http://reactivex.io/learnrx/
 var newReleases = [
     {
       "id": 70111470,
       "title": "Die Hard",
       "boxart": "http://cdn-0.nflximg.cimg/2891/DieHard.jpg",
       "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
       "rating": 4.0,
       "bookmark": []
     },
     {
       "id": 654356453,
       "title": "Bad Boys",
       "boxart": "http://cdn-0.nflximg.cimg/2891/BadBoys.jpg",
       "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
       "rating": 5.0,
       "bookmark": [{ id: 432534, time: 65876586 }]
     },
     {
       "id": 65432445,
       "title": "The Chamber",
       "boxart": "http://cdn-0.nflximg.cimg/2891/TheChamber.jpg",
       "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
       "rating": 4.0,
       "bookmark": []
     },
     {
       "id": 675465,
       "title": "Fracture",
       "boxart": "http://cdn-0.nflximg.cimg/2891/Fracture.jpg",
       "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
       "rating": 5.0,
       "bookmark": [{ id: 432534, time: 65876586 }]
     }
   ]; 
```

Enter fullscreen mode Exit fullscreen mode

每个电影都有几个属性:`id`、`title`、`boxart`、`uri`、`rating`和`bookmark`(一个 JSON 对象数组)。

在本教程中，我要解决一个简单的问题:**收集 5.0 评分的电影的 id**。

#### ðÿ'《为了爱圈圈的ðÿ'》

我解决这个问题的第一个方法是利用我们最老的朋友，不起眼的`for`循环。

```
var counter,
  otherCounter,
  favorites = [],
  favoriteIds = [];

for ( counter = 0; counter < newReleases.length; counter++ ) {
  if ( newReleases[counter].rating === 5.0 ) {
    favorites.push(newReleases[counter]);
  }
}

for ( otherCounter = 0; otherCounter < favorites.length; otherCounter++ ) {
  favoriteIds.push(favorites[otherCounter].id);
} 
```

Enter fullscreen mode Exit fullscreen mode

很可爱。这就完成了工作，但是我对这段代码有三个问题:

1 这里有很多代码来完成一个简单的任务。

2 我们制造了很多变量来跟踪我们的值，这是非常浪费内存的。

3 从头到尾遍历电影列表真的很重要吗？我们不能按任何顺序做吗？我们真的*需要*在我们的代码中明确地说明这一点吗？

问自己第三个问题，坐下来认真思考一下。当我们使用`for`循环告诉迭代器遍历一个数组时，我们必须在代码中说明遍历数组的*顺序*。这有时是有用的，但大多数时候我们只是从头到尾——对我来说就像是一个抽象的机会。

#### 为每一个或不为每一个ðÿ"–

`.forEach()`抽象出`for`循环的显式逻辑。我们在我们的`newReleases`数组上调用`.forEach()`,并相信计算机将遍历数组。计算机可以从头到尾遍历数组，首尾相连，中间向外，上下颠倒，真的没关系。要点是:*我们不需要告诉计算机数组是如何被遍历的——我们只需要知道我们要对数组的每个元素做一些事情*。这就是**迭代器函数**的用武之地。迭代器函数是我们给计算机的指令，当迭代机制(隐藏/隐含的`for`循环)遇到数组中的每个元素时*应该发生什么。例如，假设我们想检查一部电影是否有 5 颗星的评级，如果有，就把它推到一个名为`favorites`的新数组中。我们的函数应该是这样的:* 

```
function (movie) {
  if ( movie.rating === 5.0) {
    favorites.push(movie);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将这个函数作为迭代器传递给`.forEach()`，我们对数组中的每个元素运行它。

```
var favorites = [],
    favoriteIds = [];

newReleases.forEach(function(movie) {
  if ( movie.rating === 5.0 ) {
    favorites.push(movie);
  }
});

favorites.forEach(function(movie) {
  favoriteIds.push(movie.id);
}); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我在`for`循环解决方案中遇到的问题仍然存在于`.forEach()`解决方案中。

```
1) Still a lot of code for such a simple task.

2) Still using variables to hold values as we go along.

3) We may have gotten rid of the explicit `for` loops, but I still see the word "for" in there. The extra code defining the order of traversal is gone, but we're still saying "for each element in this array, do something." I think the fact that we want to apply this function to each element should be *implied*. 
```

Enter fullscreen mode Exit fullscreen mode

#### 介绍演出的ðÿœÿstarsðÿœÿ

使用`.map()`和`.filter()`完成工作的时间到了。既然我们确切地理解了需要做什么来解决这个问题，那么反过来理解`.map()`和`.filter()`为我们做了什么应该是容易的。

`.map()`和`.filter()`只是经典`.forEach()`的独特变体。它们的好处在于，它们为我们处理特定的情况，因此我们不必麻烦地告诉计算机“对于这个元素，这样做”。不言而喻，我们希望集合中的每个元素都由 **reducer 函数**处理(与`.forEach()`中的迭代器函数相同)。

`.filter()`在我们想要* *咳咳* *根据某个条件过滤集合中的每个元素时使用。

`.map()`当我们想以某种方式改变数组中的每个元素时使用。我们将每个元素从一个值“映射”到另一个值。

我们期待已久的时刻:

```
var favorites = newReleases.filter(function(movie) {
  return movie.rating === 5.0;
});

var favoriteIds = favorites.map(function(movie) {
  return movie.id;
}); 
```

Enter fullscreen mode Exit fullscreen mode

嗯...较好的...

让我们看看我们最初的痛点并进行比较:

```
1) I still think we could do this with less code.

2) Still skeptical about the need for two variables to compute one value...

3) âœ”ï¸ No more "for"! I'd say this problem is solved. 
```

Enter fullscreen mode Exit fullscreen mode

咻，抽象掉那个`for`循环花费了一些努力，但是现在已经正式解决了。我保证，我们快完成了。

#### filterðÿ"—map

方法链接是一件奇妙的事情。

```
var favoriteIds = newReleases
  .filter(function(movie) {
    return movie.rating === 5.0;
  })
  .map(function(movie) {
    return movie.id;
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这解决了第二个问题。

```
1) Still a bit verbose. I think we could sweeten this up with some syntactic sugar.*

2) âœ”ï¸ One value. One variable.

3) âœ”ï¸ No more "for"! 
```

Enter fullscreen mode Exit fullscreen mode

* *注意:尽管有些人可能相信，Javascript 中的箭头函数不仅仅是语法糖。只是想开个“甜蜜”的玩笑。*

#### 【阿罗兹】——嗯…【阿罗兹】

让我们用一些 ES6 箭头来缩短它。

```
var favoriteIds = newReleases.
  filter( movie => { return movie.rating === 5.0 }).
  map( movie => { return movie.id }); 
```

Enter fullscreen mode Exit fullscreen mode

#### 缩写变量，`const`，&隐式返回

小心行事。有人打电话给消防队。ðÿš'

```
const favIds = newReleases.filter( m => m.rating === 5.0 ).map( m => m.id ); 
```

Enter fullscreen mode Exit fullscreen mode

```
1) âœ”ï¸ Short & sweet.

2) âœ”ï¸ One value. One variable.

3) âœ”ï¸ No more "for"! 
```

Enter fullscreen mode Exit fullscreen mode

`.map()` & `.filter()`不是最好的吗？

了解更多:

这里是我得到这个问题的教程的链接:【http://reactivex.io/learnrx/[。](http://reactivex.io/learnrx/)