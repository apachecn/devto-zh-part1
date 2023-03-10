# 基于温度的荷兰哪里适合居住 XKCD 风格

> 原文：<https://dev.to/rmhogervorst/where-to-live-in-the-netherlands-based-on-temperature-xkcd-style-4ik1>

在看过西班牙和美国基于天气的最佳居住地的图表后，我不得不附和并为荷兰做同样的事情。这个想法很简单，根据你的温度偏好决定你想住在哪里。

第一个最终结果: [![Netherlands, weatherplot XKCD style](img/48e6c93c79c9df00d7422fa287a9becf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nN_hoSFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/xkcd_NL_US_ESP.png)

这个情节怎么看？

在[这幅](https://xkcd.com/1916/) xkcd 漫画中，我们看到图形的左上方代表“如果你讨厌寒冷和热”，如果你从左上方向下到左下方，冬天会变得更冷，并以“如果你喜欢寒冷和热”结束。向右走热量(和湿度)上升以“如果你爱冷也爱热”结尾。右上方往上:“如果你恨冷爱热”。

这个帖子解释了如何制作这个图，看看我从哪里得到的数据，我采取了什么程序来查看[https://github.com/RMHogervorst/xkcd_weather_cities_nl](https://github.com/RMHogervorst/xkcd_weather_cities_nl)。

### 灵感

根据 male Salmon 受 xkcd 启发的这篇[帖子，我们可以通过查看冬季温度和湿度指数(湿度和夏季热量的组合)来确定我们的理想城市。](http://www.masalmon.eu/2017/11/16/wheretoliveus/)

我看过美国的主要城市(Maelle 的帖子)和西班牙的居住地(Claudia Guirao 的文章)。甚至还有一个关于法国的法语[，当然是](https://twitter.com/matamix/status/932283897018273792)。

所以我也要为荷兰做一个。有一个小细节，荷兰很小，美国大约是荷兰的 237 倍。从海牙到德国边境直接向东是 164 公里(101 英里)，从卢瓦尔登到南部的马斯特里赫特是 262 公里(162 英里)。我的祖国基本上属于温和的海洋性气候，夏季温暖，冬季相对温和。

我预计这两个城市之间不会有太大的差别。我使用 12 月、1 月和 2 月的平均温度作为冬季温度，并使用 [comf 软件包](https://cran.r-project.org/web/packages/comf/index.html)计算湿度。这个湿度箱是湿度和温度的结合。

*   20 至 29 岁:很少或没有不适
*   30 至 39 岁:有些不适
*   40 到 45:极大不适；避免用力
*   45 以上:危险；中暑很有可能

对于城市，我采取了非常懒惰的方式，利用荷兰气象局(KNMI，荷兰皇家气象研究所)提供的所有可用气象站。荷兰有 46 个车站。这些并不总是在城市里，而是大致描述了整个国家。

### 剧情像 XKCD

XKCD 包有很棒的绘图能力和你必须安装的很酷的字体。我是从梅勒的帖子上复制修改代码的，因为真的很好！

如果你想知道我是如何清理数据的，请访问 github 页面。

首先，我们标出荷兰的所有车站*这些地方中的大部分对于非荷兰人来说可能并不熟悉*。

```
library("xkcd")
library("ggplot2")
library("extrafont")
library("ggrepel")

xrange <- range(result$humidex_avg)
yrange <- range(result$wintertemp)

set.seed(3456)
ggplot(result,
       aes(humidex_avg, wintertemp)) +
  geom_point() +
  geom_text_repel(aes(label = NAME), family = "xkcd", 
                   max.iter = 50000, size = 3)+
  ggtitle("Where to live in The Netherlands \nbased on your temperature preferences",
          subtitle = "Data from KNMI, 2016-2017") +
  xlab("Summer heat and humidity via Humidex")+
  ylab("Winter temperature in Celsius degrees") +
  xkcdaxis(xrange = xrange,
           yrange = yrange)+
  theme_xkcd() +
  theme(text = element_text(size = 13, family = "xkcd")) 
```

Enter fullscreen mode Exit fullscreen mode

[![temperature ranges in the Netherlands alone](img/f4422fe1fcb2ef9a2b92c8e5aef05100.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IBDDxUvE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/temperatureranges-in-the-netherlands-1.png)

但从全局来看，这意味着什么呢？正如你可能注意到的，范围非常小。增加一些美国城市或者西班牙城市来做比较会很有趣。为了好玩，我还添加了荷兰加勒比群岛。

```
xrange2 <- range(c(18,40)) # modified these by hand to increase the margins
yrange2 <- range(c(-5,40))
USA <- tribble(
      ~NAME, ~humidex_avg, ~wintertemp,
      "DETROIT, MI", 27, 0,
      "NASHVILLE, TN", 33, 9,
      "FORT MEYERS, FL",37, 20
  )
SPAIN <- tribble(
      ~NAME, ~humidex_avg, ~wintertemp,
      "MADRID, SPAIN", 27, 8,
      "TENERIFE, SPAIN", 24, 13,
      "BARCELONA, SPAIN",32, 10
  )
D_CARI <- tribble(
      ~NAME, ~humidex_avg, ~wintertemp,
      "Bonaire, Caribbean Netherlands", 27, calcHumx(29,76),
      "Sint Eustatius, Caribbean Netherlands", 28, calcHumx(28,77),   
      "Saba, Caribbean Netherlands",30, calcHumx(30,76)
  )

set.seed(3456)
ggplot(result,
       aes(humidex_avg, wintertemp)) +
  geom_point(alpha = .7) +
  geom_text_repel(aes(label = NAME),
                   family = "xkcd", 
                   max.iter = 50000, size = 2)+
  geom_text_repel(data = USA, aes(humidex_avg, wintertemp, label = NAME), family = "xkcd", 
                   max.iter = 50000, size = 2, color = "blue")+
    geom_point(data = USA, aes(humidex_avg, wintertemp), color = "blue")+
    geom_text_repel(data = SPAIN, aes(humidex_avg, wintertemp, label = NAME), family = "xkcd", 
                   max.iter = 50000, size = 2, color = "red")+
    geom_point(data = SPAIN, aes(humidex_avg, wintertemp),color = "red")+
    geom_text_repel(data = D_CARI, aes(humidex_avg, wintertemp, label = NAME), family = "xkcd", 
                   max.iter = 50000, size = 2, color = "orange")+
    geom_point(data = D_CARI, aes(humidex_avg, wintertemp), color = "orange")+
    labs(title = "Where to live in The Netherlands based on \nyour temperature preferences \nCompared with some places in Spain, Caribbean NL and USA",
          subtitle = "Data from KNMI, 2016-2017, added Spain and US cities",
         x = "Summer heat and humidity via Humidex",
         y = "Winter temperature in Celsius degrees",
         caption = "includes Caribbean Netherlands"
             ) +
  xkcdaxis(xrange = xrange2,
           yrange = yrange2)+
  theme_xkcd() +
  theme(text = element_text(size = 16, family = "xkcd")) 
```

Enter fullscreen mode Exit fullscreen mode

[![Netherlands, weatherplot XKCD style](img/48e6c93c79c9df00d7422fa287a9becf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nN_hoSFm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/xkcd_NL_US_ESP.png)

最后，我们可以通过使用测量站的坐标将点放置在地图上来比较荷兰冬季的温度和夏季的湿度。

```
NL <- map_data(map = "world",region = "Netherlands")
result %>% 
    rename(LON = `LON(east)`, LAT = `LAT(north)`) %>% 
    ggplot( aes(LON, LAT))+
    geom_point(aes(color = wintertemp))+
    geom_text_repel(aes(label = NAME),
                   family = "xkcd", size = 3,
                   max.iter = 50000)+
    geom_polygon(data = NL, aes(x=long, y = lat, group = group), fill = NA, color = "black") +
    coord_map()+
    labs(title = "Wintertemperature in NL",
         subtitle = "data from KNMI (2016,2017",
         x = "", y = "")+
    theme_xkcd()+
    theme(text = element_text(size = 16, family = "xkcd"))

result %>% 
    rename(LON = `LON(east)`, LAT = `LAT(north)`) %>% 
    ggplot( aes(LON, LAT))+
    geom_point(aes(color = humidex_avg))+
    geom_text_repel(aes(label = NAME),
                   family = "xkcd", size = 3,
                   max.iter = 50000)+
    geom_polygon(data = NL, aes(x=long, y = lat, group = group), fill = NA, color = "black") +
    coord_map()+
    labs(title = "Humidex in NL",
         subtitle = "data from KNMI (2016,2017",
         x = "", y = "")+
    theme_xkcd()+
    theme(text = element_text(size = 12, family = "xkcd"))+
    scale_color_continuous(low = "white", high = "red") 
```

Enter fullscreen mode Exit fullscreen mode

[![Netherlands, humidex, gps](img/23fdfe860d092b698a1e6517b45d4dc0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--6yx1Eaoe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/humidex_NL.png) [ ![Netherlands, wintertemp, gps](img/9c114171050f22532e4c90d6c0af1176.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--udkSAodP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/img/wintertemp_NL.png)

现在让我们看看你的国家是什么样子的！