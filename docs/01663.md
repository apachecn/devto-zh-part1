# Mapbox GL JS 入门

> 原文：<https://dev.to/ardennl/getting-started-with-mapbox-gl-js-8g8>

Mapbox GL JS 是用于显示地图的最好的开源 JavaScript 库之一。这是我最喜欢的谷歌服务的替代品，在这篇文章中我会告诉你为什么。我还会给你一个快速设置地图框的介绍。

## 介绍

我正在做的一个个人项目是一个精美展示旅行和目的地的 webapp。我从长途旅行回来后就有了这个想法，在完成了 Peter Tichy 的 React 课程(包括制作一个基于地图的应用程序)后，我受到了启发。该应用程序是基于 React 的，我可能会把它与[盖茨比](https://www.arden.nl/taking-gatsby-for-a-spin)结合起来，但在我开始之前，我必须学习一两件关于地图的事情。

我想象的概念充满了平滑的平移、缩放和飞向动画标记等等。我开始用谷歌地图开发，但很快就遇到了一些限制，比如标记处理和缩放。因为已经有一段时间了，我不确定这些限制到底是什么，但我确实记得花了很多时间来解决最终不如我希望的那样好的事情。于是我开始寻找替代方案。

输入 [Mapbox GL JS](https://www.mapbox.com/mapbox-gl-js/example/simple-map/) 。Mapbox GL JS 是一个开源的 JavaScript 库，用于显示基于*矢量瓦片*的地图。Mapbox GL JS 由 [Mapbox](https://www.mapbox.com/) 创建，这是一家提供全方位地图服务和产品的公司，包括托管地图和地理定位服务。通过使用 webGL 和 vector tiles，动画看起来特别流畅，并且它们的所有选项和功能都有很好的 API 文档。

### 瓷砖

我了解到的一件事是，大多数在线地图是由按需加载的图块组成的。当你进入[谷歌地图网站](https://www.google.com/maps)并快速移动地图时，你可能会看到到处都是灰色的小故障。这些是当你进入一个新的区域时被加载的牌。瓦片有两种规格:[矢量瓦片](http://wiki.openstreetmap.org/wiki/Vector_tiles)和光栅瓦片。据我所知，矢量瓷砖是最具性能的动画。

### 地图托管

现在有一件事我从来没有想过:地图需要被托管在某个地方，而托管不是免费的！谷歌地图提供了非常慷慨的个人限制，但他们的地图数据在谷歌生态系统之外是不可访问的。不过，有几个选项可以让您在没有任何初始成本的情况下进行设置:

**Mapbox**
Mapbox GL JS 背后的公司有一个[地图服务](https://www.mapbox.com/pricing/)，里面有一些华丽的地图[卫星地图](https://www.mapbox.com/mapbox-gl-js/example/satellite-map/)很惊艳)。您还可以访问 [Mapbox Studio](https://www.mapbox.com/mapbox-studio/) ，创建自定义地图，添加各种自定义数据和数据层。Mapbox 服务一个月免费提供多达 50，000 次地图浏览，这并不算多，因为一次地图浏览只需要加载四个地图块，大概需要 1000 次浏览。这甚至不是独特的访问，所以其中一半将是你玩自己的创作，因为一切都是如此顺利。在这 1000 次访问之后，东西很快就变得昂贵了。无论如何，你不需要填写任何支付信息就可以开始，所以这是一个很好的服务。

Mapzen
[Mapzen](https://mapzen.com/) 提供与 Mapbox 类似的服务和限制，他们也有一些漂亮的地图。我还没有尝试过 Mapzen，但我认为加载地图的工作方式与其他服务类似。

**Tilehosting.com**
[Openmaptiles.org](https://openmaptiles.org/)曾经为开源项目提供无限量的免费地图托管服务，但最近开始在[tilehosting.com](https://tilehosting.com)提供服务。开源/免费网站可以使用他们的免费服务，每月允许 2000 次访问，并有一些不错的默认地图可用。他们称之为“软限制”，希望这意味着他们不会太严格。商业计划起价 40 美元，并提供一些额外服务和高级地图。

使用 tileserver
你可以使用 tileserver 托管地图切片。[Openmaptiles.org](https://openmaptiles.org/)有一款开源的 tileserver，而且有更多的选择。这个[牛逼的矢量瓷砖](https://github.com/mapbox/awesome-vector-tiles)回购似乎有一些关于这个话题的好资源。自托管地图是一个兔子洞，我还没有下去，但最终可能是唯一可行的选择。如果我走到那一步，我一定会写一篇关于它的文章。

## 设置地图

对于这篇文章，我将创建一个涵盖一些一般用户案例的地图:

*   自定义标记
*   将地图装订并居中
*   单击时平移和缩放
*   基于 JSON 数据。

### 获取 Mapbox GL JS 和地图提供商

首先你需要 Mapbox GL JS 库。你可以在 NPM、Github 上找到这个库，也可以使用 Mapbox 提供的 CDN。为了正确显示地图，您需要包括脚本和样式表。

接下来，您需要一个地图提供商。当你使用 Mapbox 时，你一注册就会收到一个访问令牌。您需要做的只是提供访问令牌和基本的地图脚本，这样您就有了一个地图:

```
<div id='map' style='width: 400px; height: 300px;'></div> <script>
mapboxgl.accessToken = ‘token’;
var map = new mapboxgl.Map({
    container: 'map',
    style: 'mapbox://styles/mapbox/streets-v9'
});
</script> 
```

通过使用带有前缀`mapbox://`的链接之一来选择地图框样式。

如果您使用 Tilehosting.com，您需要首先请求数据。一些箭头函数可能看起来像这样:

```
// Initialise map with the returned data
const initMap = (mapData) => {
    const map = new mapboxgl.Map({
            container: 'map',
            style: mapData,
            zoom: 8,
            center: [-9.1952226, 38.7436214],
    });
}
// Fetch and return data from the url provided by Tilehosting.com
fetch('https://free.tilehosting.com/styles/darkmatter/style.json?key=yourKeyHere)
.then(response => response.json())
    .then(json => initMap(json)); 
```

当然，不要忘记设计容器的样式。查看 [API 文档](https://www.mapbox.com/mapbox-gl-js/api/)，了解所有可能的设置选项。

### 自定义标记

Mapbox GL JS 中的许多默认函数都从使用 GeoJSON 数据结构中受益匪浅。当使用自定义标记或类似的东西时，你的数据是如何组织的并不重要。在下面的例子中，我使用了一些 GeoJSON 格式的数据，但是因为我们只是使用了坐标，所以你的数据如何格式化并不重要，只要你可以循环它。

在`initMap`函数中，我们将遍历数据，为每组坐标创建一个标记:

```
geojson.features.forEach((marker) => {
        const markerEl = document.createElement('div');
        markerEl.innerHTML = '📌';        
        new mapboxgl.Marker(markerEl, { offset: [5,-5] })
                .setLngLat(marker.geometry.coordinates)
                .addTo(map);
}); 
```

分解一下:

*   首先，我们用一个`forEach`循环遍历数据。
*   对于列表中的每个数据条目，我们创建一个新的 HTML 元素。我们用表情符号设置元素的内部，但你可以在这里使用或创建任何你想要的东西。
*   我们使用`mapboxgl.marker`将每个标记添加到地图上，并用坐标设置经度值。`setLngLat`将接受一个对象或一个数组。`offset`将标记偏移到真正的中心点。

如果我们想平移和缩放地图，Mapbox GL JS 有一个`flyTo`功能，我们可以点击使用。在`forEach`循环中，我们可以向新创建的 HTML 元素添加一个事件处理程序

```
markerEl.addEventListener('click', () => {
            map.flyTo({
                center: marker.geometry.coordinates,
                zoom: 11,
            });      
    }); 
```

默认的`flyTo`动画不错，但是你也可以设置自己的缩放、速度和曲线。

### 将地图绑定到标记

现在我们有了我们的标记，我们希望我们的地图绑定到他们的坐标。为此你可以创建一个 [`lngLatBounds`](https://www.mapbox.com/mapbox-gl-js/api#lnglatbounds#extend) 对象，你可以用你的标记的坐标来扩展它。为此，请将对象分配给一个新变量:

```
const bounds = new mapboxgl.LngLatBounds(); 
```

在标记循环中，用您的坐标扩展`bounds`变量:

```
bounds.extend(marker.geometry.coordinates); 
```

`bounds`现在是具有最大东北和西南坐标的对象。接下来，我们将编写一个函数来将地图从起始位置动画到新的边界。为此，我们将使用`load`事件来确保所有数据都已加载:

```
map.on('load', (e) => {        
        map.fitBounds(bounds, {
                padding: { top: 50, bottom: 50, left: 50, right: 50 },
            easing(t) {
                    return t * (2 - t);
            },
        });        
}); 
```

`fitBounds`函数有几个选项([在这里查看完整列表](https://www.mapbox.com/mapbox-gl-js/api/#map#fitbounds))。现在我们只使用填充来确保我们的标记都不会太靠近边缘。

### 结果

为了得到这个结果，我将 markers 代码和 fitbounds 代码放入不同的函数中，对代码进行了一些清理。随意在 Codepen 上叉这个！

[https://codepen.io/aderaaij/embed/mpVXzB/?height=467&theme-id=0&default-tab=result&embed-version=2%20?height=500&default-tab=result&embed-version=2](https://codepen.io/aderaaij/embed/mpVXzB/?height=467&theme-id=0&default-tab=result&embed-version=2%20?height=500&default-tab=result&embed-version=2)

T2】

## 地图框和反应

在创建几个测试地图时，我还意识到，一个包含不同数据层的交互式地图会很快变得复杂。这也是我在 React 中继续我的 Mapbox GL JS 冒险的原因。有两个 Mapbox GL React 库:优步的 [React Mapbox GL](https://github.com/alex3165/react-mapbox-gl) 和 [React Map GL](https://github.com/uber/react-map-gl) 。我试过优步的那个，但最终换成了 React Mapbox GL，因为它更灵活一些。Mapbox 也有一些 React 配合 Mapbox GL JS 使用的例子可用:【https://github.com/mapbox/mapbox-react-examples。

## 结论

Mapbox GL JS API 的用途极其广泛，包括大量的事件和选项，用于处理数据和渲染我们还没有接触过的图层。有了所有的事件，很容易在地图动画的开始、过程或结束时让事情发生，这为交互带来了许多可能性。
当将 Mapbox GL 与 React 结合起来时，复杂的地图交互变得易于管理，因此在下一篇博文中，我将解释如何建立一个基于 React 的 Mapbox web 应用程序，并为其添加一些漂亮的动画标记。

*本文由[米克尔·卡诺](https://www.linkedin.com/in/miquel1/)编辑。*