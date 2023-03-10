# 数据表和 Django:终于有了 Ajax！

> 原文：<https://dev.to/codeshard/datatables-and-django-finally-with-ajax>

Django 和 T2 与 Ajax 的数据表:上周我的克星。但是经过几次尝试，并且在 [StackOverflow](http://stackoverflow.com/) 和 Django [Documentation](http://django.readthedocs.org/en/latest/releases/1.10.html) 中伤了我的眼睛，我终于找到了解决方案。我是怎么解决的？只要仔细阅读 json 输出和 DataTables 1.10 的 [changelog](http://www.datatables.net/new/1.10) 以及它们现在如何处理 json 和 ajax 请求。如果你正在读这篇文章，也许你不知道 Django 或 DataTables 是如何工作的，但是互联网上有很多很棒的教程，所以我不会解释如何使用它们。我们开始吧。您应该定义一个模型，类似于:

*models . py*T2】

```
from django.db import models

class MyModel(models.Model):
    someAttr = models.CharField()

    def __unicode__(self):
        return self.someAttr 
```

Enter fullscreen mode Exit fullscreen mode

然后，您应该定义一个视图，以 json 格式返回一个 queryset。

*views.py*

```
from django.http import HttpResponse
from django.core import serializers
from .models import MyModel

def myModel_asJson(request):
    object_list = MyModel.objects.all() #or any kind of queryset
    json = serializers.serialize('json', object_list)
    return HttpResponse(json, content_type='application/json') 
```

Enter fullscreen mode Exit fullscreen mode

这里，您必须为 ajax 视图定义一个 url，如下所示:

*urls.py*

```
from django.conf.urls import patterns, url

urlpatterns = patterns('myapp.views',
                    url(regex=r'^$',
                    view='myModel_asJson',
                    name='my_ajax_url'),
) 
```

Enter fullscreen mode Exit fullscreen mode

然后，在您的模板中，您应该定义一个表和 javascript 函数，类似于:

*template.html*T2】

```
<table cellpadding="0" cellspacing="0" border="0" id="example">
     <thead>
         <tr><th>My Attr Heading</th></tr>
     </thead>
     <tbody></tbody>
</table>

<script type="text/javascript" language="javascript" class="init">
     $(document).ready(function() {
         $('#example').dataTable( {
             "processing": true,
             "ajax": {
                 "processing": true,
                 "url": "{% url 'my_ajax_url' %}",
                 "dataSrc": ""
             },

             "columns": [
                     { "data": "fields.someAttr },
                     { "data": "pk" }
                 ]
         } );
     } );
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

注意，由于 Django 输出的 json 的格式，dataSrc 为空。您还应该阅读如何在 Django 中序列化自然键，以防您的模型中有外键。这很简单，但是文档解释得很清楚:只需给模型本身添加一个方法:

```
def natural_key(self):
    return self.my_natural_key 
```

Enter fullscreen mode Exit fullscreen mode

这样可以确保 json 遵循外键的值，而不是外键本身(这意味着整数值)。

你成功了！或者我希望如此。上面的例子对我有用，我希望它是不言自明的，至少比互联网上的其他例子更好。