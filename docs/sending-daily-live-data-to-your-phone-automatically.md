# 将每日实时数据自动发送到您的手机

> 原文：<https://dev.to/larswaechter/sending-daily-live-data-to-your-phone-automatically>

### 简介

今天我想给大家展示我最近参与的一个小型 RaspberryPi / Python 项目的摘录。

这背后的主要想法是将每日推送通知与实时数据发送到我的手机。例如:天气或交通信息。

因此，我认为一个简单而有效的方法是编写一个在 Raspberry Pi 上执行的小 python 程序。

为了重复做这件事，我在我的 Pi 上创建了一个 cronjob，它在每天的某个时间触发 python 程序员。

现在我向你展示 python 程序的基本结构。在这种情况下，我们将实时天气信息发送到我们的手机。开始吧！

### 导入所需模块:

```
from pushbullet import Pushbullet
import requests 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 python Pushbullet 模块向我们的手机发送 notitifactions，使用 requests 模块发出 HTTP 请求。

### 创建天气类:

下一步是为天气数据创建一个类。这里我们处理以下天气信息:

*   位置
*   最低温度
*   最高温度
*   摘要

对于其中的每一个，我们都在天气类中创建了一个属性。此外，我们创建一个 init 方法来设置属性，并创建一个方法来返回字符串中的所有信息。

当然，您可以在以后添加更多的数据。

```
 class Weather:
    location = ""
    tempMin  = ""
    tempMax = ""
    summary = ""

    def __init__(self,location,tempMin,tempMax,summary):
        self.location = location
        self.tempMin = tempMin
        self.tempMax = tempMax
        self.summary = summary

    def getWeatherString(self):
        return "Today's weather in " + self.location + ": \nTemperature: " + self.tempMin + " - " + self.tempMax + u'\u2103' + "\nSummary: " + self.summary 
```

Enter fullscreen mode Exit fullscreen mode

### 接收天气数据:

为了接收天气数据，我们称之为雅虎天气 API。使用是免费的，我们不需要 API 密钥。

详细的 API 信息请看一下[文档](https://developer.yahoo.com/weather/documentation.html)。

接下来，我们创建一个发送 HTTP 请求并处理数据的函数。

```
def getWeather(woeid):
    apiUrl = "https://query.yahooapis.com/v1/public/yql?format=json&"
    apiQuery = {"q": "select location,item from weather.forecast where u='c' AND woeid=" + woeid}

    res = requests.get(apiUrl, params=apiQuery).json()

    location = res['query']['results']['channel']['location']['city']
    weatherToday = res['query']['results']['channel']['item']['forecast'][0]

    tempMin = weatherToday['low']
    tempMax = weatherToday['high']
    summary = weatherToday['text']

    weather = Weather(location,tempMin,tempMax,summary)

    return weather 
```

Enter fullscreen mode Exit fullscreen mode

这个函数的过程应该是显而易见的。首先，我们设置 API Url 和查询字符串来选择我们想要的数据。我们的“woeid”参数用于将天气预报的位置表示为 WOEID。

API 返回许多信息。在我们的例子中，我们只是过滤了本质。

“weatherToday”变量包括来自 json 数据的今天的天气。如果你想得到明天的天气很容易改变数组索引为 1。

查看 docu(上面的链接)找到你的目标城市的 WOEID 和更多。

### 发送数据到您的手机:

现在我们知道如何获取数据。下一步是发送到我们的手机上。正如我上面已经说过的，我们将使用 Pushbullet 来完成这个任务。

让我们创建一个新函数，将我们的数据发送到目标设备。

```
def pushMessage(device,msg):
    pushTitle = "Weather Update"
    push = device.push_note(pushTitle, msg)
    print("Message was sent!") 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数有两个参数。“设备”是我们的目标设备，“消息”是我们的数据/消息。“push_note”是 pushbullet 模块中的一个方法。它将数据发送到目标设备。

### 最后一步:

我们差不多完成了。没什么可做的了。:)

剩下要做的事情:

*   设置我们的标识天气目标城市的 WOEID
*   设置我们的 Pushbullet API 键
*   设置我们的推板目标设备
*   调用我们在上面创建的函数

```
# Your Pushbullet API Key pb = Pushbullet("your_api_key")

# Target Device Name device = pb.get_device('your_device_name')

# Yahoo WOEID for location identification woeid = "your_woeid"

msg = getWeather(woeid).getWeatherString()
pushMessage(device,msg) 
```

Enter fullscreen mode Exit fullscreen mode

只要问一下 Google 如何获得自己的 Pushbullet API 密匙就可以了。如您所见，我们使用相应的 WOEID 调用“getWeather”函数，并使用“getWeatherString”获取输出字符串。最后，我们用目标设备和消息调用“pushMessage”函数。

要找出您的设备名称，您可以这样做:

```
print(pb.devices) 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们完了！每次执行程序时，我们都会收到一个推送通知，通知我们手机上有实时天气数据。当然，你可以扩展你程序来获得更多的数据。例如:

*   业务资料
*   小鸟叫声
*   新闻

还有更多。

如果您使用的是 Raspberry Pi，那么您可能希望添加一个 cronjob 来执行程序，例如从每周一到周五的早上 7 点。

我还从这里创建了一个包含源代码的 GitHub Repo。
来看看: [MorningPi](https://github.com/Lars124/MorningPi)