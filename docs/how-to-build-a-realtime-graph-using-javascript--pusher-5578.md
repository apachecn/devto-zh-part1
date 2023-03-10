# 如何使用 Javascript & Pusher 构建实时图形

> 原文：<https://dev.to/mappmechanic/how-to-build-a-realtime-graph-using-javascript--pusher-5578>

这个世界现在需要一切超快的东西。不同的系统每天都会产生大量的数据流。如今，实时监控和分析对于许多行业的决策已经变得非常重要。例如:实时监控网站流量、服务器性能、天气更新、IOT 传感器等。分析和解释这些数据是很重要的，对于这些数据，交互式图表是一个很好的解决方案。

在这篇博文中，我们将构建一个 NodeJS 服务器来公开 API，以便为一个指标(在本例中是伦敦市的天气)提供历史数据，并且还提供一个 API 来接收新的数据点。我们还将建立一个前端应用程序，用折线图实时显示伦敦天气的温度变化。我们构建的应用程序将如下所示:

[![](img/2ccdf6b8583e5a3a66625dd0b4a68351.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mL3yfc6B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-realtime-graph-app-javascript.gif)

## 签约为推手

开始本教程的第一步是[在 Pusher](https://pusher.com/signup) 注册，或者如果你已经有一个帐户，用你现有的凭证登录。登录后，您将需要创建一个新的应用程序，并为前端选择“Vanilla JS”，为后端选择“NodeJS”。然后，您将被带到一个包含前端和后端“入门”代码的登录页面，我们将在本教程的后面使用。

## NodeJS 服务器 API 用于监控&分析系统

对于任何指标或实体的任何分析系统，最基本的 API 是:

1.  摄取 API——为任何特定实体摄取新数据点的 API。在这篇博文的服务器中，我们将制作一个 API，在特定时间接收伦敦市的新温度数据。这个 API 可以被任何全球天气系统或任何 IOT 传感器调用。
2.  历史数据 API——这个 API 将及时返回从这个日期开始的一个范围内的所有数据。对于我们的服务器，我们将创建一个简单的 API，该 API 将返回一些静态的历史数据，其中包含伦敦任一天的温度值的有限数据点。

### NodeJS Express 服务器框架

我们将创建一个基本的 Express 服务器，并实例化 Pusher 库服务器实例。我们将为我们的项目创建一个新文件夹，并创建一个新文件 **server.js** 。将以下代码添加到该文件中:

```
var express = require('express');
var path = require('path');
var bodyParser = require('body-parser');

var Pusher = require('pusher');

var pusher = new Pusher({
  appId: '<your-app-id>',
  key: '<your-app-key>',
  secret: '<your-app-secret>',
  cluster: '<your-app-cluster>',
  encrypted: true
});

var app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(express.static(path.join(__dirname, 'public')));

// Error Handler for 404 Pages
app.use(function(req, res, next) {
    var error404 = new Error('Route Not Found');
    error404.status = 404;
    next(error404);
});

module.exports = app;

app.listen(9000, function(){
  console.log('Example app listening on port 9000!')
}); 
```

Enter fullscreen mode Exit fullscreen mode

**获取历史温度数据的 API**

现在，我们将添加一些关于伦敦一天中特定时间的温度的静态数据，并将其存储在任何 JavaScript 变量中。我们还将公开一个路由，以便每当有人使用 GET HTTP 调用来调用它时返回该数据。

```
 var londonTempData = {
    city: 'London',
    unit: 'celsius',
    dataPoints: [
      {
        time: 1130,
        temperature: 12 
      },
      {
        time: 1200,
        temperature: 13 
      },
      {
        time: 1230,
        temperature: 15 
      },
      {
        time: 1300,
        temperature: 14 
      },
      {
        time: 1330,
        temperature: 15 
      },
      {
        time: 1406,
        temperature: 12 
      },
    ]
  }

app.get('/getTemperature', function(req,res){
  res.send(londonTempData);
}); 
```

Enter fullscreen mode Exit fullscreen mode

**API 摄取温度数据点**

现在，我们将添加公开 API 的代码，以便在特定时间接收温度。我们将公开一个以温度和时间作为查询参数的 GET HTTP API。我们将验证它们不为空，并通过推入静态 Javascript 变量 **londonTempData** 的**数据点**数组来存储它们。请将以下代码添加到 **server.js** 文件中:

```
app.get('/addTemperature', function(req,res){
  var temp = parseInt(req.query.temperature);
  var time = parseInt(req.query.time);
  if(temp && time && !isNaN(temp) && !isNaN(time)){
    var newDataPoint = {
      temperature: temp,
      time: time
    };
    londonTempData.dataPoints.push(newDataPoint);
    pusher.trigger('london-temp-chart', 'new-temperature', {
      dataPoint: newDataPoint
    });
    res.send({success:true});
  }else{
    res.send({success:false, errorMessage: 'Invalid Query Paramaters, required - temperature & time.'});
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，除了存储在数据源中，我们还将在一个新的通道**‘伦敦温度图表’**上触发一个事件**‘新温度’**。对于每个唯一的数据源或图表，您可以创建一个新的通道。

由我们的服务器触发的事件将由前端处理，以实时更新图表/图形。事件可以包含图表正确显示数据点所需的所有重要数据。在我们的例子中，我们将把新时间的温度发送到我们的前端。

## 使用 Vanilla JS 构建前端应用& Chart.js

现在，我们将构建前端应用程序来显示一个折线图，表示伦敦市一天中不同时间的温度变化。显示实时图形的主要方法是

1.  我们必须进行一次初始 Ajax 调用来获取历史数据，并使用现有数据呈现图形。
2.  我们将订阅存储在特定通道上的新数据点的任何事件。

### 基本 HTML 模板

我们将在我们的项目根目录中创建一个名为 **public** 的新文件夹，然后在这个文件夹中创建一个新文件【index.html**。该文件将包含基本的 HTML 代码，以呈现一个简单的标题和一个带有应用程序名称的子标题以及几个图标。我们还将从其 CDN URL 导入 **Pusher Javascript** 库。** 

```
<!DOCTYPE>
<html>
    <head>
        Realtime Analytics
        <link rel="stylesheet" href="https://unpkg.com/purecss@0.6.2/build/pure-min.css" integrity="sha384-UQiGfs9ICog+LwheBSRCt1o5cbyKIHbwjWscjemyBMT9YCUMZffs6UqUTd0hObXD" crossorigin="anonymous">
        <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Raleway:200">
        <link rel="stylesheet" href="./style.css">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>
    <body>
        <header>
            <div class="logo">
                <img src="./assets/pusher-logo.png" />
            </div>
            <div id="logout" class="logout">
               <a href="/logout">Logout</a>
            </div>
        </header>
        <section class="subheader">
            <img class="weather-icon" src="./assets/weather.png" />
            <h2>Realtime Weather Updates for London !</h2>
            <img class="weather-icon" src="./assets/london-icon.png" height="70px" />
        </section>
        <section>
           <div id="loader" class="loader">
           </div>
        </section>
        <script src="https://js.pusher.com/4.0/pusher.min.js"></script>
        <script type="text/javascript" src="./app.js"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 添加图表库

在 Javascript 和 HTML 应用程序中，我们必须使用这两种技术中的任何一种来构建图形组件来表示数学图形，SVG 或 Canvas。有许多开源库可以帮助您呈现不同的图表类型，如条形图、饼图、折线图和散点图。以下是一些流行图书馆的链接:

*   高图表-[http://www.highcharts.com/](http://www.highcharts.com/)
*   chart . js-[http://www.chartjs.org/](http://www.chartjs.org/)
*   谷歌图表-[https://developers.google.com/chart/](https://developers.google.com/chart/)
*   http://www.flotcharts.org/
*   D3 JS-[http://d3js.org/](http://d3js.org/)

对于我们的项目，我们将选择 Chart.js，因为它有相当简单的 API，并使用 Canvas HTML 标记呈现健壮的图表。您可以选择任何图表库，但请记住，该库应该能够更新图表，而无需完全重新呈现它。Chart.js 在任何实例化的图表上提供一个方法来更新它。

将以下代码添加到 index.html 文件的适当位置

```
...
<section>
   <div id="loader" class="loader">
    Loading...
   </div>
   <canvas id="weatherChart">
   </canvas>
</section>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.5.0/Chart.bundle.min.js"></script>
<script src="https://js.pusher.com/4.0/pusher.min.js"></script>
... 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 JS 文件&实例化推送器客户端库

现在，我们将在我们的公共文件夹中创建一个新文件 **app.js** ，并添加以下代码来实例化 Pusher 客户端库。

```
// Using IIFE for Implementing Module Pattern to keep the Local Space for the JS Variables
(function() {
    // Enable pusher logging - don't include this in production
    Pusher.logToConsole = true;

    var serverUrl = "/",
        members = [],
        pusher = new Pusher('<your-api-key>', {
          encrypted: true
        }),
        channel,weatherChartRef;

    function showEle(elementId){
      document.getElementById(elementId).style.display = 'flex';
    }

    function hideEle(elementId){
      document.getElementById(elementId).style.display = 'none';
    }

    function ajax(url, method, payload, successCallback){
      var xhr = new XMLHttpRequest();
      xhr.open(method, url, true);
      xhr.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
      xhr.onreadystatechange = function () {
        if (xhr.readyState != 4 || xhr.status != 200) return;
        successCallback(xhr.responseText);
      };
      xhr.send(JSON.stringify(payload));
    }

})(); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们还添加了一些实用方法来进行 Ajax 调用，并显示或隐藏 DOM API 中的元素。

### 添加代码取历史数据

现在，我们将添加代码来获取历史温度数据，以显示带有初始值的图表。我们还将使用特定的配置实例化一个新的图表对象，以呈现折线图。您可以在 [Chart.js 文档](http://www.chartjs.org/docs/)中阅读更多关于如何构建这些配置的信息。

请将以下代码添加到 app.js 文件中:

```
 function renderWeatherChart(weatherData) {
      var ctx = document.getElementById("weatherChart").getContext("2d");
      var options = { };
      weatherChartRef = new Chart(ctx, {
        type: "line",
        data: weatherData,
        options: options
      });
   }

   var chartConfig = {
      labels: [],
      datasets: [
         {
            label: "London Weather",
            fill: false,
            lineTension: 0.1,
            backgroundColor: "rgba(75,192,192,0.4)",
            borderColor: "rgba(75,192,192,1)",
            borderCapStyle: 'butt',
            borderDash: [],
            borderDashOffset: 0.0,
            borderJoinStyle: 'miter',
            pointBorderColor: "rgba(75,192,192,1)",
            pointBackgroundColor: "#fff",
            pointBorderWidth: 1,
            pointHoverRadius: 5,
            pointHoverBackgroundColor: "rgba(75,192,192,1)",
            pointHoverBorderColor: "rgba(220,220,220,1)",
            pointHoverBorderWidth: 2,
            pointRadius: 1,
            pointHitRadius: 10,
            data: [],
            spanGaps: false,
         }
      ]
   };

   ajax("/getTemperature", "GET",{}, onFetchTempSuccess);

   function onFetchTempSuccess(response){
      hideEle("loader");
      var respData = JSON.parse(response);
      chartConfig.labels = respData.dataPoints.map(dataPoint => dataPoint.time);
      chartConfig.datasets[0].data = respData.dataPoints.map(dataPoint => dataPoint.temperature);
      renderWeatherChart(chartConfig)
  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们添加了一个名为 **renderWeatherChart** 的函数，该函数将使用嵌入在关键字 **datasets** 下的 **chartConfig** 变量中的最新数据来呈现图表。如果我们想在同一个画布上绘制多个折线图，我们可以向这个数组添加更多的元素。

数组中每个元素的**数据**键将显示图形上的不同点。我们将向 **/getTemperature** api 发出一个 ajax 请求，获取所有的数据点，并将它们放入这个键中。我们将调用 rendering 方法来显示图形。现在我们可以运行命令`node server.js`，然后使用下面的 URL 进入浏览器，查看使用数据呈现的初始图表。

```
http://localhost:9000/ 
```

Enter fullscreen mode Exit fullscreen mode

为了正确设置我们的应用程序的样式，请将以下 CSS 添加到 public 文件夹中的新 style.css 文件中。将以下代码添加到该文件中:

```
body{
    margin:0;
    padding:0;
    overflow: hidden;
    font-family: Raleway;
}

header{
    background: #2b303b;
    height: 50px;
    width:100%;
    display: flex;
    color:#fff;
}

.logo img{
  height: 45px;
}

.subheader{
    display: flex;
    align-items: center;
    margin: 0px;
}

.logout{
    flex:1;
    justify-content: flex-end;
    padding:15px;
    display: none;
}

.logout a{
    color:#fff;
    text-decoration: none;
}

#weatherChart{
    height: 80% !important;
    width: 95% !important;
    margin: 0 auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 收到新事件时更新图形的代码

现在，我们希望订阅我们的服务器将在其上发送该图的更新事件的唯一通道。在我们的项目中，频道被命名为**伦敦气温图表**，事件被命名为**新气温**。请添加以下代码来处理事件，然后实时更新图表:

```
channel = pusher.subscribe('london-temp-chart');
channel.bind('new-temperature', function(data) {
    var newTempData = data.dataPoint;
    if(weatherChartRef.data.labels.length > 15){
      weatherChartRef.data.labels.shift();  
      weatherChartRef.data.datasets[0].data.shift();
    }
    weatherChartRef.data.labels.push(newTempData.time);
    weatherChartRef.data.datasets[0].data.push(newTempData.temperature);
    weatherChartRef.update();
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了看到这段代码的运行，您必须刷新浏览器，您将看到初始图表。现在，我们必须接收一个新的数据点，为此，您需要调用以下 API，要么使用一些模拟 API 调用工具，要么在浏览器中使用以下具有不同值的 URL。

```
http://localhost:9000/addTemperature?temperature=17&time=1500 
```

Enter fullscreen mode Exit fullscreen mode

为了测试您的图表更新代码，您可以在您的 **app.js** Javascript 文件中使用以下临时代码，它将在特定的时间间隔后向上述 URL 发出虚拟 Ajax 请求。

```
/* TEMP CODE FOR TESTING */
  var dummyTime = 1500;
  setInterval(function(){
    dummyTime = dummyTime + 10;
    ajax("/addTemperature?temperature="+ getRandomInt(10,20) +"&time="+dummyTime,"GET",{},() => {});
  }, 1000);

  function getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
  }
/* TEMP CODE ENDS */ 
```

Enter fullscreen mode Exit fullscreen mode

参考完整代码的 Github repo 是[https://github.com/mappmechanic/realtime-analytics](https://github.com/mappmechanic/realtime-analytics)

## 结论

最后，我们的实时分析应用程序准备就绪。我们将看到伦敦市的天气温度图实时更新。

[![](img/2ccdf6b8583e5a3a66625dd0b4a68351.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mL3yfc6B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.pusher.com/wp-content/uploads/2017/04/build-realtime-graph-app-javascript.gif)

我们可以将这篇博客文章中的代码用于任何图表库，也可以渲染任何类型的图表，如条形图、散点图或饼图，以便实时更新。

该代码也可用于多个企业应用，如监控仪表板、分析报告、传感器监管应用、金融应用等。Pusher 库帮助我们将实时事件发送到所有连接的客户端应用程序，这些应用程序可以使用数据来实时更新图表。

这篇博文原载于 Pusher 的博客-[https://blog . Pusher . com/build-real time-graph-using-JavaScript](https://blog.pusher.com/build-realtime-graph-using-javascript)。**