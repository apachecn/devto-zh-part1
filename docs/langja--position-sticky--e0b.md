# [lang='ja']我觉得只需要定位粘棒就可以了

> 原文：<https://dev.to/designchips/langja--position-sticky--e0b>

## 初次见面

Dev.to first contributed.

## 位置:粘；について

2017/11/17 当前[can I use ...在](https://caniuse.com/#feat=css-sticky)中确认，在 IE11、Edge15 以前、Android 的旧浏览器中无法正常工作。 另外，Chrome 和 Firefox 也不支持 table 要素相关的跟随。

## CSSとJavaScriptでのSticky

如果使用多填充剂( ex. [stickybits](https://github.com/dollarshaveclub/stickybits) )，则可以通过 JavaScript 进行追踪。

但是，位置: sticky； 通过进行跟随和通过 JavaScript 进行跟随时，显示会稍微出现差异。

## 动画不流畅

在 JS 中安装了随动时，position 属性的值会从 JS 开始变更。

( how-to-achieve-60-fps-animations-with-CSS3) [ [https://medium.com/out systems-experts/how-how 如中所述，在 transform 和 opacity 之外移动元素时，无法生成平滑的(接近 60fps )动画。](https://medium.com/outsystems-experts/how-to-achieve-60-fps-animations-with-css3-db7b98610108%5D%E3%81%A7%E8%A8%80%E3%82%8F%E3%82%8C%E3%81%A6%E3%81%84%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%80%81transform%E3%81%A8opacity%E4%BB%A5%E5%A4%96%E3%81%A7%E8%A6%81%E7%B4%A0%E3%82%92%E5%8B%95%E3%81%8B%E3%81%99%E3%81%A8%E3%80%81%E6%BB%91%E3%82%89%E3%81%8B%E3%81%AA%EF%BC%8860fps%E3%81%AB%E8%BF%91%E3%81%84%EF%BC%89%E3%82%A2%E3%83%8B%E3%83%A1%E3%83%BC%E3%82%B7%E3%83%A7%E3%83%B3%E3%81%AB%E3%81%AF%E3%81%AA%E3%82%89%E3%81%AA%E3%81%84%E3%80%82)

在 JS 上安装的情况下，虽然也可能会因终端而异，但是通过持有的 Android 和周围人的智能手机进行确认后，在切换 position 属性值的时机上发生了一些晃动。 即使是 PC，根据操作速度的不同也会瞬间晃动。

我个人很在意，也不想特意安装有可能在意的东西。

## 到底有必要追随吗

位置: sticky； 为了无效的浏览器，有必要特意用 JS 进行追随吗？ 动画并不平滑，而且本来就需要读入 JS，所以在 JS 执行之前无法追踪。 。 。

而且，大体上，跟踪的要素有时也是标题。 手机的情况尤其如此，但是缩小显示主要内容的区域并追踪标题的意义几乎没有吧。 例如，如果与转换相关联的按钮存在于头部中，并且为了使其持续显示而想要跟随的话，还可以明白。

但是让只包含标志和菜单的标题跟随怎么样？ 因此，我觉得在 JS 上安装很浪费时间，还有更重要的地方吧。

## 渐进式 CSS

这是造词，但 position: sticky； 我觉得支持的浏览器可以追随，不支持的浏览器可以不追随。 [@support](https://dev.to/support) ( position: sticky； ) {}用了的话也可以分支风格，所以我觉得不用那么在意。

也许可以说除了 sticky 之外，因为不太重要的事情，特意找来了 Polifile，在旧的浏览器上确认之后再安装也没有什么意义，而且我觉得不管是什么样的浏览器，外观和行为都必须是一样的。

毕竟只能在自己的浏览器上看。 至少自己不会在多个终端上确认自己制作的网站以外的内容。

如果功能没有破产，外观没有破坏的话，旧的浏览器就不应对了，应该积极地采用新的写法吧。