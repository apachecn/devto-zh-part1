# 如何使用 Django 和 Pusher 构建一个照片提要

> 原文：<https://dev.to/samuelayo/how-to-build-a-photo-feed-using-django-and-pusher-409e>

[![](img/9aa549b307019504f12a595e10cabdcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0A_sNzfm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/06/build-a-photo-feed-using-django-and-pusher.gif)

今天，我们将利用 Django 和 Pusher 制作一个实时照片馈送框架。这就像一个迷你 Instagram，但没有评论和过滤功能。遵循本教程需要对 Django 和 jQuery 有一个基本的了解。

这篇文章最初发表在 Pusher 的博客[这里](https://blog.pusher.com/build-a-photo-feed-using-django/)

## 设置姜戈

首先，如果我们还没有 Django 库，我们需要安装它。为了安装 Django，我们运行:

```
pip install django 
```

Enter fullscreen mode Exit fullscreen mode

安装完 Django 之后，就该创建我们的项目了。

打开一个终端，使用下面的命令创建一个新项目:

```
django-admin startproject photofeed 
```

Enter fullscreen mode Exit fullscreen mode

在上面的命令中，我们创建了一个名为`photofeed`的新项目。下一步将是在我们的新项目中创建一个应用程序。为此，让我们运行以下命令:

```
//change directory into the pusher_message directory 
cd photofeed 
//create a new app where all our logic would live 
django-admin startapp feed 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们完成了新应用的设置，Django 需要了解我们的新应用。

为此，我们将进入`feed\settings.py`，将消息应用添加到我们已安装的应用中，如下所示:

```
INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'feed'
    ] 
```

Enter fullscreen mode Exit fullscreen mode

完成以上工作后，就该运行应用程序了，看看是否一切顺利。

在我们的终端 shell 中，我们运行:

```
python manage.py runserver 
```

Enter fullscreen mode Exit fullscreen mode

如果我们将浏览器导航到`http://localhost:8000`，我们应该会看到以下内容:

[![](img/3835a1f46b399d09bed6b401cf74b2d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3x7TVTga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/06/build-a-photo-feed-using-django-1.png)

## 在 Pusher 上设置一个 App

至此，Django 已经准备就绪。接下来，我们需要设置 Pusher，并获取我们的应用程序凭据。

如果你还没有，注册一个免费的[推手](https://pusher.com/signup)账户，创建一个新的应用程序，然后复制你的秘密、应用程序密钥和应用程序 id。

[![](img/45f89e4624a3b20fab17a8b4587662de.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iso2E4GS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/06/build-a-photo-feed-using-django-2.png)

下一步是安装所需的库:

```
pip install pusher 
```

Enter fullscreen mode Exit fullscreen mode

在上面的 bash 命令中，我们安装了一个包 Pusher。

–Pusher:这是 Python 的官方 Pusher 库。我们将使用这个库来触发和发送我们的消息到 Pusher HTTP API

## 创建我们的应用

首先，让我们创建一个模型类，它将生成我们的数据库结构。

我们把`feed\models.py`打开，换成下面的:

```
 from django.db import models

    # Create your models here.
    class Feed(models.Model):
        description = models.CharField(max_length=255, blank=True)
        document = models.FileField(upload_to='static/documents/') 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码块中，我们定义了一个名为`Feed`的模型。提要表将由以下字段组成:

*   存储照片描述的字段
*   存储照片的字段

在上面的代码中，在声明我们的文档字段时，我们包含了一个`upload_to`属性，我们将其设置为`static/documents`。请注意，这个路径是相对于我们现在要设置的`DJANGO MEDIA ROOT`的路径。

在本文中，我们将把`MEDIA_ROOT`设置到我们的`feed`应用程序的静态文件夹中，这样它就可以作为一个静态文件。为此，让我们转到我们的`photofeed/settings.py`，将下面的代码添加到我们的文件中，紧接在`STATIC_URL`声明之后。

```
MEDIA_ROOT = os.path.join(BASE_DIR, 'feed/') 
```

Enter fullscreen mode Exit fullscreen mode

### 运行迁移

我们需要进行迁移并运行它们，这样就可以创建我们的数据库表。为此，让我们在终端中运行以下命令:

```
python manage.py makemigrations 
python manage.py migrate 
```

Enter fullscreen mode Exit fullscreen mode

### 创造我们的观点

我们的视图指的是支撑应用程序背后逻辑的文件，通常称为`Controller`。

让我们打开`feed`文件夹中的`views.py`，用下面的替换:

```
 from django.shortcuts import render, HttpResponse
    from django.http import JsonResponse
    from .forms import *
    from pusher import Pusher
    import json

    #instantiate pusher
    pusher = Pusher(app_id=u'XXX_APP_ID', key=u'XXX_APP_KEY', secret=u'XXX_APP_SECRET', cluster=u'XXX_APP_CLUSTER')
    # Create your views here.
    # function that serves the welcome page
    def index(request):
        # get all current photos ordered by the latest
        all_documents = Feed.objects.all().order_by('-id')
        # return the index.html template, passing in all the feeds
        return render(request, 'index.html', {'all_documents': all_documents})

    #function that authenticates the private channel 
    def pusher_authentication(request):
        channel = request.GET.get('channel_name', None)
        socket_id = request.GET.get('socket_id', None)
        auth = pusher.authenticate(
          channel = channel,
          socket_id = socket_id
        )

        return JsonResponse(json.dumps(auth), safe=False)
    #function that triggers the pusher request
    def push_feed(request):
        # check if the method is post
        if request.method == 'POST':
            # try form validation
            form = DocumentForm(request.POST, request.FILES)
            if form.is_valid():
                f = form.save()
                # trigger a pusher request after saving the new feed element 
                pusher.trigger(u'a_channel', u'an_event', {u'description': f.description, u'document': f.document.url})
                return HttpResponse('ok')
            else:
                # return a form not valid error
                return HttpResponse('form not valid')
        else:
           # return error, type isnt post
           return HttpResponse('error, please try again') 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们定义了两个主要函数:

*   指数
*   pusher _ 认证 _
*   推送进给

在`index`函数中，我们获取数据库中所有可用的照片。照片随后在视图中渲染。这使得新用户能够看到所有以前可用的提要。

在`pusher_authentication`函数中，我们验证当前用户可以访问我们的私有频道。

在`push_feed`函数中，我们检查它是否是一个 POST 请求，然后在将表单保存到数据库之前，我们尝试验证它。(该方法中使用的名为`DocumentForm`的表单尚不可用。我们将很快创建它。)在表单验证之后，我们调用 Pusher 库进行实时交互。

### 创建表单类

Django 表单处理用户输入、验证输入并将其转换成 Python 对象。他们也有一些方便的渲染方法。

让我们在`feed`文件夹中创建一个名为`forms.py`的文件，并在其中添加以下内容:

```
 from django import forms
    from .models import Feed

    class DocumentForm(forms.ModelForm):
        class Meta:
            model = Feed
            fields = ('description', 'document', ) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码块中，我们已经导入了我们的 Feed 模型，并使用它来创建一个表单。该表单现在将处理图像的验证和上传到正确的文件夹。

### 填充 URL 的. py

让我们打开我们的`photofeed\urls.py`文件，并用以下内容替换:

```
 """photofeed URL Configuration

    The `urlpatterns` list routes URLs to views. For more information please see:
        https://docs.djangoproject.com/en/1.11/topics/http/urls/
    Examples:
    Function views
        1\. Add an import:  from my_app import views
        2\. Add a URL to urlpatterns:  url(r'^$', views.home, name='home')
    Class-based views
        1\. Add an import:  from other_app.views import Home
        2\. Add a URL to urlpatterns:  url(r'^$', Home.as_view(), name='home')
    Including another URLconf
        1\. Import the include() function: from django.conf.urls import url, include
        2\. Add a URL to urlpatterns:  url(r'^blog/', include('blog.urls'))
    """
    from django.conf.urls import url
    from django.contrib import admin
    from feed.views import * 

    urlpatterns = [
        url(r'^$', index),
        url(r'^push_feed$', push_feed),
        url(r'^pusher_authentication', pusher_authentication),
        url(r'^admin/', admin.site.urls),
    ] 
```

Enter fullscreen mode Exit fullscreen mode

这个文件中有什么变化？我们在文件中添加了两条新路线。

我们已经定义了入口点，并把它分配给了我们的`index`函数。我们还定义了 push_feed URL，并将其分配给我们的`push_feed`函数。这将负责实时向 Pusher 推送更新。最后是`pusher_authentication`端点，它处理我们私有通道的认证。

### 创建 HTML 文件

现在我们需要创建 index.html 文件，我们引用它作为索引函数的模板。

让我们在`feed`文件夹中创建一个名为`templates`的新文件夹。

接下来，我们在`templates`文件夹中创建一个名为`index.html`的文件，并用下面的代码替换它:

```
 <html>
        <head>
            Django Photo feed
            <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
            <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.js"></script>
            <script src="//js.pusher.com/4.0/pusher.min.js"></script>
        </head>
        <body>

            <div class="container">
                <form  method="post" enctype="multipart/form-data" action="/push_feed" onsubmit="return feed_it()">
                <input type="hidden" id="csrf" name="csrf" value="{{ csrf_token }}"/>
                <div class="form-group">
                        <label for="usr">Image:</label>
                        <input type="file" id="document" name="document" class="form-control"  required>
                    </div>
                    <div class="form-group">
                        <label for="pwd">comment:</label>
                        <input type="text" id="description" name="description"  class="form-control"  required>
                    </div>
                    <div class="form-group">
                        <button type="submit" class="btn btn-success">Feed it</button>
                    </div>
                </form>
                <div class="row" id="feeds">
                    {% for doc in all_documents  %}
                    <span>
                        <h2>{{doc.description}}</h2>
                        <img  src="{{doc.document}}">
                    </span>
                    {% endfor %}
                </div>
            </div>
        </body>
    </html> 
```

Enter fullscreen mode Exit fullscreen mode

在这个 HTML 片段中，请注意我们已经包含了一些必需的库，例如:

*   Bootstrap CSS
*   jQuery JavaScript 库
*   Pusher JavaScript 库

### 推送器绑定和 jQuery 代码片段

就是这样！现在，一旦一张照片被上传，它也会被广播，我们可以通过我们的频道实时更新。下面是我们用来处理文件上传和 Pusher 实时更新的 jQuery 代码片段。

```
 <script>
     var files;

        // Add events
        $(document).ready(function() {
            $('input[type=file]').on('change', prepareUpload);
        })

        // Grab the files and set them to our variable
        function prepareUpload(event) {
            files = event.target.files;
        }

        function feed_it() {
            var data = new FormData();
            $.each(files, function(key, value) {
                data.append('document', value);
            });
            data.append('description', document.getElementById('description').value);

            data.append('csrfmiddlewaretoken', document.getElementById('csrf').value);
            $.post({
                url: '/push_feed',
                data: data,
                processData: false, // Don't process the files
                contentType: false, // Set content type to false as jQuery will tell the server it's a query string request
                success: function(data) {
                    if (data == "ok") {
                        alert('done');
                        document.getElementById('description').value = '';
                    }
                },
                error: function(error) {
                    alert('an error occured, please try again later')
                }
            });
            return false;
        }
        var pusher = new Pusher('XXX_APP_KEY', {
            encrypted: true,
            cluster: 'XXX_APP_CLUSTER',
            authTransport: 'jsonp',
            authEndpoint: '/pusher_authentication'
        });
        my_channel.bind("an_event", function(doc) {
            alert("message");
            var new_message = `<span>
                        <h2>` + doc.description + `</h2>
                        <img  src="` + doc.document + `">
                    </span>`;
            $('#feeds').prepend(new_message);
        });
    </script> 
```

Enter fullscreen mode Exit fullscreen mode

下面是我们构建的图像:

[![](img/9aa549b307019504f12a595e10cabdcd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0A_sNzfm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.pusher.com/wp-content/uploads/2017/06/build-a-photo-feed-using-django-and-pusher.gif)

## 结论

在本文中，我们介绍了如何使用 Django 和 Pusher 创建实时照片提要，以及如何使用 Django 在 AJAX 请求中传递 CSRF 令牌。本教程的代码库可以在[的公共 Github 库](https://github.com/samuelayo/pusher_django_photo_feed)中找到。可以下载用于教育目的。

有更好的方法可以让我们建立我们的应用程序，预订或评论，让我们在评论中知道。记住分享就是学习。