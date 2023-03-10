# 在 Python 中用 NLU 创建一个机器人

> 原文：<https://dev.to/plutov/create-a-bot-with-nlu-in-python-91e>

[![bot.png](img/8c310526cb2b114d636c51cc80596fc0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nj-HRh8z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/plutov/bot/master/bot.png)

在 Wizeline 我们有 Python 课程，最近的话题是如何用 Python 构建一个机器人。我一直想尝试将自然语言理解(NLU)作为人工智能中自然语言处理的子主题，处理机器阅读理解。

当我们使用 Python 时，我检查了哪些库我们已经必须这样做，我决定尝试一下[拉沙 NLU](https://rasa.ai/) ，一个理解短文本中所说内容的工具。比如，拿一条短信来说:

```
"I'm looking for a Mexican restaurant in the center of town" 
```

Enter fullscreen mode Exit fullscreen mode

返回结构化数据，如:

```
intent: search_restaurant
entities: 
  - cuisine : Mexican
  - location : center 
```

Enter fullscreen mode Exit fullscreen mode

### 机器人的想法

简单的想法是建立一个机器人，它将回答(理想的)任何问题。请看上面的截图，它现在是如何工作的。

### 训练

首先，我们需要训练 RASA 理解我们的信息。我们需要定义意图和实体。我们在开始时没有什么意图:

*   迎接
*   whatis
*   基本知识的

您可以使用 [rasa-nlu-trainer](https://rasahq.github.io/rasa-nlu-trainer/) 来定义一些示例，我们稍后将使用这些示例来训练机器人。然后你可以将配置保存为 [JSON 文件](https://github.com/plutov/bot/blob/master/rasa-data.json)。

### Python 依赖关系

这个项目需要安装一些依赖项来构建这个想法:rasa、slackclient、sklearn 和 wolfram/wikipedia 来寻找答案。

```
pip3 install slackclient rasa_nlu scipy scikit-learn sklearn-crfsuite numpy spacy wolframalpha wikipedia
python3 -m spacy download en 
```

Enter fullscreen mode Exit fullscreen mode

我用的是 Python 3，你可以试着用这个 [Dockerfile](https://github.com/plutov/bot/blob/master/Dockerfile) 检查一下。

### 配置

我使用 Slack 与机器人通信，但我很确定它可以被任何其他消息服务取代。要使用 Slack，我们需要创建一个 bot 并获得 Slack API 令牌。

你知道什么是 [Wolfram](https://www.wolframalpha.com/) 吗？事实上，非常好的工具来获得简洁的答案。因此，我们可以[在那里创建一个每月限额为 2000req 的免费应用程序](https://developer.wolframalpha.com/portal/myapps/)，并获得应用程序 ID。

如果对 Wolfram 的请求失败，或者它找不到任何答案，我们将使用免费的 Wikipedia API 来获得答案。

我们的应用程序使用环境变量:

```
docker build -t bot . && docker run -e SLACK_TOKEN=<token> -e WOLFRAM_APP_ID=<app_id> bot
# or
SLACK_TOKEN=<token> WOLFRAM_APP_ID=<app_id> python3 bot.py 
```

Enter fullscreen mode Exit fullscreen mode

### 训练时间

RASA 应在使用预测之前接受培训，这是在应用程序启动时在 [nlp/rasa.py](https://github.com/plutov/bot/blob/master/nlp/rasa.py) :
中完成的

```
self.rasa_config = RasaNLUConfig(config_file)

training_data = load_data(self.data_file)
trainer = Trainer(self.rasa_config)
trainer.train(training_data) 
```

Enter fullscreen mode Exit fullscreen mode

### 预测时间

你可以从你的 python 脚本中直接调用拉莎·NLU。为此，您需要实例化一个解释器。

```
self.interpreter = Interpreter.load(trainer.persist(self.model_dir), self.rasa_config) 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以使用这个解释器从 Slack 获取消息并解析它:

```
self.interpreter.parse(msg) 
```

Enter fullscreen mode Exit fullscreen mode

例如:

```
What is the birth date of John Lennon? 
```

Enter fullscreen mode Exit fullscreen mode

rasa parseas:

```
intent: whatis
entities: 
  - query : birth date of John Lennon 
```

Enter fullscreen mode Exit fullscreen mode

### 数据提供者

然后我们发送`birth date of John Lennon`到 Wolfram API，很简单，对吗？而 Wolfram 会返回非常简短的回答:

```
Wednesday, October 9, 1940 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要将它发送回用户。

### 未来的步骤

当然，预测目前是非常基础的，不处理上下文等。但是我们可以在飞行中训练拉莎。接下来我想做的是保存所有未分析的问题，或者无法找到答案的问题 Bot，并训练 Bot 回答这些问题。

因此，如果你对此感兴趣，我愿意为这个 [GitHub repo](https://github.com/plutov/bot) 做出贡献。

[我博客里的原帖](http://pliutau.com/create-bot-with-nlu-in-python/)。