# 用 Arduino Uno 发推文

> 原文：<https://dev.to/danielammorais/tweeting-with-arduino-uno>

很久以前，我买了一个以太网 ENC28J60，但我最近才测试它，尽管我喜欢 Java，并且在我以前的所有项目中都使用它来开发 Arduino 和服务器之间的接口，但我很清楚在这种情况下 Python 会是一个更好的选择。

2014 年，我决定使用 [Panama Hitek Arduino SDK](https://panamahitek.com/) ，然后我给他们发了一封电子邮件，分享了我对他们新 SDK 版本的体验。2016 年，我在 IRC 的 Fedora 拉丁美洲会议上随机遇到了来自巴拿马 Hitek 的家伙。分享你作为开发人员的经验并加入社区是非常重要的，也许你会在这些地方遇到你最喜欢的框架的开发人员，甚至和他们一起工作。对我来说，这是通过文章、谈话、会议等分享知识和想法的重要性的又一个例子。所以，不要害羞或害怕分享你所知道的。

## 物联网关乎互联

物联网、嵌入式系统和智能设备是不同的概念，在使用这些术语时经常会出现混淆。
如果您的设备与互联网断开连接，它可能不会产生物联网设备的价值。联网不仅仅是从应用程序远程读取命令，它还可以以不同的方式与您的客户端和 Twitter 等其他系统进行交互，还可以收集有用的信息，这些信息可以用来让它变得更加智能。

## 第一步

在 Twitter 中注册您的应用程序以使用他们的 API。访问 [Twitter Dev](https://apps.twitter.com/app/new) 来创建您的 Twitter 应用程序，并生成一个访问令牌，以便能够从您的帐户发送推文(“密钥和访问令牌”>“创建我的访问令牌”)

## 方案

### 对查克·诺里斯道

您可以开发自己的 C 程序来连接 Arduino，并向 Twitter API 发送 HTTPS 请求。有一个适用于 Arduino 的 [Tweet 库，但它已被弃用，需要重构才能与高于 1.6.4 的 Arduino 版本一起使用。](https://arduino-tweet.appspot.com/)

[![SpongeBob SquarePants](img/8bfd5f8d4bd243e378092840fa79ebd5.png)T2】](https://i.giphy.com/media/3oriNUhx4FLc707jq0/giphy.gif)

### 简单易行的方法

如果你没有 C 语言和微控制器、寄存器、数字系统等方面的经验，那么在低级水平上使用指针会很复杂。使用 python 脚本，编码和维护软件会容易得多。Python 将负责展示一个 web 服务器，然后由 Arduino 使用，并将内容(例如，传感器值、开灯或关灯的命令等)发送到 Twitter。注意，Arduino UNO 在构建 HTTP 请求时有局限性(需要使用 GET 方法而不是 POST 来创建 tweet)，因此 Python **没有公开 API** 。

## 要求

*   UIPEthernet
*   Python 2.7
*   点
*   特威森
*   瓶

EtherShield 和 ETHER_28J60 是大多数教程中使用的常见库，但 UIPEthernet 与原始以太网兼容，这允许您使用 Arduino IDE 中可用的示例(文件>示例>以太网)，只需替换:

```
#include <SPI.h>
#include <Ethernet.h> //Replace here with <UIPEthernet.h> 
```

Enter fullscreen mode Exit fullscreen mode

下载 UIPEthernet 并安装 Flask 和 Twython:

```
$ cd ~/Arduino/libraries/
$ git clone https://github.com/ntruchsess/arduino_uip UIPEthernet
$ sudo pip install flask
$ sudo pip install twython 
```

Enter fullscreen mode Exit fullscreen mode

创建一个配置文件，插入 Twitter 为您生成的信息:

```
# api_config 
APP_KEY = 'your_api_key'  
APP_SECRET = 'your_api_secret'  
OAUTH_TOKEN = 'your_access_token'  
OAUTH_TOKEN_SECRET = 'your_token_secret' 
```

Enter fullscreen mode Exit fullscreen mode

在同一个文件夹中，创建 tweet.py:

```
from twython import Twython  
from flask import Flask  
import api_config

app = Flask(__name__)

APP_KEY = api_config.APP_KEY  
APP_SECRET = api_config.APP_SECRET  
OAUTH_TOKEN = api_config.OAUTH_TOKEN  
OAUTH_TOKEN_SECRET = api_config.OAUTH_TOKEN_SECRET 

twitter = Twython(APP_KEY, APP_SECRET, OAUTH_TOKEN, OAUTH_TOKEN_SECRET)

@app.route("/")
def index():  
    return "Hello world, I'm running!"

@app.route("/tweet/<message>")
def sendTweet(message):  
    twitter.update_status(status=message); 
    return "Done." 

if __name__ == '__main__':  
    #Fix here
    app.run(host="your_ip", port=8080, debug=True) 
```

Enter fullscreen mode Exit fullscreen mode

上传到 Arduino:

```
#include <SPI.h>
#include <UIPEthernet.h>

byte mac[] = {0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xED};  
char server[] = "ip_server"; //fix here  
IPAddress ip(192, 168, 1, 177); //fix here
EthernetClient client;

void setup() {  
  Serial.begin(9600);
  if (Ethernet.begin(mac) == 0) {
    Serial.println("Failed to configure Ethernet using DHCP");
    Ethernet.begin(mac, ip);
  }
}

void loop() {  
  if (client.connect(server, 8080)) {
    Serial.println("connected");
    // Make a HTTP request:
    client.println("GET /sendTweet/Hello%20world%20by%20Arduino HTTP/1.1");
    client.println("Host: ip_server_with_port"); //fix here
    client.println("Connection: close");
    client.println();
    client.stop();  
  } else {
    Serial.println("connection failed");
  }

  delay(5000);
} 
```

Enter fullscreen mode Exit fullscreen mode

授予脚本执行权限:

```
$ chmod a+x tweet.py
$ python tweet.py 
```

Enter fullscreen mode Exit fullscreen mode

还有魔法.....

> ![Daniela Morais profile image](img/2c743a927972ed013b1f30313e84457d.png)丹妮拉莫莱斯[@丹妮拉莫赖斯](https://dev.to/danielammorais)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)你好世界 by Arduino2016 年 7 月 17 日上午 06:45![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=754567798819479553)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=754567798819479553)

## 观察

出于某种原因，我不能向本地服务器发出请求，因此有必要在数字海洋上托管 Python 脚本。服务器不支持 HTTPS，有时会收到一个错误。
注意，这个 Python 脚本**没有公开 API** ，你必须始终使用 URL 编码器/解码器在 path param 中发送消息。特殊字符可能是一个问题，如果有错误，请尝试使用 curl 或 wget。

## 酷的东西

[Tweetduino](https://github.com/danielamorais/tweetduino.git)
[物联网正在吞噬世界](https://medium.com/@AlexandraBowen/iot-is-eating-the-world-ed97f2b5a2ca)
[物联网设备 API(巴西葡萄牙语)](http://sensedia.com/blog/apis/apis-para-dispositivos-iot/)

#### 参考文献

arduino Ethernet–将数据推送到(PHP)服务器[http://www . tweaking 4 all . com/hardware/arduino/arduino-Ethernet-data-push/](http://www.tweaking4all.com/hardware/arduino/arduino-ethernet-data-push/)

如何使用 Python[http://www.techplugg.com/send-tweet-command-line-python/](http://www.techplugg.com/send-tweet-command-line-python/)从命令行发送推文

*最初发布于[我的个人博客](http://danielammorais.com/2016/07/17/como-postar-tweets-com-arduino/)。*