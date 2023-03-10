# 我如何用 Telegram 和 Python 创建一个聊天机器人

> 原文：<https://dev.to/rodolfoferro/disatbot---how-i-built-a-chatbot-with-telegram-and-python-ajn>

*原嘉宾由[鲁道夫·费罗](https://pybit.es/author/rodolfo-ferro.html)于 2017 年 12 月 10 日发布于[工具](https://pybit.es/category/tools.html) @ [PyBites](https://pybit.es/guest-telegram-python-chatbot.html)*

Rodolfo 最近加入了我们的[代码挑战](https://pybit.es/pages/challenges.html)并创建了灾难关注机器人(DisAtBot)，这是一个帮助受自然灾害影响的人的聊天机器人。在这篇文章中，他展示了如何用 Telegram 和(当然)Python 构建这个机器人。给他一些爱，因为谁知道，这可能是一个生命的救星(双关语)！我们很高兴让他展示他为[代码挑战赛 43](https://pybit.es/codechallenge43.html) 提交的这个有趣的项目，这个项目为他赢得了一本关于聊天机器人的书。/Rod 请分享...

> "∞召集了这么多男孩，从哪里来这么多志愿者，医院里怎么流了这么多血，是谁组织的旅在整个受灾地区指挥车辆和行人的通行？没有传唤，也没有打电话，大家都去了。”
> 
> **【改为墨西哥的黑色星期四】**
> –埃米利奥·维亚雷，1985 年。

## 一点脉络...

自 2017 年 9 月 19 日[以来，墨西哥遭受了数次地震](https://en.wikipedia.org/wiki/2017_Central_Mexico_earthquake) ( [《卫报》](https://www.theguardian.com/world/live/2017/sep/20/mexico-city-earthquake-dozens-dead-powerful-quake-live-updates)， [CNN](http://edition.cnn.com/2017/09/19/americas/mexico-earthquake/index.html) )。这让我想知道我们如何更好地处理受损区域、被埋在建筑物废墟下的人、需要医疗护理的受伤人员和其他情况的报道。

[Verificado 19s](http://www.verificado19s.org/) 是跟进社交媒体报道并在在线地图上显示信息的即时解决方案。这需要对来自受影响地区的人们在社交媒体上的帖子进行大量实时(24/7)监控。这些数据每 10 分钟更新一次。

因此，我开始思考一种方法来优化这一过程，以适应未来的情况，不仅是地震，而是一般的灾难情况。这激励我为 Pybites [代码挑战 43 开发这个机器人——用 Python](https://pybit.es/codechallenge43.html) 构建一个聊天机器人。

## 于是灾难机器人诞生了

DisAtBot 通过消息平台(如 Telegram、Facebook Messenger、Twitter 等)自动报告事件。目前它只支持 Telegram，但我希望将其扩展到其他社交媒体。如果你想投稿，请参阅最后的投稿部分。

您可以在以下网址找到 DisAtBot:

*   电报:[https://t.me/DisAtBot](https://t.me/DisAtBot)
*   官方回购:[https://github.com/RodolfoFerro/DisAtBot](https://github.com/RodolfoFerro/DisAtBot)

我们的想法是建立一个简单的流程，让灾难报告变得快速而简单。DisAtBot 的一般流程如下:
[![DisAtBot flow](img/291ed9b41acc89bf8f0a7af3a18bc29a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fa5N6iKn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/RodolfoFerro/DisAtBot/master/imgs/flow.png)

这个想法是，任何用户都可以通过在对话中从按钮菜单中选择选项来与机器人进行交互。这大大加快了事故报告的速度。

下一步是为相应的政府机构/公共组织/非政府组织等开一张存储在数据库中的罚单。验证并发送援助。当不再需要帮助，或者情况得到控制时，票证关闭。

## 设置

第一次克隆[回购](https://github.com/RodolfoFerro/DisAtBot)。我使用了 Python 3.6 和以下软件包:

*   熊猫
*   [地质公园](http://geopandas.org/)
*   [地理编码器](http://geocoder.readthedocs.io/)
*   [谷歌地图](https://developers.google.com/maps/documentation/)
*   [geojsonio](http://geojson.io/)
*   [身材匀称](https://shapely.readthedocs.io/en/latest/)
*   [python-telegram-bot](https://python-telegram-bot.org/)

要安装所有的依赖项，创建一个虚拟环境并运行:

```
pip install -r requirements.txt 
```

然后将 cd 放入 scripts 文件夹并运行 bot，如下所示:

```
python DisAtBot.py 
```

## 设计

最初版本的重点是创建菜单按钮，以便与用户轻松交互。第二个也是主要的问题是对话处理程序。需要一个[有限状态机](https://en.wikipedia.org/wiki/Finite-state_machine)来保存每个状态的期望流和响应。

我不会深入解释，但是下面的代码将展示我是如何解决这个问题的。

首先，Telegram 的库有几种方法可以在对话流程中为用户响应创建按钮菜单。这个想法是创建一个键盘标记来通过按钮处理响应。这可以是内嵌的(按钮将出现在对话窗口中)或作为回复键盘(按钮将显示在文本框下以编写消息)。

在菜单功能中可以看到一个例子:

```
def menu(bot, update):
    """
    Main menu function.
    This will display the options from the main menu.
    """
    # Create buttons to select language:
    keyboard = [[send_report[LANG], view_map[LANG]],
                [view_faq[LANG], view_about[LANG]]]

    reply_markup = ReplyKeyboardMarkup(keyboard,
                                       one_time_keyboard=True,
                                       resize_keyboard=True)

    user = update.message.from_user
    logger.info("Menu command requested by {}.".format(user.first_name))
    update.message.reply_text(main_menu[LANG], reply_markup=reply_markup)

    return SET_STAT 
```

如您所见，`keyboard`变量是一个包含四个要显示的按钮的列表。布局可以通过嵌套列表来设置。在这种情况下，**报告**和**地图**按钮在第一行，而**常见问题**和**关于**按钮在第二行。这看起来像:

[![Menu buttons screenshot](img/0f1198bea67b4a69058dd71b075e60a3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rBluG3K6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/RodolfoFerro/DisAtBot/master/imgs/screenshot_menu.jpg)

继续代码，需要一个`ReplyMarkup`来处理按钮响应。它指定了菜单的布局:是否只显示一个菜单，是否需要调整大小，等等。

一个记录器用于机器人，而`update.message.reply(...)`函数用于根据用户的响应更新显示的文本。该函数返回的`SET_STAT`变量是一个(预定义的)整数，用于返回当时的状态，并遵循流程。

我们现在了解了菜单的创建和处理。使用按钮的原因是我们想要快速的交互，因为机器人是在紧急情况下使用的。

对话处理器——电报的`ConversationHandler`——负责设置我们当前所处的状态或流程的步骤，我前面提到过的有限状态机。请注意，每个状态还需要处理其各自的信息(按钮响应等。)

这段代码显示了对话处理程序:

```
def main():
    """
    Main function.
    This function handles the conversation flow by setting
    states on each step of the flow. Each state has its own
    handler for the interaction with the user.
    """
    global LANG
    # Create the EventHandler and pass it your bot's token.
    updater = Updater(telegram_token)

    # Get the dispatcher to register handlers:
    dp = updater.dispatcher

    # Add conversation handler with predefined states:
    conv_handler = ConversationHandler(
        entry_points=[CommandHandler('start', start)],

        states={
            SET_LANG: [RegexHandler('^(ES|EN)$', set_lang)],

            MENU: [CommandHandler('menu', menu)],

            SET_STAT: [RegexHandler(
                        '^({}|{}|{}|{})$'.format(
                            send_report['ES'], view_map['ES'],
                            view_faq['ES'], view_about['ES']),
                        set_state),
                       RegexHandler(
                        '^({}|{}|{}|{})$'.format(
                            send_report['EN'], view_map['EN'],
                            view_faq['EN'], view_about['EN']),
                        set_state)],

            LOCATION: [MessageHandler(Filters.location, location),
                       CommandHandler('menu', menu)]
        },

        fallbacks=[CommandHandler('cancel', cancel),
                   CommandHandler('help', help)]
    )

    dp.add_handler(conv_handler)

    # Log all errors:
    dp.add_error_handler(error)

    # Start DisAtBot:
    updater.start_polling()

    # Run the bot until the user presses Ctrl-C or the process
    # receives SIGINT, SIGTERM or SIGABRT:
    updater.idle() 
```

乍一看，这似乎有点令人困惑，但归根结底:

*   对话处理程序拥有流的状态。
*   它也有入口点(比如`start`函数)和回退点(比如`cancel`和`help`函数)。
*   它还包含一些错误处理程序。
*   使用了一个全局`LANG`变量，因为实现
*   忘了说了——支持英文或西班牙文互动！为了支持这一点，我用两种语言为每次交互创建了字典。

如果你想检查这个机器人的完整代码，检查脚本目录，在那里你会找到主脚本和语言词典。

其他一些实现的特性是地理定位处理和`About` / `FAQ`部分。但是了解这个项目的最佳方式是观看它的实际操作(要观看现场演示，请访问 8:30):

[![DisAtBot video preview](img/551cf76333c7965bd2dd4bfbba421d0e.png)T2】](https://drive.google.com/file/d/1dOvF17AYKiic85HmzMjnK5Qza2Tg0PNw/preview)

## 未来的工作

对于未来的发展，我正在考虑添加一个地图。系统已经根据获取的位置创建了 GeoJSON 文件。

如前所述，我正在考虑将此扩展到其他平台，如 Facebook Messenger 和 Twitter。另一个可以添加的好东西是一个解释机器人主要使用案例的网站，也许是一个维基网站？

如果您有任何其他想法或建议，请随时[联系我](https://twitter.com/FerroRodolfo)或:

## 投稿

如果你对这个项目感兴趣，可以随意看看回购的[贡献](https://github.com/RodolfoFerro/DisAtBot/blob/master/CONTRIBUTING.md)文件。如果这个项目能够发展到在现实生活中使用，以减轻自然灾害的严重后果，我会非常高兴，因为自然灾害似乎总是在最意想不到的时候发生。

* * *

保持冷静，用 Python 编码！
–[杆](https://pybit.es/pages/guests.html#rodolfoferro)