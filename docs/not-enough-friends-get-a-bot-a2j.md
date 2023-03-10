# 不够朋友？找个机器人！

> 原文：<https://dev.to/hardkoded/not-enough-friends-get-a-bot-a2j>

# 简介

当你开始阅读机器学习和[递归神经网络](https://en.wikipedia.org/wiki/Recurrent_neural_network)时，你很有可能会立即想写一个机器人。所以，我就是这么做的！

我编写了一个机器学习模型。但是，即使在训练了几天之后，我也无法获得像我过去在 Twitter 上看到的机器人那样酷的结果。因此，在深入了解社区是如何实现这些机器人的之后，我发现大多数人都在使用 [Markovify](https://github.com/jsvine/markovify) 。

在几个小时的阅读和编码之后，我能够得到我自己的机器人，名为 [TelegramBotFriend](https://github.com/kblok/TelegramBotFriend) ，准备在 Docker 容器中运行。

# 菜谱

那么我们需要什么来创建我们自己的机器人呢？

*   一个数据源。
*   文本提供商。
*   内容生成器。
*   一个平台无关的机器人。
*   平台连接器。
*   管弦乐队。

# 数据源

数据源是最重要的组成部分。如果你没有丰富的文本，你的机器人听起来会很笨。我说的丰富是指超过 10.000 行的文本，越多越好。

你能从哪里得到那个文本？

*   以我为例，我去了一个朋友的 WhatsApp 群，导出了群历史。
*   你的推特账户，甚至你朋友的推特账户。
*   你的博客。
*   懈怠。

你需要收集尽可能多的文本。

您可能需要对该文本进行后处理，因为众所周知，“垃圾进，垃圾出”。你可能希望删除由源生成的 URL 或垃圾，例如，如果你导出没有内容的历史记录，WhatsApp 会添加“音频忽略”“图像忽略”，但你应该完全保留表情符号:)。

# 文本提供者

文本提供者是一个具有以下签名的类

```
def TextProvider(object):
    def get_text(self):
        #Loads the data source and returns its content
    def add_text(self):
        #Add new content to the file 
```

Enter fullscreen mode Exit fullscreen mode

为什么我们需要一个类来完成这个简单的任务？在我的例子中，我想从 dropbox 中获取那个文件，这样 docker 实例就可以很容易地被删除或重新创建。只要您创建了一个匹配该签名的类，您可能希望使用您的本地文件系统或 Google Drive。

我添加了一个`add_text`函数，因为我们可能希望用从我们的平台获得的新内容来填充我们的历史文件。

# 内容生成器

现在我们到了有趣的部分。内容生成器将负责从源数据中学习，并在被调用时返回新内容。

内容生成器看起来像这样:

```
dev ContentGenerator(object):
    def load(self):
        #Setups the instance using a TextProvider
    def get_message(self, text):
        #It expects a text and try to returns a message based on that input 
```

Enter fullscreen mode Exit fullscreen mode

正如我在介绍中提到的，我使用 [Markovify](https://github.com/jsvine/markovify) 实现了一个内容生成器，但是它也可以是任何其他的内容生成器，比如一个训练有素的 RNN。

这些内容生成器中的大多数(可选地)期望一个种子文本，并基于该文本返回一个字符串。您可以通过删除停用词，然后尝试生成有意义的回复，将内容作为参数进行处理..

我使用了 [NLTK](http://www.nltk.org/) 来删除停用词，然后我只是迭代剩余的文本，试图从 Markovify 获得一个有效的内容，如果我没有，我只是要求一个没有种子的文本。

# 平台无关的 Bot

虽然我的机器人被称为 **Telegram** BotFriend，但在几个小时的编码后，我发现它可以在任何平台上工作。我需要在一个平台不可知的类中编写所有的逻辑，然后我需要编写一个使用我的抽象机器人的平台特定的类，看起来像这样:

```
def AbstractBot(object):
    def process_incoming_message(self, chat_id, text):
        #Expects a text and returns a reply using a Content Generator
        #chat_id can be set to 0 if it's not a concept valid in the platform 
```

Enter fullscreen mode Exit fullscreen mode

抽象机器人将由特定于平台的机器人调用，并且:

*   如果提到他，返回一个字符串(是的，是一个他)。
*   它将加入并在收到随机数量的消息后返回一条消息，这就是 chat_id 发挥作用的地方。
*   它可以选择将新消息添加到源数据中。

# 平台特定的 Bot

特定于平台的机器人，如 TelegramBot、TwitterBot 等，将在`__init__`函数中接收平台不可知的机器人，然后在需要时调用`process_incoming_message`方法。

我的电报机器人只是遵循这个简单的签名:

```
class TelegramClient(object):
    def __init__(self, abstract_bot, token): 
```

Enter fullscreen mode Exit fullscreen mode

# 管弦乐手

刚刚找到建议编辑模式:)

如您所见，数据源可以是任何数据，文本提供者可以是任何提供者，内容生成器可以是任何生成器，等等。现在我们需要的东西将抓住所有的成分，设置他们，并连接他们彼此。

在我的实现中，一个`bot_friend.py`脚本从环境(非常容易在 docker 中设置)或命令参数(容易调试)中获取参数，然后烹饪机器人:

```
text_provider = DropboxTextProvider(dropbox_access_token, dropbox_file)
provider = MarkovifyProvider(language, text_provider)
meme_text_provider = DropboxTextProvider(dropbox_access_token, meme_file)
meme_provider = MemeProvider(meme_text_provider)
abstract_bot = AbstractBot(name, provider, auto_feed, meme_provider)
TelegramClient(abstract_bot, token) 
```

Enter fullscreen mode Exit fullscreen mode

是的，当然，我忘了说，我也有一个迷因提供者。如果机器人不能使用迷因，它就不是机器人。

# 开箱即用

您可以按照以下步骤使用我的机器人:

### 创建自己的 TelegramBot

网上有很多教程，但是非常简单:

*   打开与 BotFather 的聊天
*   类型:`/newbot`
*   它会问你名字，然后给你令牌
*   然后你必须键入`/setprivacy`并将其设置为`disable`，以便能够读取收到的消息

### 创建 Dropbox 访问令牌

*   前往[https://www.dropbox.com/developers/apps/create](https://www.dropbox.com/developers/apps/create)
*   创建一个新的“Dropbox API”
*   它会给你一个令牌
*   将聊天记录和 meme 列表文件复制到 Dropbox 创建的文件夹中。

### 码头工人

您可以使用[repo](https://github.com/kblok/TelegramBotFriend)中的指令设置一个新的 docker 容器。

注意:meme 列表文件是逗号分隔的文件，类似于:

```
trigger word(s),meme url 
```

Enter fullscreen mode Exit fullscreen mode

# 最后的话

我喜欢这个小项目的原因是，尽管我不是 Python 开发人员，但它帮助我熟悉了这门语言。如果你对这个项目感兴趣，欢迎加入 Github！我会感谢任何评论，不仅是对工作本身，还有对代码风格和良好实践的评论。

第二，编写机器人很有趣，我是说，超级有趣！

最后但同样重要的是，这个项目打开了学习新东西的大门，已经完成了基本原理。我现在可以回到 TensorFlow 中的 RNN，简单地替换 MarkovifyContentProvider，或者通过用 Slack 替换 TelegramBot 来创建 Slack bot，或者甚至使用同一个 AbstractBot 实例运行两者。

从[hardkoded.com](https://www.hardkoded.com/blogs/not-enough-friends-get-a-bot)