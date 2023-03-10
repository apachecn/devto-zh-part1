# 高图表

> 原文：<https://dev.to/funkysi1701/highcharts-27cb>

High Charts 是一个 javascript 库，允许将图形和图表轻松添加到网页中。

[![](img/259849b1c1521621afb6facff99f119e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jU17A6eo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2017/05/chart.jpeg%3Fresize%3D768%252C512%26ssl%3D1)

像这样的图表很容易添加几行 html 和 javascript 代码。

```
<script src="https://code.highcharts.com/highcharts.js">
<script src="https://code.highcharts.com/modules/exporting.js">
<div id="container"></div> 
```

Enter fullscreen mode Exit fullscreen mode

这将添加 highcharts 库和一个用于显示图表的容器。

```
Highcharts.chart('container', {

  title: {
    text: 'Solar Employment Growth by Sector, 2010-2016'
  },
  subtitle: {
    text: 'Source: thesolarfoundation.com'
  },
  yAxis: {
    title: {
      text: 'Number of Employees'
    }
  },
  legend: {
    layout: 'vertical',
    align: 'right',
    verticalAlign: 'middle'
  },
  plotOptions: {
    series: {
      pointStart: 2010
    }
  },
  series: [{
    name: 'Installation',
    data: [43934, 52503, 57177, 69658, 97031, 119931, 137133, 154175]
  }, {
    name: 'Manufacturing',
    data: [24916, 24064, 29742, 29851, 32490, 30282, 38121, 40434]
  }, {
    name: 'Sales & Distribution',
    data: [11744, 17722, 16005, 19771, 20185, 24377, 32147, 39387]
  }, {
    name: 'Project Development',
    data: [null, null, 7988, 12169, 15112, 22452, 34400, 34227]
  }, {
    name: 'Other',
    data: [12908, 5948, 8105, 11248, 8989, 11816, 18274, 18111]
  }]

}); 
```

Enter fullscreen mode Exit fullscreen mode

这将添加数据并为图表设置各种选项。关于您可以设置的不同图表和选项的更多细节可以在[https://www.highcharts.com](https://www.highcharts.com)找到

我很喜欢你可以放在你的网站上的不同的量规，因为你可以添加一点动画，让指针四处跳动。[https://www.highcharts.com/demo/gauge-solid](https://www.highcharts.com/demo/gauge-solid)

[![](img/b2cc35885b7643c179de5b60c567b3ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WfA523HN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2017/05/chart-1.jpeg%3Fresize%3D750%252C250%26ssl%3D1)

因为我处理的大部分数据都存储在 SQL Server 数据库中，所以我花了相当多的时间编写存储过程和函数来建模数据，这样我就可以轻松地将数据从数据库传递到 javascript。

Highcharts 非常灵活，有很多不同的选项，可以显示几乎任何数据的图表，这些图表也可以导出为 PNG、JPEG、CSV 等格式