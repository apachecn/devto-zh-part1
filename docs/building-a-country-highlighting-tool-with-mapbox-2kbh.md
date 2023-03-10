# 使用 Mapbox 构建国家高亮显示工具

> 原文：<https://dev.to/wuz/building-a-country-highlighting-tool-with-mapbox-2kbh>

> 这是我最近在我新建的个人网站上发布的一个旧帖子。感谢阅读！

对于最近的一个项目，我们需要创建一个动态地图，突出显示我们的客户已经完成工作的区域。在评估了需求并研究了我们的选择之后，我们决定使用 [Mapbox](http://mapbox.com/) 来构建这个。他们与 [OpenStreetMap](https://www.openstreetmap.org/) 的集成以及在我们的地图上轻松定制磁贴集和样式的能力立即吸引了我们。

让我们看看我们在建造什么。

[https://codepen.io/wuz/embed/ayOwjY/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wuz/embed/ayOwjY/?height=600&default-tab=js,result&embed-version=2)

在本例中，我们将创建一个弹出窗口，显示该国家的一些基本统计数据。该方法可用于任何类型的地图框 tileset，但我们使用的是来自[自然地球数据](http://www.naturalearthdata.com/downloads/10m-cultural-vectors/)的数据。下面是我们正在使用的矢量 tileset 的链接。点击该页面上的下载国家链接，让我们开始吧！

[![getting your access token](img/508de9bbdd62a5ece7b99ec522d589fe.png)T2】](///static/7f97ce40e3b7dea3a2640e7740a4ecc2/9199c/access_token.png)

要开始构建，您需要创建一个 [Mapbox Studio 帐户](http://mapbox.com/studio)。一旦你注册了，你需要得到你的 API 访问令牌。登录到您的 Mapboxaccount，点击仪表板左下方的帐户按钮。然后点击右上角的 API 访问令牌。寻找您的默认公共令牌。

> 接下来的几个步骤假设您使用本地文件来运行这段代码。如果你想在 Codepen 中使用它，[这里有一个快速模板](https://codepen.io/wuz/pen/Mvaavy?editors=0010)，它包含了项目的开始。

继续复制，然后粘贴到一个名为`main.js`的新 Javascript 文件中，如下所示:

```
mapboxgl.accessToken =
  'pk.eyJ1IjoiYnlmcm9zdC1hcnRpY2xlcyIsImEiOiJjajVsZ3NwZGczMWNtMnFyeTR2cHRnajZ4In0.HOjYrueiLWlhLfhsDCa7wQ'; // Replace with your token 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要一些基本的 HTML 样板文件。创建一个名为 index.html 的文件，并添加以下内容:

```
<!DOCTYPE html>
<html>
  <head>
    My Awesome Map
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div id="map"></div>
    <script src="https://api.mapbox.com/mapbox-gl-js/v0.38.0/mapbox-gl.js"></script>
    <script src="main.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加一些样式。创建一个 style.css 并添加这个:

```
#map {
  height: 500px;
  width: 1000px;
}
#map .mapboxgl-popup-content {
  padding: 10px;
  max-width: 300px;
  padding-top: 20px;
}
#map .mapboxgl-popup-content ul {
  list-style: none;
  margin: 0;
  padding: 0;
  float: left;
}

#map .mapboxgl-popup-content ul h3 {
  margin: 0 0 10px 0;
}

#map .mapboxgl-popup-content img {
  float: left;
  width: 30px;
  margin-right: 10px;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你加载你的页面，你可能什么也看不到。我们的下一步是添加地图。将以下代码添加到 main.js:

```
mapboxgl.accessToken =
  'pk.eyJ1IjoiYnlmcm9zdC1hcnRpY2xlcyIsImEiOiJjajVsZ3NwZGczMWNtMnFyeTR2cHRnajZ4In0.HOjYrueiLWlhLfhsDCa7wQ'; // Replace with your token

var map = new mapboxgl.Map({
  container: 'map', //this is the id of the container you want your map in
  style: 'mapbox://styles/mapbox/light-v9', // this controls the style of the map. Want to see more? Try changing 'light' to 'simple'.
  minZoom: 2, // We want our map to start out pretty zoomed in to start.
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 如果您使用了上面的 Codepen 模板，那么到目前为止您应该已经拥有了所有的东西。

[https://codepen.io/wuz/embed/NvGGjQ/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wuz/embed/NvGGjQ/?height=600&default-tab=js,result&embed-version=2)

此时，我们需要在 Mapbox Studio 中加载我们的自定义 tileset。如果您还没有，请下载前面的自然地球数据。打开 Mapbox Studio 并点击 Tilesets。从那里点击“新建 tileset”

[![the new tileset button](img/b240998b69aec483aadbac828a147ab2.png)T2】](///static/df1071a162ab3b23c92b230933a0045a/e198c/new_tileset.png)

上传你从 Natural Earth Data 下载的整个 zip 文件。

[![uploading the tile set](img/af493646c3a3b276eeedfe81ff99d126.png)T2】](///static/f519c413c22958e7609e3d564f60565f/0a9c2/upload.png)

等待它完成上传和处理。

[![waiting for the upload to finish](img/6129c3e43fa62ab39acf6990d414b4f1.png)T2】](///static/8ad67a0f5ddb65c3a68640ff32abc3ca/fddbb/waiting.png)

完成后，点击你的自定义 tileset。

[![your custom tileset](img/eed10ebd5c52afb4f15018a82a8ab822.png)T2】](///static/13c9e42945e1f2a4e825c9b51e8624e2/232f1/custom_tileset.png)

我们现在需要将这个自定义图块集加载到您的地图中。我们创建了一个地图加载函数。创建一个自定义事件侦听器，并在其中调用 addLayer。

<figure>

[![mapid](img/52ffe311893b05b8a4fe5582fd54fd3d.png)T2】](///static/e2cf2d185ae105634fe10c988d5a98af/02744/mapid.png)

<figcaption>Copy your map ID</figcaption>

</figure>

在右侧栏中查找地图 ID。

<figure>

[![copyname](img/6471e6c4bbe35d24f691005ac3da0b07.png)T2】](///static/ce944423236e16ad5984bce1c429a636/6ff5e/copyname.png)

<figcaption>Copy the name starting with ne_</figcaption>

</figure>

您还需要源层名称，即以`ne_`开头的那个位。获取这个和地图 ID，并将它们添加到 JS 代码中。

```
mapboxgl.accessToken =
  'pk.eyJ1IjoiYnlmcm9zdC1hcnRpY2xlcyIsImEiOiJjajVsZ3NwZGczMWNtMnFyeTR2cHRnajZ4In0.HOjYrueiLWlhLfhsDCa7wQ'; // Replace with your token

var map = new mapboxgl.Map({
  container: 'map', //this is the id of the container you want your map in
  style: 'mapbox://styles/mapbox/light-v9', // this controls the style of the map. Want to see more? Try changing 'light' to 'simple'.
  minZoom: 2, // We want our map to start out pretty zoomed in to start.
});

map.on('load', function() {
  //On map load, we want to do some stuff
  map.addLayer({
    //here we are adding a layer containing the tileset we just uploaded
    id: 'countries', //this is the name of our layer, which we will need later
    source: {
      type: 'vector',
      url: 'mapbox://', // <--- Add the Map ID you copied here
    },
    'source-layer': '', // <--- Add the source layer name you copied here
    type: 'fill',
    paint: {
      'fill-color': '#52489C', //this is the color you want your tileset to have (I used a nice purple color)
      'fill-outline-color': '#F2F2F2', //this helps us distinguish individual countries a bit better by giving them an outline
    },
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们现在应该已经加载了 tileset，您的地图应该看起来像这样:

[https://codepen.io/wuz/embed/WEQQav/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wuz/embed/WEQQav/?height=600&default-tab=js,result&embed-version=2)

现在这不是超级有用的。所有的国家都在显示，这使得很难区分任何东西。让我们稍微过滤一下数据。

为此，我们希望通过 ID 为“ADM0_A3_IS”的 tileset 中的 ISO Alpha3 代码进行过滤。

我们在 load 函数中添加一行来开始过滤:

```
mapboxgl.accessToken =
  'pk.eyJ1IjoiYnlmcm9zdC1hcnRpY2xlcyIsImEiOiJjajVsZ3NwZGczMWNtMnFyeTR2cHRnajZ4In0.HOjYrueiLWlhLfhsDCa7wQ'; // Replace with your token

var map = new mapboxgl.Map({
  container: 'map', //this is the id of the container you want your map in
  style: 'mapbox://styles/mapbox/light-v9', // this controls the style of the map. Want to see more? Try changing 'light' to 'simple'.
  minZoom: 2, // We want our map to start out pretty zoomed in to start.
});

map.on('load', function() {
  //On map load, we want to do some stuff
  map.addLayer({
    //here we are adding a layer containing the tileset we just uploaded
    id: 'countries', //this is the name of our layer, which we will need later
    source: {
      type: 'vector',
      url: 'mapbox://', // <--- Add the Map ID you copied here
    },
    'source-layer': '', // <--- Add the source layer name you copied here
    type: 'fill',
    paint: {
      'fill-color': '#52489C', //this is the color you want your tileset to have (I used a nice purple color)
      'fill-outline-color': '#F2F2F2', //this helps us distinguish individual countries a bit better by giving them an outline
    },
  });

  map.setFilter(
    'countries',
    ['in', 'ADM0_A3_IS'].concat(['USA', 'AUS', 'NGA']),
  ); // This line lets us filter by country codes.
}); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/wuz/embed/RZWWEv/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wuz/embed/RZWWEv/?height=600&default-tab=js,result&embed-version=2)

好多了。现在我们的地图突出显示了三个国家:尼日利亚、澳大利亚和美国。如果我们想添加更多的国家，我们可以只编辑国家代码数组。我们甚至可以从 API 中提取这些代码，并以这种方式添加它们。

最后，让地图具有交互性。为此，我们将使用由 [REST 国家](https://restcountries.eu/)提供的 API。幸运的是，他们有一个接受 ISO Alpha3 代码的端点！

```
mapboxgl.accessToken =
  'pk.eyJ1IjoiYnlmcm9zdC1hcnRpY2xlcyIsImEiOiJjajVsZ3NwZGczMWNtMnFyeTR2cHRnajZ4In0.HOjYrueiLWlhLfhsDCa7wQ'; // Replace with your token

var map = new mapboxgl.Map({
  container: 'map', //this is the id of the container you want your map in
  style: 'mapbox://styles/mapbox/light-v9', // this controls the style of the map. Want to see more? Try changing 'light' to 'simple'.
  minZoom: 2, // We want our map to start out pretty zoomed in to start.
});

map.on('load', function() {
  //On map load, we want to do some stuff
  map.addLayer({
    //here we are adding a layer containing the tileset we just uploaded
    id: 'countries', //this is the name of our layer, which we will need later
    source: {
      type: 'vector',
      url: 'mapbox://byfrost-articles.74qv0xp0', // <--- Add the Map ID you copied here
    },
    'source-layer': 'ne_10m_admin_0_countries-76t9ly', // <--- Add the source layer name you copied here
    type: 'fill',
    paint: {
      'fill-color': '#52489C', //this is the color you want your tileset to have (I used a nice purple color)
      'fill-outline-color': '#F2F2F2', //this helps us distinguish individual countries a bit better by giving them an outline
    },
  });

  map.setFilter(
    'countries',
    ['in', 'ADM0_A3_IS'].concat(['USA', 'AUS', 'NGA']),
  ); // This line lets us filter by country codes.

  map.on('click', 'countries', function(mapElement) {
    const countryCode = mapElement.features[0].properties.ADM0_A3_IS; // Grab the country code from the map properties.

    fetch(`https://restcountries.eu/rest/v2/alpha/${countryCode}`) // Using tempalate tags to create the API request
      .then(data => data.json()) //fetch returns an object with a .json() method, which returns a promise
      .then(country => {
        //country contains the data from the API request
        // Let's build our HTML in a template tag
        const html = ` 
        <img src='${country.flag}' /> 
        <ul>
          <li><h3>${country.name}</h3></li>
          <li><strong>Currencies:</strong> ${country.currencies
            .map(c => c.code)
            .join(', ')}</li>
          <li><strong>Capital:</strong> ${country.capital}</li>
          <li><strong>Population:</strong> ${country.population}</li>
          <li><strong>Demonym:</strong> ${country.demonym}</li>
        </ul>
      `; // Now we have a good looking popup HTML segment.
        new mapboxgl.Popup() //Create a new popup
          .setLngLat(mapElement.lngLat) // Set where we want it to appear (where we clicked)
          .setHTML(html) // Add the HTML we just made to the popup
          .addTo(map); // Add the popup to the map
      });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个突出显示国家的交互式地图！

[https://codepen.io/wuz/embed/ayOwjY/?height=600&default-tab=js,result&embed-version=2](https://codepen.io/wuz/embed/ayOwjY/?height=600&default-tab=js,result&embed-version=2)

* * *

感谢阅读！我刚开始了一个新的时事通讯，我希望你能阅读并注册它！我会分享我写的一些文章，我发现的一些很酷的东西，以及一些我在网上最喜欢的链接。

### 

<center>T3】🎉加入时事通讯🎉</center>

* * *