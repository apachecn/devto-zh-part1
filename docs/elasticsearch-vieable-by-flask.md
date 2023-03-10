# 可通过 Flask 查看的弹性搜索

> 原文：<https://dev.to/lukaszkuczynski/elasticsearch-vieable-by-flask>

# 用于 ES 的 API 为什么

那么目标是什么呢？我想有一个小应用程序，这将允许贪婪的人力资源工作者探索我参加的项目。我相信，一个人的经历是未来(IT)工作者最重要的特征；学校是很久以前的事了，课程还可以，但是谁知道是谁帮助完成的呢？

# Python 为 API

通过使用 Django 做一些小项目，我相信这是一个很好的网络应用框架。但是我们需要像那样的全功能机器吗？不。有时微服务可以用几行代码编写。为什么

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

是的，这就是编写监听端口 5000 的服务的全部内容，如果你用
运行它的话

```
FLASK_APP=hello.py flask run 
```

Enter fullscreen mode Exit fullscreen mode

这是所有的乡亲。

# py 管理的 Elasticsearch

在 py 官方图书馆搜索 ES 很有趣。简单如下:

```
from elasticsearch import Elasticsearch
es = Elasticsearch()
response = self.es.search(index=INDEX_PATTERN, doc_type=PROJECT_DOCTYPE, q=lucene_query) 
```

Enter fullscreen mode Exit fullscreen mode

响应是常规的 json，所以使用 pythonic dicts 访问它的身体是可能的。

# Python + ES =快速 API 工作

即使使用 TDD 方法，我也能在 2 到 3 个小时内展示我未来 webapp 的搜索功能。集成测试也很容易，正如这个[烧瓶测试](https://github.com/lukaszkuczynski/search_my_bio/blob/master/api/tests/itest_apiserver.py)和[弹性测试](https://github.com/lukaszkuczynski/search_my_bio/blob/master/api/tests/itest_elasticdao.py)的例子所示。到目前为止，在我的 [github 项目页面](https://github.com/lukaszkuczynski/search_my_bio)上可以看到所有结果。