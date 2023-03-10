# SparkPost 消息事件使事件数据变得简单

> 原文：<https://dev.to/sparkpost/event-data-made-easy-with-sparkpost-message-events>

我们喜欢开发者使用 SparkPost [webhooks](https://www.sparkpost.com/blog/webhooks-beyond-the-basics/) 来构建令人敬畏的响应服务。当你需要实时反馈你的客户在用他们的信息做什么的时候，Webhooks 是很棒的。他们采用“推送模式——你创建一个[微服务](https://en.wikipedia.org/wiki/Microservices)来处理事件流。

您知道 SparkPost 还支持“拉模型消息事件 [API](https://developers.sparkpost.com/api/message-events.html) 吗？它可以让您下载长达十天的事件数据。这在以下情况下特别有用:

*   您发现创建和维护生产就绪的微服务非常困难。例如，您的公司 IT 策略可能会使您很难让开放端口永久侦听；
*   您熟悉批处理类型操作和运行周期性工作负载，因此您不需要实时消息事件；
*   你是一个坚定的 webhooks 粉丝，但你正在调查你几乎正常工作的 webhooks receiver 微服务的问题，并且想要一份这些事件的参考副本来进行比较。

如果这听起来像你的情况，你来对地方了！现在让我们通过设置一个非常简单的工具来获取这些事件。

### 设计目标

让我们首先列出该项目的需求，然后将它们转化为工具的设计目标:

*   您希望无需编程即可轻松定制。
*   SparkPost 事件是丰富的数据源，但是某些事件类型和事件属性可能与您无关。有选择性的输出文件更小，这是一件好事，对吗？
*   说到输出文件，您希望事件数据采用常用的`csv`文件格式。虽然程序员喜欢 JSON，但是 CSV 对于非技术用户来说更容易(并且文件更小)。
*   您希望一次性设置您的 SparkPost 帐户凭据和其他基本信息，而不必在每次使用时都重新设置。不得不记住那些东西是令人厌烦的。
*   您需要灵活掌握感兴趣的事件日期/时间范围。
*   您希望设置一次本地时区，然后在该时区工作，而不是手动将值转换为 UTC 时间。当然，如果你真的想用 UTC 工作，因为你的其他服务器日志都是 UTC，那么“就这样做吧。”
*   在你的屏幕上提供一些有意义的安慰报告。提取数百万个事件可能需要一些时间来运行。我想知道它起作用了。

### 事件，亲爱的程序员，事件…

首先，你需要在你的系统上安装 Python 3 和`git`。对于 Linux，一个简单的过程可以在我们之前的[博客文章](https://www.sparkpost.com/blog/sending-scheduled-mailings-simply/)中找到。真的很简单:

```
sudo su -
yum update -y
yum install -y python35
yum install -y wget
wget https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py 
yum install -y git 
```

对于其他平台，[这个](https://www.python.org/downloads/)是获取最新 Python 下载的一个很好的起点；有很多关于如何安装的好教程。

然后使用从 Github 获取`sparkyEvents` [代码:](https://github.com/tuck1s/sparkyEvents)

```
$ git clone https://github.com/tuck1s/sparkyEvents.git
Initialized empty Git repository in /home/stuck/sparkyEvents/.git/
remote: Counting objects: 32, done.
remote: Compressing objects: 100% (22/22), done.
remote: Total 32 (delta 7), reused 28 (delta 5), pack-reused 0
Unpacking objects: 100% (32/32), done.

$ cd sparkyEvents 
```

### 我们是[骑士团](https://www.youtube.com/watch?v=zIV4poUZAQo)卫说”。ini "

在这里按照 Github 自述文件[中的例子建立一个`sparkpost.ini`文件。](https://github.com/tuck1s/sparkyEvents/blob/master/README.md)

将`<YOUR API KEY>`替换为~~一个灌木林~~你特定的、私有的 API 密匙。

`Host`仅用于 SparkPost 企业服务用途；对于[sparkpost.com](https://www.sparkpost.com/)可以省略。

`Events`是一个列表，根据 [SparkPost 事件类型](https://developers.sparkpost.com/api/message-events.html#message-events-message-events-get)；省略该行，或将其指定为空白，以选择所有事件类型。

`Properties`可以是任意一个 [SparkPost 事件属性](https://www.sparkpost.com/docs/tech-resources/webhook-event-reference/)。按照 [Python 的说法，定义可以使用缩进来拆分行。ini 文件结构](https://docs.python.org/3/library/configparser.html#supported-ini-file-structure)，这是方便的，因为有近六十个不同的属性。您可以只选择那些您想要的属性，而不是所有的属性；这使输出文件只包含您想要的信息。

`Timezone`可根据您的语言环境进行配置。SparkPost 用它来解释你在命令行参数中给出的事件时间范围`from_time`和`to_time`。如果您留空，SparkPost 将默认使用 UTC。

如果在没有任何命令行参数的情况下运行该工具，它会打印用法:

```
$ ./sparkyEvents.py 

NAME
   ./sparkyEvents.py
   Simple command-line tool to retrieve SparkPost message events into a .CSV file.

SYNOPSIS
  ./sparkyEvents.py outfile.csv from_time to_time

MANDATORY PARAMETERS
    outfile.csv output filename, must be writeable. Records included are specified in the .ini file.
    from_time
    to_time Format YYYY-MM-DDTHH:MM 
```

`from_time`和`to_time`是包含性的，因此，例如，如果您想要一整天的事件，请使用时间 T00:00 到 T23:59。

这是该工具的典型运行，提取了超过 1800 万个事件。这次跑步花了两个多小时才完成。

```
./sparkyEvents.py outfile.csv 2017-06-04T00:00 2017-06-04T23:59

SparkPost events from 2017-06-04T00:00 to 2017-06-04T23:59 America/New_York to outfile.csv
Events: <all>
Properties: ['timestamp', 'type', 'event_id', 'friendly_from', 'mailfrom', 'raw_rcpt_to', 'message_id', 'template_id', 'campaign_id', 'subaccount_id', 'subject', 'bounce_class', 'raw_reason', 'rcpt_meta', 'rcpt_tags']
Total events to fetch: 18537125
Page 1: got 10000 events in 5.958 seconds
Page 2: got 10000 events in 5.682 seconds
Page 3: got 10000 events in 5.438 seconds
Page 4: got 10000 events in 6.347 seconds
:
: 
```

就是这样！您现在可以使用该工具了。想看看代码的内部吗？继续读！

### 内码

**通过 SparkPost API 获取事件**

SparkPost Python 库还没有对`message-events`端点的内置支持。实际上，Python `requests`库就是我们所需要的。它为处理 JSON 数据、响应状态代码等提供了内置的抽象，通常是一件美妙的事情。

这里我们需要注意的一点是消息事件端点是有速率限制的。如果我们发出太多请求，SparkPost 会回复一个`429`响应代码。我们很好地使用了下面的函数，它休眠一段时间，然后重试:

```
def getMessageEvents(uri, apiKey, params):
   try:
       path = uri + '/api/v1/message-events'
       h = {'Authorization': apiKey, 'Accept': 'application/json'}

       moreToDo = True
       while moreToDo:
           response = requests.get(path, timeout=T, headers=h, params=params)

           # Handle possible 'too many requests' error inside this module
           if response.status_code == 200:
               return response.json()
           elif response.status_code == 429:
               if response.json()['errors'][0]['message'] == 'Too many requests':
                   snooze = 120
                   print('.. pausing', snooze, 'seconds for rate-limiting')
                   time.sleep(snooze)
                   continue # try again
           else:
               print('Error:', response.status_code, ':', response.text)
               return None

   except ConnectionError as err:
       print('error code', err.status_code)
       exit(1) 
```

实际上，当使用 10000 个事件批次时，即使在快速客户端上，我也没有遇到任何限速响应。我不得不在测试期间故意设置较小的批量，所以在实践中您可能看不到速率限制。

**选择事件属性**

SparkPost 的事件有近 60 种可能的属性。用户可能不需要全部，所以让我们通过 sparkpost.ini 文件选择它们。与其他 Python 项目一样，优秀的 ConfigParser 库完成了这里的大部分工作。它支持一个很好的多行特性:

"值也可以跨多行，只要它们比值的第一行缩进得更深."

我们可以读取属性(如果没有，应用合理的默认值)，删除任何换行符或回车符，并转换成 Python 列表，只需三行代码:

```
# If the fields are not specified, default to a basic few properties = cfg.get('Properties', 'timestamp,type')
properties = properties.replace('\r', '').replace('\n', '') # Strip newline and CR fList = properties.split(',') 
```

**写入文件**

Python [csv 库](https://docs.python.org/3.6/library/csv.html)使我们能够基于我们刚刚阅读的`fList`创建输出文件，包括所需的标题行字段名称:

```
fh = csv.DictWriter(outfile, fieldnames=fList, restval='', extrasaction='ignore')
fh.writeheader() 
```

使用`DictWriter`类，数据自动匹配输出文件中的字段名，并按预期的顺序写入每一行。确保我们为缺失的数据发出空白，因为不是所有的事件都有所有的属性。
`extrasaction=’ignore’`确保我们跳过不想要的额外数据。

```
for i in res['results']:
   fh.writerow(i) # Write out results as CSV rows in the output file 
```

这几乎是所有值得注意的事情。该工具的实际代码不到 150 行。

### 你是事件的主宰！

原来如此！你现在可以从 SparkPost 下载大量的事件，并且可以定制你得到的输出文件。你现在是事件的主人！

*本帖最初发表于[sparkpost.com](https://www.sparkpost.com/blog/sparkpost-message-events-api/)T3】*