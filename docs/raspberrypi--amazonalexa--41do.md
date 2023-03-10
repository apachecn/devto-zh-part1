# 使用 RaspberryPi + AmazonAlexa 讲述房间湿度

> 原文：<https://dev.to/yurfuwa/raspberrypi--amazonalexa--41do>

在意房间干燥的季节。
使用加湿器加湿房间的人也很多吧。
虽然自己有想要的加湿器(俗称[壶](https://www.balmuda.com/jp/rain/))，但加湿到什么程度却很可疑。
首先想定量地视觉化房间干燥到什么程度，以及加湿后会有什么样的变化。

制作。

传感器是[这个是](http://amzn.to/2lS6X7o)。

[![](img/ef2c924f46d2357b198023eaafbe08ea.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6s8pjhCU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0bvb81pzqkgh68wavnpy.jpg)

配线参考[这个](http://osoyoo.com/ja/2017/07/06/dht11/)或者[这个](http://www.kabegiwablog.com/entry/2017/09/21/214548)左右。

[![](img/452ab3748272977d773f011060d3ff21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--z-QVg4wU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-ak.f.st-hatena.cimg/fotolife/k/kabegiwakun/20170921/20170921212028.png)

自己的 RaspberryPi 的模型是 2011 12 的模型 b。 GPIO 序列是这样的。

> ![unknown tweet media content](img/e73c23671f6a515b8b66e14913655a22.png)![ふわこ profile image](img/3599449eec8074e8172393c8de53802e.png)[@yurfuwa](https://dev.to/yurfuwa)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)自己家 rasp， 因为总是容易忘记模型和 gpio 排列，所以贴上吧17:15 PM - 02 Jan 2018[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948241332539027456) t24 .![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948241332539027456)

执行代码挪用这里的[样本](http://osoyoo.com/driver/pi3_start_learning_kit_lesson_17/dht11.py)家伙。

胡姆。 会枯萎。

```
$ python dhht11.py
humidity: 6 %,  Temperature: 24 C 
```

Enter fullscreen mode Exit fullscreen mode

湿度 5%似乎与[中东巴林](//monsieurshibata.cocolog-nifty.com/blog/2010/03/5-f4e3.html)相同。 因为是阿拉伯人，所以没办法。

接下来连接到 Alexa。 体系结构。

[![](img/d7252d953a92bb51971298156843fee5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TJUmbfZZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iyjejwiwbp828z4c0fms.jpg)

从 rasp 的执行代码向 dynamodb 中存储数据，经由 AWS Lambda 用 Alexa Skill kit 说话。

在执行代码中添加以下内容，从 boto3 更新到 dynamodb 的表中。 因为是每分钟的 update_item，所以完全不需要在意容量。 最适合 DIY 数据存储的是 Dynamodb 吧。

```
def update_data(temp,humidity):
    db = session.resource('dynamodb')
    table = db.Table('room-status')
    room = table.update_item( Key = {'room' : 1 }, AttributeUpdates={ 'temp': { 'Value': temp, 'Action': 'PUT' }, 'humidity': { 'Value': humidity, 'Action': 'PUT' } }) 
```

Enter fullscreen mode Exit fullscreen mode

用于 Alexa Skill Kit 的 AWS Lambda 的执行代码使用[Echokit](https://github.com/arcward/echokit) 。 广告公司和广告公司很好地联系在一起，相当容易使用。

```
import os
from echokit import EchoKit
import boto3
from boto3.session import Session
from boto3.dynamodb.conditions import Key, Attr

application_id = os.environ['APP_ID'] if 'APP_ID' in os.environ else 'amzn1.ask.skill.000'
app = EchoKit(application_id, verify_app_id=False)
handler = app.handler

# LaunchRequest @app.launch
def on_launch(request, session):
    data = get_data()
    return app.response("部屋の温度は、{}度、湿度は{}%です".format(data['temp'],data['humidity']))

def get_data():
    db = session.resource('dynamodb')
    table = db.Table('room-status')
    room = table.get_item( Key = {'room' : 1 } )['Item']
    return room 
```

Enter fullscreen mode Exit fullscreen mode

只是读着说。 Alexa 需要用 JSON 恰当地回复想说的内容，但 Echokit 最好能很好地隐藏起来。 用 flask 的想法安装。

Alexa Skillkit 的注册周围省略。 也没有色彩比对模型。 只要注册 AWSLambda 的 ARN 就可以了。 这种类型本来应该使用智能家居 API，但智能家居 API 需要 Echo 的高级机型，或者另外安装蓝牙集线器的终端进行 or 购买。
向互联网放出我家的湿度信息更简单。
但是，这个安装恐怕不会通过审查。 (注册 Alexa Skill store 需要审查)

所以在这里使用对技能的实机终端的封闭测试的功能。 使用这个的话，可以对特定的亚马逊账号称为封闭测试，传播技能。

泰伦

[https://www.youtube.com/embed/5khHjIZobXY](https://www.youtube.com/embed/5khHjIZobXY)

“将我家的温度打开*，打开*”

之所以这样，是因为没有使用智能家居 API。 使用条件的功能的话，“我们家的温度是多少？ ”等，我觉得很帅。

## 火锅加湿解决方案

那么，因为可以让 Alexa 说湿度，所以随时都可以知道房间的湿度，但湿度完全没有改善。

也有意见认为买加湿器，但众所周知，加热水会产生蒸汽。

> ![unknown tweet media content](img/d3c42dc459b22458a33ce4991237f587.png)![ふわこ profile image](img/3599449eec8074e8172393c8de53802e.png)[@ yur fuwa](https://dev.to/yurfuwa)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)鍋加湿ソリューション2018 年 1 月 03 日下午 14:22[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948560124288802816)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948560124288802816)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948560124288802816)

日本的房子建造得很好，为了不让气味充满客厅，热气会滞留在厨房上空。 所以用循环器硬把空气送到客厅。

> ![unknown tweet media content](img/24660350d9908756af3478655b0a86c9.png)![ふわこ profile image](img/3599449eec8074e8172393c8de53802e.png)[@yurfuwa](https://dev.to/yurfuwa)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)由解决方案得到的结果14:38 PM - 03 J an 2018[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948564232274001922)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948564232274001922)

虽然用锅加湿解决方案在短时间内达到了 55%，但是补给水和处理火很麻烦。
睡觉的时候需要停下来是个课题。

如果停止的话

> ![unknown tweet media content](img/9d19be26f766d30387b730ae6b9700ef.png)![ふわこ profile image](img/3599449eec8074e8172393c8de53802e.png)ふわこ[@ yur fuwa](https://dev.to/yurfuwa)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)05:51am-04 Jan 2018[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948794018535632896)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948794018535632896)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948794018535632896)

但是在 1.5 小时内湿度减半，朝着 5%前进。
锅加湿解决方案没有可持续性。

我还是想要[壶](https://www.balmuda.com/jp/rain/)，请谁买吧