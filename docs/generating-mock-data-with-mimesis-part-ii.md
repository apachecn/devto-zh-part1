# 用模仿生成模拟数据:第二部分

> 原文：<https://dev.to/likid_geimfari/generating-mock-data-with-mimesis-part-ii>

[![Mimesis](img/ef6027c29786d11b310037dad224d98e.png)T2】](https://github.com/lk-geimfari/mimesis)

我们已经[发布了](https://dev.to/likid_geimfari/generating-mock-data-using-mimesis-part-i)如何在 Python 库的帮助下生成模拟数据——[模拟](https://github.com/lk-geimfari/mimesis)。你现在读的这篇文章是前一篇文章的延续，因此，我们不会再复习基础知识。如果您错过了第一篇文章，或者您当时觉得很懒，那么您现在可能想回去看看，因为本文假设您对这个库很熟悉。在这里，我们将讨论该库的最佳实践和许多最有用的特性。

## 注

首先，我们要指出的是，模仿并不是为了与某个数据库或 ORM 一起使用而开发的。该库解决的主要问题是生成有效数据。因此，虽然使用这个库没有严格的规则，但是这里有一些建议可以帮助您保持测试环境的有序，并避免项目中熵的增长。建议非常简单，并且完全符合 Python 精神(如果您不同意，请随时告诉我们)。

尽管前面提到了这个库不能用于特定的数据库或 ORM，但是对测试数据的需求通常出现在对数据库执行特定操作(主要是 CRUD)的 web 应用程序中。我们有一些关于为网络应用组织测试数据生成的建议。
负责数据生成并将其导入数据库的函数应该靠近模型，或者更好地作为与它们相关的模型的统计方法，就像上一篇文章中的`_bootstrap()`方法的例子一样。当模型结构改变，需要添加新的字段时，这对于避免运行文件是必要的。上一篇文章中的模型`Patient()`说明了这个想法:

```
class Patient(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(120), unique=True)
    phone_number = db.Column(db.String(25))
    full_name = db.Column(db.String(100))
    weight = db.Column(db.String(64))
    height = db.Column(db.String(64))
    blood_type = db.Column(db.String(64))
    age = db.Column(db.Integer)

    def __init__(self, **kwargs):
        super(Patient, self).__init__(**kwargs)

    @staticmethod
    def _bootstrap(count=500, locale='en', gender):
        from mimesis import Personal
        person = Personal(locale)

        for _ in range(count):
            patient = Patient(
                email=person.email(),
                phone_number=person.telephone(),
                full_name=person.full_name(gender=gender),
                age=person.age(minimum=18, maximum=45),
                weight=person.weight(),
                height=person.height(),
                blood_type=person.blood_type()
            )

            db.session.add(patient)
            try:
                db.session.commit()
            except IntegrityError:
                db.session.rollback() 
```

Enter fullscreen mode Exit fullscreen mode

记住，上面的例子是一个由`SQLAlchemy`使用的 Flask-app 的模型。使用不同的框架为这样的应用程序组织模拟数据生成器也是以同样的方式完成的。

## 创建对象

如果你的应用程序需要一种特定语言的数据，最好使用 class `Generic()`，通过一个对象而不是多个独立的类提供者来访问所有的类提供者。使用`Generic()`可以让你去掉几行多余的代码。

正确:

```
>>> from mimesis import Generic
>>> generic = Generic('ru')

>>> generic.personal.username()
'sherley3354'

>>> generic.datetime.date()
'14-05-2007' 
```

Enter fullscreen mode Exit fullscreen mode

不正确:

```
>>> from mimesis import Personal, Datetime, Text, Code

>>> personal = Personal('ru')
>>> datetime = Datetime('ru')
>>> text = Text('ru')
>>> code = Code('ru') 
```

Enter fullscreen mode Exit fullscreen mode

仍然正确:

```
>>> from mimesis import Personal

>>> p_en = Personal('en')
>>> p_sv = Personal('sv')
>>> # … 
```

Enter fullscreen mode Exit fullscreen mode

这意味着单独导入类提供者只有在您限制自己只能通过导入的类获得数据时才有意义，否则最好使用`Generic()`。

## 将数据插入数据库

如果您需要生成数据并将其导入数据库，我们强烈建议分块生成数据，而不是一次生成。记住数据库、ORM 等可能的限制。生成的数据块越小，处理速度就越快。

好:

```
>>> Patient()._bootstrap(count=2000, locale='de') 
```

Enter fullscreen mode Exit fullscreen mode

非常糟糕:

```
>>> Patient()._bootstrap(count=600000, locale='de') 
```

Enter fullscreen mode Exit fullscreen mode

## 导入图像

类`Internet()`拥有几个生成图像链接的方法(更多细节在这里)。链接到位于远程服务器上的图像就足够了，但是，如果您仍然想在本地拥有大量的随机图像，您可以从模型实用程序:
下载由各自的类`Internet()`方法在函数`download_image()`的帮助下生成的图像

```
>>> from mimesis import Internet
>>> from mimesis.utils import download_image

>>> net = Internet()

>>> img_url = net.stock_image(category='food', width=1920, height=1080)
>>> download_image(url=img_url, save_path='/some/path/') 
```

Enter fullscreen mode Exit fullscreen mode

## 用户提供商

该库支持大量的数据，在大多数情况下这就足够了。对于那些想用更具体的数据创建自己的提供者的人。可以这样做:

```
>>> class SomeProvider():
...     class Meta:
...         name = "some_provider"
...
...     @staticmethod
...     def one():
...         return 1

>>> class Another():
...     @staticmethod
...     def bye():
...         return "Bye!"

>>> generic.add_provider(SomeProvider)
>>> generic.add_provider(Another)

>>> generic.some_provider.one()
1

>>> generic.another.bye()
'Bye!' 
```

Enter fullscreen mode Exit fullscreen mode

您也可以添加多个提供商:

```
>>> generic.add_providers(SomeProvider, Another)
>>> generic.some_provider.one()
1
>>> generic.another.bye()
'Bye!' 
```

Enter fullscreen mode Exit fullscreen mode

这里的一切都很简单，不言自明，因此，我们将只澄清一点——attribute`name`，class `Meta`是一个类的名称，通过它可以访问用户类提供者的方法。默认情况下，类名是低位寄存器中的类名。

## 内置提供者

大多数国家只有一种官方语言，只有这些国家的典型数据。例如，`CPF`代表巴西(`pt-br`)，`SSN`代表美国(`en`)。这种数据可能会引起不适，并干扰顺序(或者至少令人烦恼),因为它们存在于所有对象中，而不管所选择的语言标准如何。您可以通过查看示例(代码不会运行)来了解这一点:

```
>>> from mimesis import Personal
>>> person = Personal('en')

>>> person.ssn()
>>> person.cpf() 
```

Enter fullscreen mode Exit fullscreen mode

我们打赌每个人都会同意这看起来不太好。像我们这样的完美主义者已经注意到了这一点，一些特定的地区提供商不会为其他地区的其他提供商而烦恼。出于这个原因，具有本地特定数据的类提供者被分离到一个特殊的子包(`mimesis.builtins`)中，用于为所有语言及其对象保持一个公共的类结构。

它是这样工作的:

```
>>> from mimesis import Generic
>>> from mimesis.builtins import BrazilSpecProvider

>>> generic = Generic('pt-br')
>>> generic.add_provider(BrazilProvider)
>>> generic.brazil_provider.cpf()
'696.441.186-00' 
```

Enter fullscreen mode Exit fullscreen mode

如果您想更改内置提供者的默认名称，只需更改内置提供者的属性`name`，类`Meta`的值:

```
>>> BrazilSpecProvider.Meta.name = 'brasil'
>>> generic.add_provider(BrazilSpecProvider)
>>> generic.brasil.cpf()
'019.775.929-70' 
```

Enter fullscreen mode Exit fullscreen mode

或者只是继承这个类并覆盖提供者的类`Meta`的属性`name`的值(在我们的例子中是`BrazilSpecProvider()` ) :

```
>>> class Brasil(BrazilSpecProvider):
...
...     class Meta:
...         name = "brasil"
...
>>> generic.add_provider(Brasil)
>>> generic.brasil.cnpj()
'55.806.487/7994-45' 
```

Enter fullscreen mode Exit fullscreen mode

通常，您不需要向对象`Generic()`添加内置类。示例中这样做的唯一目的是演示在哪些情况下应该向对象`Generic()`添加内置的类提供者。可以直接使用，如下图:

```
>>> from mimesis.builtins import RussiaSpecProvider
>>> ru = RussiaSpecProvider()

>>> ru.patronymic(gender='female')
'Петровна'

>>> ru.patronymic(gender='male')
'Бенедиктович' 
```

Enter fullscreen mode Exit fullscreen mode

## 按模式生成数据

模仿支持从版本`0.0.5`通过模式生成数据。该功能仍处于早期开发阶段。其实是很简单的功能，下面介绍一下它的工作原理:

```
>>> from mimesis.schema import Schema
>>> schema = Schema('en')

>>> schema.load(schema={
...     "id": "cryptographic.uuid",
...     "name": "text.word",
...     "version": "development.version",
...     "owner": {
...         "email": "personal.email",
...         "token": "cryptographic.token",
...         "creator": "personal.full_name"
...     }
... }).create(iterations=2)

>>> # or you can load data from json file: >>> schema.load(path='schema.json').create(iterations=2) 
```

Enter fullscreen mode Exit fullscreen mode

结果:

```
[  {  "id":  "790cce21-5f75-2652-2ee2-f9d90a26c43d",  "name":  "container",  "owner":  {  "email":  "anjelica8481@outlook.com",  "token":  "0bf924125640c46aad2a860f40ec4b7f33a516c497957abd70375c548ed56978",  "creator":  "Ileen Ellis"  },  "version":  "4.11.6"  },  ...  ] 
```

Enter fullscreen mode Exit fullscreen mode

你在工作中通常需要哪种类型的数据？图书馆缺什么？我们将很高兴听到您的建议和意见。

## 有用的链接

链接到文章的第[部分。](https://dev.to/likid_geimfari/generating-mock-data-using-mimesis-part-i)

链接到[模拟](https://github.com/lk-geimfari/mimesis)的[库](https://github.com/lk-geimfari/mimesis)

## 后手稿

一篇由 [@likid_geimfari](https://dev.to/likid_geimfari) 撰写并由 [@wemake-services](https://medium.com/wemake-services) 翻译成英文的文章。