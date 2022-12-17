# 使用 IntersectionObserver API 在 angularJS 项目中延迟加载图像

> 原文：<https://dev.to/iamafro/lazy-loading-images-in-your-angularjs-project-using-the-intersectionobserver-api-ae9>

[![alt text](img/577b807eb4d52d1fa9fd41cc0d108bee.png)T2】](https://i.giphy.com/media/xUOrw2DnHjY0zleq0o/giphy.gif)

因此，有一段时间我一直试图减少我的项目中滚动监听器的数量，直到我在我的 web 应用程序上运行灯塔测试后偶然发现了交叉点观察器 API。这个实现如此简单明了，以至于我立即着手实现它。

**什么是路口观察者**

交叉点观察器 API 提供了一种异步观察目标元素与祖先元素或顶级文档的视口的交叉点变化的方法。

默认情况下，IntersectionObservers 计算目标元素与页面的可见部分(也称为浏览器的“视口”)重叠(或“相交”)的程度。

我如何启动它？

正如我上面所说，设置非常简单。我们需要做的就是用回调函数定义一个新的交集观察者，然后观察一个目标元素。

说得太多了让我们看看！！

```
const observer = new IntersectionObserver(callback);
observer.observe(element) 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单，我们可以在 DOM 上加载更多的新项目，异步加载图像，一旦目标元素出现在视野中，还可以做更多的事情。让我们看看一个观察者事件是什么样子的。

让我们设置一个基本示例，并将事件记录到控制台。我不会详细说明事件中的每个值代表什么，你可以在这里阅读详细信息。

```
const observer = new IntersectionObserver(callback);
const img = document.getElementById('lazy-img');
observer.observe(img);

function callback(changes){
    console.log(changes)
    changes.forEach(change => {
        console.log(change)
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

观察器通常返回 IntersectionObserverEntry 对象的列表，这些对象包含有关目标元素更改的元数据。

如果我们想检查一个元素在视口中是否完全可见，我们的 obeserver 回调将如下所示:

```
const observer = new IntersectionObserver(callback);
const img = document.getElementById('lazy-img');
observer.observe(img);

function callback(changes){
    console.log(changes)
    changes.forEach(change => {
        if(change.intersectionRatio >= 1){
            img.classList.add('visible')
        }
        else{
            img.classList.add('not-visible')
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

**我们如何用 angularJS 进行惰性加载？？**

放松点。开始吧。

因此，为了将它添加到我们的 angularJS 项目中，我们将创建一个简单的指令，在它进入视口时添加我们的 img src。由于我们的图像还没有 src 属性，我们可以给它添加高度和背景颜色样式。

```
img{
    height: 60px;
    background: grey;
} 
```

Enter fullscreen mode Exit fullscreen mode

那么我们的 javascript 代码应该是这样的:

```
angular
    .module('lazy', [])
    .directive('lazyLoad', lazyLoad)

function lazyLoad(){
    return {
        restrict: 'A',
        link: function(scope, element, attrs){
            const observer = new IntersectionObserver(loadImg)
            const img = angular.element(element)[0];
            observer.observe(img)

            function loadImg(changes){
                changes.forEach(change => {
                    if(change.intersectionRatio > 0){
                        change.target.src = 'boy.jpg'
                    }
                })
            }    

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来是我们的 HTML。应该是这样的:

```
<body ng-app="lazy">
    <div>
        <img alt="" class="" lazy-load>
    </div> 
</body> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！！显然这不够优雅，你可以更进一步，添加一个低分辨率的图像副本，然后在图像出现时用高分辨率副本替换它。我们也可以在图像上添加一个类似模糊的媒介，然后在图像进入视野时显示出来。

让我们用下面的代码替换我们当前的代码，使这个过程更优雅一点。

CSS

```
 .img-blur{
    filter: blur(10px);
}

img{
    height: 60px;
    background: gray;
} 
```

Enter fullscreen mode Exit fullscreen mode

HTML

```
<body ng-app="lazy">
    <div>
        <img src="low-res.jpg" class="img-blur" alt="Lazy load" lazy-load>
    </div>
<body> 
```

Enter fullscreen mode Exit fullscreen mode

JS

```
 angular
    .module('lazy', [])
    .directive('lazyLoad', lazyLoad)

function lazyLoad(){
    return {
        restrict: 'A',
        link: function(scope, element, attrs){
            const observer = new IntersectionObserver(loadImg)
            const img = angular.element(element)[0];
            observer.observe(img)

            function loadImg(changes){
                changes.forEach(change => {
                    if(change.intersectionRatio > 0){
                        change.target.src = 'boy.jpg';
                        change.target.classList.remove('img-blur');
                    }
                })
            }    

        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们有了一个使用 IntersectionObserver API 和 angular 的指令来延迟加载图像的足够好的过程。我相信有很多方法可以让这个过程变得更好，你可以在下面的评论区说出你的想法。