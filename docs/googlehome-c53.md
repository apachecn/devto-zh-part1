# 和谷歌主页说话并请他发推文

> 原文：<https://dev.to/amotarao/googlehome-c53>

兼做 dev.to 投稿测试，在
[Qiita](https://qiita.com/amotarao/items/5852f8564d4e2f429769) 上原封不动地刊登也写了的报道。

# 你好

是“喂谷歌”派的 amotarao。 我是设计师一职的学徒。

本文的目的是**“与 GoogleHome 搭话，让他代理内容发推特”**。

“喂，谷歌，在 Twitter 上嘀咕‘饿了’！ ”
“在 Twitter 上嘟囔着‘饿了’。 ”
的感觉。

谷歌主页以设置为前提，
这次使用**IFTTT** 。
**因为没有任何程序**，所以我想谁都能做到。
尽量注意易懂的文章。
请告诉我难以理解的表达方式。
请多关照。

# 什么是 IFTTT？

[IFTTT](https://ifttt.com/) 的说明，我觉得[Wikipedia](https://ja.wikipedia.org/wiki/IFTTT) 很容易理解，但是有点旧，所以以下引用+稍微变更。

```
「if this then that」というシンプルなコンセプトに基づく「Applet」を作成し共有することができる。
Appletの「this」の部分は「Facebookで写真をタグ付けした時」「Foursquareでチェックした時」といった「きっかけ」になり、
「that」の部分は「テキストメッセージの送信」「Facebookでステータスメッセージを作成」といった「行動」になる。
ユーザーが使用するサービスで有効にする「きっかけ」と「行動」のコンビネーションがAppletである。 
```

服务可以在 Twitter、Facebook、LINE、Instagram 等上使用各种各样。
在某个服务引起的**契机**(发牢骚、上传照片、入住等)下，
在其他服务中进行**行动**(发微博、发表文章等)

所以这次，以“跟 GoogleHome 搭话”的**为契机，请他们采取“在 Twitter 上发推特”的**行动**。**

# 注册 IFTTT 吧

[在首页](https://ifttt.com/)中，我想有 3 种按钮。
用喜欢的东西登录一下吧。

## 1.输入您的电子邮件

从自己的电子邮件地址注册账户。
输入电子邮件地址后，点击 Get Started 吧。

出现名为 Set your password 的密码输入页面，输入
密码，点击 Sign up 吧。

## 2.继续使用谷歌

利用谷歌账户的信息进行注册。
点击 Continue with Google，应该会出现 Google 的登录页面，
输入邮箱地址、密码后登录吧。

## 3.继续脸书

使用 Facebook 账户的信息进行注册。
点击 Continue with Facebook，应该会出现 Facebook 的登录页面，
输入邮箱地址、密码，登录吧。

# 认证账户吧

因为不知道要从哪个 GoogleHome 发布到哪个 Twitter 账户，所以分别给你设定。

首先，点击[Google Assistant](https://ifttt.com/google_assistant) 的 Connect，用设置了
GoogleHome 的 Google 账户登录。
“允许访问 ifttt.com 吗？ ”然后单击“允许”。

[Twitter](https://ifttt.com/twitter) 也同样点击 Connect，
用想发推特的账户进行认证。

# 寻找目标 Applet (小程序)

**Applet** 是连接契机、行动的桥梁之类的名字。

*   “发推特”后，“将同样的内容发布到 Facebook”
*   “发布到 Facebook”后，“将同样的内容发布到 Twitter”
*   “更改 Twitter 上的个人资料照片”后，将“相同的照片设定为 Facebook 上的个人资料照片”

等等，光是连接推特和 Facebook，就有各种各样的连接方法和桥梁。

它们分别是**Applet** 。

今回使うappletは[〖用你的声音发推〗です。](https://ifttt.com/applets/479447p-post-a-tweet-using-your-voice)

# 苹果设置

首先，点击 Turn on，让这个 Applet 成为可以使用的状态吧。

接下来会出现很多英语输入栏。

## 1 .第一个输入栏

我刚输入了“Post a tweet using your voice”。
我可以给你取这个 Applet 的名字，但是没有什么意义，所以这样也没有问题。

## 2.此小程序运行时接收通知+緑のスイッチ

每次这个 Applet 运行，都会向您注册的电子邮件地址发送通知。
恐怕是多余的，关掉(绿色为黑色)就没问题了。

## 3.你要说什么？

在 GoogleHome 上填写该怎么说。

“喂，谷歌，在 Twitter 上嘀咕‘饿了’！ ”来移动，所以输入
**“在 Twitter 上嘟囔$”**吧。

不需要“OK，Google”“喂 Google”。
在包含嘟囔内容的部分使用**$** 这样的符号。
不能用于第一个文字，和紧靠其他文字的时候，请加入空格。
OK 「Twitter 上没有\$和”
NG 「Twitter 上没有\$和”的空间，所以不行
NG 「\$和”首先有\$，所以不行

## 4.另一种说法是什么？(可选)

和 3 相同，但可以填写其他模式。
不是必须的项目。

**“发微博说$”**之类的。

## 5.另一种方式呢？(可选)

和 4 一样。
不是必须的项目。

**“在 Twitter 上发推特$”**之类的。

## 6.你希望助理怎么回答？

填写推文成功时，让谷歌主页怎么说话。

**“在 Twitter 上发了$”**之类的就可以了。

## 7.推文

填写推文内容。
**将替换为推特内容。
也可以是**“by Google home”**，但是保持这样也可以。**

 **## 8.救援

点击以保存到此为止的设定吧。

## 9 .再次进入设定画面

我想回到点击 Turn on 时的画面。
请点击右上角的齿轮，再次打开设定画面。

## 10.语言

不知为何出现了刚才没有的**语言**。
选择**日本**吧。

## 11.救援

再保存一次。
辛苦了。

# 开始行动！

**“喂谷歌，在 Twitter 上嘀咕‘饿了’！ ”**

您觉得怎么样...？
我想你在 Twitter 上发了推特说“饿了”。
另外，不是也发推文了吗？

**使用 IFTTT** 可以很容易地进行设定。

# 最后

我想恐怕还有其他这样的报道。
如果是类似的报道的话很抱歉。

IFTTT，真的可以连接到各种各样的服务。
可能会因为英语而很痛苦，但请一定要挑战各种各样的事情。

谷歌主页、IFTTT 哪个是主要的呢: thinking :

那么。**