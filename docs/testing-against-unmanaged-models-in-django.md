# 针对 Django 中的非托管模型进行测试

> 原文：<https://dev.to/vergeev/testing-against-unmanaged-models-in-django>

# 问题

我的 Django 应用程序应该读取一个已经存在的数据库的表。所以数据库的模型是使用`inspectdb`命令生成的。他们长这样:

```
class User(models.Model):
    first_name = models.CharField(max_length=255)
    second_name = models.CharField(max_length=255)
    # other properties 
    class Meta:
        managed = False
        db_table = 'user' 
```

Enter fullscreen mode Exit fullscreen mode

属性`managed = False`告诉 Django 不要在`migrate`命令中创建表格，也不要在`flush`之后删除它。这里的问题是 Django [在测试期间也不会创建表](https://docs.djangoproject.com/en/1.11/ref/models/options/#managed)。因此，当我想针对非生产数据库测试我的代码时，我遇到了以下问题:

1.  仅仅为了运行测试，就需要手工为非托管模型创建所有的表。否则我会变得`django.db.utils.OperationalError: no such table: user`。
2.  对我来说，如何为非托管模型生成 fixtures 并不明显。如果模型是托管的，您可以在测试数据库上运行迁移，在`shell`命令的帮助下填充表格，并用`dumpdata`保存它。但是非托管模型的表不是在迁移过程中创建的，没有什么需要填充的。同样，我不得不手工创造一切。

我的目标是找到一种自动化过程的方法，使测试和协作更加容易。相关的点点滴滴散落在互联网上。这是一次将它们集中在一个地方的尝试。

# 简单的解决方法

在测试运行程序启动测试之前，它运行迁移，为托管模型创建表。迁移本身是创建模型和设置`managed`属性的 Python 代码。可以修改一个迁移，以便在我们测试时将`managed`设置为`True`，否则为非托管模型设置为`False`。为此，我们将在`settings.py`中创建`IS_TESTING`变量，并在测试时将其设置为`True`。我们还将修改迁移代码:

```
from django.conf import settings

# ... operations = [
    migrations.CreateModel(
        name='User',
        fields=[
            # ...
        ],
        options={
            'db_table': 'user',
            # 'managed': False,
            'managed': settings.IS_TESTING,
        },
    ),
] 
```

Enter fullscreen mode Exit fullscreen mode

现在，每当使用`IS_TESTING = True`运行迁移时，都会创建该表。
这个想法属于凯尔·瓦拉德，他在他的博客中描述了它[。
要生成夹具，可使用之前描述的`shell`命令。这里的缺点是你必须记住修改每个非托管模型的迁移。](https://kdazzle.svbtle.com/testing-with-unmanaged-databases-in-django)

# 创建一个定制的测试运行程序

一个更复杂的解决方案是创建一个定制的测试运行器，在运行测试之前将所有非托管模型转换为托管模型，并在之后恢复其效果。
我们将把跑步者放在`appname/utils.py` :

```
from django.test.runner import DiscoverRunner

class UnManagedModelTestRunner(DiscoverRunner):

    def setup_test_environment(self, *args, **kwargs):
        from django.apps import apps
        get_models = apps.get_models
        self.unmanaged_models = [m for m in get_models() if not m._meta.managed]
        for m in self.unmanaged_models:
            m._meta.managed = True
        super(UnManagedModelTestRunner, self).setup_test_environment(*args, **kwargs)

    def teardown_test_environment(self, *args, **kwargs):
        super(UnManagedModelTestRunner, self).teardown_test_environment(*args, **kwargs)
        for m in self.unmanaged_models:
            m._meta.managed = False 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将告诉 Django 通过在我们的设置中添加`TEST_RUNNER = 'app_name.utils.UnManagedModelTestRunner'`来使用这个 runner。
我们还没有准备好，因为`User`型号[有自定义表名](https://stackoverflow.com/questions/18085245/running-tests-with-unmanaged-tables-in-django) `user`。这就是为什么我们需要在不运行迁移的情况下创建测试表。有一个小应用程序可以做到这一点。它是通过运行`pip install`并将其添加到`INSTALLED_APPS`来安装的。如果我们使用`-n`开关:`python manage.py test -n`运行测试，我们的测试将会成功。因此，我们将失去查看是否有迁移中断的能力(如果所有迁移都是由 Django 生成的，这可能是好的)。

创建定制测试跑步者的想法属于托拜厄斯·麦纽提，他在 Caktus Group 博客上发布了这个想法。他的帖子的代码不得不被[更新](https://stackoverflow.com/a/36234846/3694363)。

当涉及到维护代码时，会有一些复杂的问题。首先，如果我们想使用其他的测试运行程序，我们必须继承它:

```
from django.test.runner import DiscoverRunner
from django_nose import NoseTestSuiteRunner

class UnManagedModelTestRunner(NoseTestSuiteRunner, DiscoverRunner):
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

第二，即使`django-test-without-migrations` app 很简单，它[也不代表](https://github.com/henriquebastos/django-test-without-migrations/issues/13)它不能被新版本的 Django 破解，所以我们需要做好排除故障的准备。
第三，我们必须以一种不寻常的方式生成夹具。我们的非托管模型的表只在`setUp()`方法中可用，所以为了生成 fixtures，我们必须在测试的源代码中添加和转储数据

```
import sys

from django.core.management import call_command

# ... 
    def setUp(self):
        models.User.objects.create(
            # ...
        )
        sysout = sys.stdout
        sys.stdout = open('fixtures/users.json', 'w')
        call_command('dumpdata', app_label='app_name')
        sys.stdout = sysout 
```

Enter fullscreen mode Exit fullscreen mode

在我们运行代码之后，我们可以移除它，并以通常的方式在我们的测试中[加载 fixture。](https://stackoverflow.com/questions/2470634/loading-fixtures-in-django-unit-tests)

# 共同的弱点

当测试运行时，它们会将非托管模型视为托管模型。如果有人无意中向非托管模型添加了一个字段，它们不会失败。我所知道的解决这个问题的唯一方法是手工创建表格。

# 最终字

这是我对这个话题的全部了解。如果你碰巧知道另一个解决方案，我很乐意了解一下。