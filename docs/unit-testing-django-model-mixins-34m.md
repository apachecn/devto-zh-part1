# 单元测试 Django 模型混合

> 原文：<https://dev.to/michaelmior/unit-testing-django-model-mixins-34m>

我最近发现自己必须对一些模型混合进行单元测试，我想我会分享我所使用的技术，以防其他人发现它有用。您可以选择一个使用 mixin 的模型，并在该模型的实例上运行测试。但是 mixin 的目标是提供独立于任何模型的可重用功能。相反，我们创建了一个虚拟模型用于测试。

模型不应该驻留在`models.py`中，因为我们不希望它出现在我们的数据库中。相反，我们动态地创建模型。然而，我想测试一些需要将模型保存到数据库中的功能。幸运的是，Django 可以构造必要的 SQL 来创建和销毁数据库表。我们简单地忽略设置和拆卸来完成繁重的工作。

```
from django.test import TestCase
from django.db import connection
from django.core.management.color import no_style
from django.db.models.base import ModelBase

class ModelMixinTestCase(TestCase):
    """
    Base class for tests of model mixins. To use, subclass and specify
    the mixin class variable. A model using the mixin will be made
    available in self.model.
    """

    def setUp(self):
        # Create a dummy model which extends the mixin
        self.model = ModelBase(' __TestModel__'+self.mixin. __name__ , (self.mixin,),
            { ' __module__': self.mixin. __module__ })

        # Create the schema for our test model
        self._style = no_style()
        sql, _ = connection.creation.sql_create_model(self.model, self._style)

        self._cursor = connection.cursor()
        for statement in sql:
            self._cursor.execute(statement)

    def tearDown(self):
        # Delete the schema for the test model
        sql = connection.creation.sql_destroy_model(self.model, (), self._style)
        for statement in sql:
            self._cursor.execute(statement) 
```

Enter fullscreen mode Exit fullscreen mode

要使用这段代码，只需从`ModelMixinTestCase`子类化并将 mixin 类变量设置为您想要测试的模型 mixin 类。然后你就可以通过`self.model`访问使用这个 mixin 的全功能模型。测试愉快！