# 芹菜和兔子的异步任务

> 原文：<https://dev.to/zorexsalvo/asynchronous-task-with-celery-and-rabbitmq>

API 响应缓慢？浏览器超时？

也许你有一些非常慢的过程，比如外部 API 调用、电子邮件或短信服务等，在真正返回 OK 响应之前并不需要同步处理。

上周，当我向我们的 QA 演示我的应用程序时，我经历了那个尴尬的暂停/网站挂起/无限加载/“片刻之后”的时刻。老实说，我没想到会是这样，因为我从未在本地机器上体验过。结果是，我的处理挂在我的电子邮件服务部分，其中我必须通过 SMTP 在我们的邮件服务器上执行一个请求。响应时间非常不一致，除非邮件服务器响应或者我的进程超时，否则我的应用程序会一直处于挂起状态。

[![A Few Moments Later](img/4b93e90144fec417f03f6d3521bf2c29.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zymfifv3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://zorexsalvo.com/blog/img/momentslater.jpg)

不用再担心了！因为兔 x 芹菜套餐是为你准备的。RabbitMQ 将被用作消息代理。它存储任务队列，然后由 Celery 逐一执行。怎么用？我这里有一个简单的 python 来演示它。

首先，你得装上芹菜和兔子

```
pip install celery==4.0.2
sudo apt-get install -y rabbitmq-server 
```

Enter fullscreen mode Exit fullscreen mode

然后创建一个名为 task.py 的脚本:

`touch tasks.py`

On tasks.py 初始化芹菜:

```
from celery import Celery
app = Celery('tasks', broker='amqp://localhost//') 
```

Enter fullscreen mode Exit fullscreen mode

然后，创建该方法，用您的芹菜对象来修饰它，以便芹菜能够识别它。

```
@app.task
def create_a_slow_process_needs_async(job_name):
    print('Async job {}'.format(job_name)) 
```

Enter fullscreen mode Exit fullscreen mode

之后你需要启动你的芹菜工:

```
celery -A tasks worker  --loglevel=info

<console>
------------------- celery@zorexsalvo v4.0.2 (latentcall)
--------- **** -----
-------- * ***  * -- Linux-4.4.0-78-generic-x86_64-with-elementary-0.4.1-loki 2017-06-21 16:42:32
-- * - **** ---
- ** ---------- [config]
- ** ---------- .> app:         tasks:0x7fd374ebb210
- ** ---------- .> transport:   amqp://guest:**@localhost:5672//
- ** ---------- .> results:     disabled://
- *** --- * --- .> concurrency: 4 (prefork)
-- ******* ---- .> task events: OFF (enable -E to monitor tasks in this worker)
-------- ***** -----
 -------------- [queues]
                .> celery           exchange=celery(direct) key=celery

[tasks]
  . tasks.reverse

[2017-06-21 16:42:32,523: INFO/MainProcess] Connected to amqp://guest:**@127.0.0.1:5672//
[2017-06-21 16:42:32,529: INFO/MainProcess] mingle: searching for neighbors
[2017-06-21 16:42:33,545: INFO/MainProcess] mingle: all alone
[2017-06-21 16:42:33,556: INFO/MainProcess] celery@zorexsalvo ready. 
```

Enter fullscreen mode Exit fullscreen mode

然后尝试在 shell 中执行作业:

```
 Python 2.7.12 (default, Nov 19 2016, 06:48:10)
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import tasks
>>> tasks.create_a_slow_process_needs_async.delay('Send email blast!!')
<AsyncResult: 41ba4299-77a1-421b-9c35-873347f98b14> 
```

Enter fullscreen mode Exit fullscreen mode