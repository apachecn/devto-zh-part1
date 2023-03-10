# 简单地用 SparkPost 发送预定邮件

> 原文：<https://dev.to/sparkpost/sending-scheduled-mailings-simply-with-sparkpost>

你需要在固定时间同步发送一批电子邮件吗？您是否不确定是开发自己的活动管理工具，还是购买现成的工具？您是否已经阅读了我们的[入门指南](https://www.sparkpost.com/docs/getting-started/sparkpost-new-user-guide/)，并受到启发发送了您的第一个活动，但对编写自己的代码感到有些紧张？

我们的一个客户最近也有同样的需求——每天早上发送几百万封电子邮件。他们查看了来自 [SparkPost 合作伙伴](https://www.sparkpost.com/partners/)的一些功能齐全的活动管理工具，如 [Iterable](https://iterable.com/) 、 [Ongage](https://www.ongage.com/) 和 [Cordial](http://cordial.io/) 等，这些工具满足了这一需求，此外还有许多其他工具。当您有许多不同的活动类型、复杂的“客户之旅”活动以及集成的所见即所得编辑器时，它们是一个不错的选择。

然而，如果你正在寻找简单的东西，有另一种方法-你在正确的地方！ [SparkPost 的 Python 库](https://github.com/SparkPost/python-sparkpost)和我们的[内置的预定发送功能](https://www.sparkpost.com/blog/why-not-schedule-the-emails-you-send/)使得把一些东西放在一起变得很容易。

我们将设身处地为我们友好的虚构公司 Avocado Industries 着想，并跟随他们开展一项活动。本文将带您了解 SparkPost 的 Python 客户端库的各种特性，并在这里链接到最终代码[。](https://github.com/tuck1s/sparkySched)

## 那么我需要什么呢？

您正在向您的订户发送简讯。你已经[创建了一个好看的模板](https://www.sparkpost.com/docs/getting-started/creating-template/)并上传到 SparkPost。您手边有收件人列表，或者可以很容易地从数据库中导出。您希望 SparkPost 将收件人的个性化信息合并到邮件中，并发出您的精彩邮件。

这些需求转化为我们将要编写的代码的以下设计目标:

*   使用参数和一个小文本文件来指定发送的所有内容。你不想改变每个活动的代码。
*   利用 SparkPost 存储的模板和个性化功能，无需自己进行任何编程。
*   对收件人列表使用本地平面文件。SparkPost 的[存储的收件人列表](https://support.sparkpost.com/customer/portal/articles/2351320-uploading-and-storing-a-recipient-list-as-a-csv-file?_ga=2.108761730.1899408583.1496187922-424867781.1485888065)上传使用的相同格式非常适合。
*   从你的列表中收集收件人到 API 调用批处理中以提高效率——对你发送的总大小没有上限。
*   支持定时发送的时区，还支持“立即发送”。

## 数据鳄梨酱

SparkPost 收件人列表格式如下所示，所有字段都已填充。在这里，我们看到杰罗姆的鳄梨产业忠诚度计划的细节。我们可以看到他是一名金卡会员，住在华盛顿州，喜欢几种不同的鳄梨品种。

```
email,name,return_path,metadata,substitution_data,tags
jerome.russell@example.com,Jerome Russell,bounce@avocado-industries.com,"{""custID"": 60525717}","{""memberType"": ""gold"", ""state"": ""WA""}","[""hass"", ""pinkerton"", ""gwen"", ""lamb hass"", ""fuerte"", ""bacon""]" 
```

Enter fullscreen mode Exit fullscreen mode

除了电子邮件地址之外的所有内容都是可选的，所以如果这个工具也能接受一个简单的旧的电子邮件地址列表就好了。如果我们省略了标题行，工具会很高兴，这也很好。这很容易做到。

## Taco 我要出发了

该工具是为`python3`编写的。SparkPost 依赖于`pip`安装程序，我们需要`git`来获得这个工具。您可以使用以下命令检查您是否已经拥有这些工具。

```
$ python3 -V
Python 3.5.1
$ pip3 -V
pip 9.0.1 from /usr/local/lib/python3.5/site-packages (python 3.5)
$git --version
git version 2.7.4 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经有了，继续下面的“添加 SparkPost Python 库酱”。否则，下面是 Amazon EC2 Linux 的简单安装顺序:

```
sudo su -
yum update -y
yum install -y python35
yum install -y wget
wget https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py 
yum install -y git 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在使用另一个平台，请在此查看您的平台的安装说明[。](https://www.python.org/)

## 添加 SparkPost Python 库酱

我们用`pip3`来安装，如下。

```
$ sudo /usr/local/bin/pip3 install sparkpost
Collecting sparkpost
  Using cached sparkpost-1.3.5-py2.py3-none-any.whl
Collecting requests>=2.5.1 (from sparkpost)
  Using cached requests-2.13.0-py2.py3-none-any.whl
Installing collected packages: requests, sparkpost
Successfully installed requests-2.13.0 sparkpost-1.3.5 
```

Enter fullscreen mode Exit fullscreen mode

使用
从 Github 获取`sparkySched`代码

```
$ git clone https://github.com/tuck1s/sparkySched.git
Cloning into 'sparkySched'...
remote: Counting objects: 55, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 55 (delta 0), reused 0 (delta 0), pack-reused 52
Unpacking objects: 100% (55/55), done.
Checking connectivity... done.

$ cd sparkySched 
```

Enter fullscreen mode Exit fullscreen mode

## 得了。我想赢得它

我们现在在一个文本文件中设置一些属性，如 API 密钥、活动和某些替代数据，因为它们在每次发送时都是相同的。名为`sparkpost.ini.example`的项目中提供了一个例子。将其重命名为`sparkpost.ini`，并用你在自己的 SparkPost 帐户中创建的密钥替换*。* 

```
[SparkPost]
Authorization = <YOUR API KEY>
#Campaign setup Campaign = avocado-saladcopter
GlobalSub = {"subject": "Fresh avocado delivered to your door in 30 minutes by our flying saladcopter"} 
```

Enter fullscreen mode Exit fullscreen mode

## 还有，发！

我们可以将项目中包含的 1000 名安全测试接受者的样本文件发送到。将下面的模板名称从`avocado-goodness`更改为您帐户中的名称，并设置适合您的发送时间:

```
$ ./sparkySched.py recips_1k_sub_n_tags.csv avocado-goodness 2017-05-08T19:10:00+01:00
Opened connection to https://api.sparkpost.com
Injecting to SparkPost:
To  1000 recips: template "avocado-goodness" start_time 2017-05-08T19:10:00+01:00: OK - in 1.62 seconds
$ 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，您应该看到“OK”行，您的邮件就发送了。这就是你需要做的。快乐发送！

## 代号莎莎

在这一节中，我们深入了解代码内部。如果你只是想使用工具而不是改变它，你可以跳过这一步。下面是我们如何调用 SparkPost API 来发送消息，使用 [SparkPost Python 库](https://github.com/SparkPost/python-sparkpost) :

```
# Inject the messages into SparkPost for a batch of recipients, using the specified transmission parameters def sendToRecips(sp, recipBatch, sendObj):
   print('To', str(len(recipBatch)).rjust(5, ' '),'recips: template "'+sendObj['template']+'" start_time '+sendObj['start_time']+': ',end='', flush=True)

   # Compose in additional API-call parameters
   sendObj.update({
       'recipients': recipBatch,
       'track_opens':  True,
       'track_clicks': True,
       'use_draft_template': False
   })
   startT = time.time()
   try:
       res = sp.transmissions.send(**sendObj)
       endT = time.time()
       if res['total_accepted_recipients'] != len(recipBatch):
           print(res)
       else:
           print('OK - in', round(endT - startT, 3), 'seconds')
   except SparkPostAPIException as err:
       print('error code', err.status, ':', err.errors)
       exit(1) 
```

Enter fullscreen mode Exit fullscreen mode

在屏幕上输出一些关于我们要发送的内容的有用信息后，该函数使用`sendObj.update()`将接收者与其他传入的成分组合在一起，并混合一些合理的默认值。

SparkPost 库调用被包装在一个`try/except`子句中，因为它可能在应用程序级别返回错误(比如有一个不正确的 API 密钥)，或者在传输层返回错误(比如您的互联网连接中断)。对于任何与远程服务通信的代码来说，这通常是一个好的实践，并遵循我们的库打包的示例[。](https://github.com/SparkPost/python-sparkpost/blob/master/examples/transmissions/handle_exception.py)

我们使用`startT`、`endT`和`time()`函数来测量 API 调用实际花费的时间。虽然不是绝对必要的，但观察性能如何随批处理大小、客户端到服务器的路由距离等而变化是很有趣的。

我们现在将编写代码从`.ini`文件中读取参数，并在 API 发送中使用它们。让我们阅读并检查强制参数:

```
# Get parameters from .ini file configFile = 'sparkpost.ini'
config = configparser.ConfigParser()
config.read_file(open(configFile))
cfg = config['SparkPost']
apiKey = cfg.get('Authorization', '')           # API key is mandatory if not apiKey:
   print('Error: missing Authorization line in ' + configFile)
   exit(1)
baseUri = 'https://' + cfg.get('Host', 'api.sparkpost.com')     # optional, default to public service 
```

Enter fullscreen mode Exit fullscreen mode

Python 库`configParser`完成了繁重的工作。你必须有一个 API 密匙，所以如果不设置，我们就退出。如果未设置，baseUri 默认为[sparkpost.com API 端点](https://developers.sparkpost.com/api/?_ga=2.100782849.1899408583.1496187922-424867781.1485888065)。中的其他参数。ini 文件的读取方式相同，并在项目自述文件中有所描述。

在 Python 中还有其他的设置方式，比如使用环境变量。我的偏好是。ini 文件，因为文件就在那里，盯着你。它很容易存储，交流，改变和检查，就在你的项目中。

## 鸡进去，馅饼出来...

让我们看看如何阅读。csv 格式收件人列表。Python 提供了一个很好的库，`csv`。所有引用双引号的内容。csv 文件需要携带 JSON 对象，像`"{""custID"": 60525717}"`就是为我们处理的。

我们可以使用`csv`将整个收件人列表读入一个 Python 数组对象——但是如果我们的列表中有数十亿个地址，这并不是一个好主意。对于我们的目的来说，客户端将足够快，并且我们将使用更少的客户端内存，如果我们每次读取的数据足以给我们一个相当大的批处理。

我们还将特别处理文件的第 1 行，以满足我们的“轻松对待可选文件头”的要求。回想一下，一个格式良好的标题应该是这样的:

```
email,name,return_path,metadata,substitution_data,tags 
```

Enter fullscreen mode Exit fullscreen mode

如果在第 1 行的某个地方有一个单词“email ”,让我们假设它真的是一个标题，我们将从那一行获取我们的字段布局。如果您省略了可选字段，或者让它们以不同的顺序出现在一行中，该工具会很高兴。唯一你绝对需要的是电子邮件字段。

```
recipBatch = []
f = csv.reader(fh_recipList)

for r in f:
   if f.line_num == 1:                         # Check if header row present
       if 'email' in r:                        # we've got an email header-row field - continue
           hdr = r
           continue
       elif '@' in r[0] and len(r) == 1:       # Also accept headerless format with just email addresses
           hdr = ['email']                     # line 1 contains data - so continue processing
       else:
           print('Invalid .csv file header - must contain "email" field')
           exit(1) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们通过检查带有@符号的单个条目，来检查它是否真的是一个只有一堆电子邮件地址的无头文件。

在主循环`for i,h in enumerate(hdr)`中，我们使用了一些不错的 Python 语言特性来使数据符合 SparkPost 所期望的 JSON 对象。`name`字段需要放在`address.name` JSON 属性中。如果存在，则添加 Return_path。元数据、substitution_data 和标签都是以 JSON 格式的字符串进入我们的，所以我们使用`json.loads()`对它们进行解包。

```
 # Parse values from the line of the file into a row object
   row = {}
   for i,h in enumerate(hdr):
       if r[i]:                                        # Only parse non-empty fields from this line
           if h == 'email':
               row['address'] = {h: r[i]}              # begin the address
           elif h == 'name':
               row['address'].update(name = r[i])      # add into the existing address structure
           elif h == 'return_path':
               row[h] = r[i]                           # simple string field
           elif (h == 'metadata' or h == 'substitution_data' or h == 'tags'):
               row[h] = json.loads(r[i])               # parse these fields as JSON text into dict objects
           else:
               print('Unexpected .csv file field name found: ', h)
               exit(1)

   recipBatch.append(row)
   if len(recipBatch) >= batchSize:
       sendToRecips(sp, recipBatch, txOpts)
       recipBatch = []                                 # Empty out, ready for next batch
# Handle the final batch remaining, if any if len(recipBatch) > 0:
   sendToRecips(sp, recipBatch, txOpts) 
```

Enter fullscreen mode Exit fullscreen mode

剩下要做的就是仔细检查列表，每次我们收集完整的列表时发送。我们在最后发送最后一批，我们就完成了。

## ——一撮~~百里~~时间

我们需要的最后一部分是一些命令行参数解析。收件人列表、模板 ID 和发送日期/时间是每次运行该工具时您可能想要更改的内容。Python 使用`argv[]`来处理你的论点，就像其他语言一样。

输入日期和时间有各种各样的无意义可能——比如 2 月 30 日，24:01 等等。混合时区偏移量，这样用户可以按照他们的本地时间进行调度，没有人会真的想要编写自己的时间解析代码！SparkPost 的 API 当然将是好的和不好的最终仲裁者——但是在我们尝试发送之前最好做一些初步的品尝测试。

Python 的`strptime()`函数主要做我们想要的事情。除了 Python 在`%z`时区没有`:`分隔符之外，格式字符串可以像 SparkPost 格式一样。Python 优雅的对字符串的负索引(从字符串末尾向后)使得编写一个小的检查函数变得很容易。

```
# Validate SparkPost start_time format, slightly different to Python datetime (which has no : in timezone offset format specifier) def isExpectedDateTimeFormat(timestamp):
   format_string = '%Y-%m-%dT%H:%M:%S%z'
   try:
       colon = timestamp[-3]
       if not colon == ':':
           raise ValueError()
       colonless_timestamp = timestamp[:-3] + timestamp[-2:]
       datetime.strptime(colonless_timestamp, format_string)
       return True
   except ValueError:
       return False 
```

Enter fullscreen mode Exit fullscreen mode

## 日菜

如果您不想安排未来的开始时间，您可以只给出今天的日期和时间。过去的时间会立即发送。

根据您的代码运行的位置(我碰巧使用的是 AWS 虚拟机)，您应该会在几秒钟内看到每一批代码都被发送出去。即使它是单线程的，你也可以在十分钟左右安排一百万封电子邮件。实际发送将尽可能快地进行(在预定的 start_time)。

```
$ ./sparkySched.py recips_100k_sub_n_tags.csv avocado-goodness 2017-04-11T23:55:00+01:00
Opened connection to https://demo.sparkpostelite.com
Injecting to SparkPost:
To 10000 recips: template "avocado-goodness" start_time 2017-04-11T23:55:00+01:00: OK - in 4.97 seconds
To 10000 recips: template "avocado-goodness" start_time 2017-04-11T23:55:00+01:00: OK - in 4.92 seconds
To 10000 recips: template "avocado-goodness" start_time 2017-04-11T23:55:00+01:00: OK - in 4.783 seconds 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样。完整的代码只有 100 多行，这里有简单的安装说明。

## 一个小文摘...

真实测试这个的最好方法是什么？一个工具，以产生虚假收件人名单与天坑地址可能是方便的。请留意后续的博客帖子..我在 [github 项目](https://github.com/tuck1s/sparkySched)中包含了几个现成的食谱收件人文件，以帮助您入门。

这是你第一次使用 Python 和 SparkPost 用餐吗？我是不是加了太多调料？作者应该因为这些糟糕的笑话而被讽刺吗？让我们知道！

P.S .想和我们多聊聊 Python 吗？加入我们的[社区休闲活动](http://slack.sparkpost.com/?_ga=2.209442194.1899408583.1496187922-424867781.1485888065)。

这篇文章最初发表在 [SparkPost 博客](https://www.sparkpost.com/blog/sending-scheduled-mailings-simply/)上。*