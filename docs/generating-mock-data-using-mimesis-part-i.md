# 使用模仿生成模拟数据:第一部分

> 原文：<https://dev.to/likid_geimfari/generating-mock-data-using-mimesis-part-i>

[![Mimesis](img/4e2ce8b4dcd8f7d9f3aa31e328cb9cfd.png)T2】](https://github.com/lk-geimfari/mimesis)

在需要使用数据库的应用程序开发中，生成模拟但有效的数据的能力非常有用。手动填充数据库是一个耗时且繁琐的过程，可以分三个阶段完成——收集必要的信息、对数据进行后处理以及对数据生成器本身进行编码。当你不仅需要生成 10-15 个用户，还需要生成 10-15 万个用户(或其他类型的数据)时，事情就变得非常复杂了。在本文以及接下来的两篇文章中，我们将向您介绍一个工具，它极大地简化了生成模拟数据、初始数据库加载和一般测试。

[**Mimesis**](https://github.com/lk-geimfari/mimesis) 是一个 Python 库，它帮助生成各种用途的模拟数据。该库是使用标准 Python 库中的工具编写的，因此，它没有任何从属关系。目前该库支持 32 种语言和 21 个类提供者，提供各种数据。

### 安装

安装模拟的正常方式是通过 pip:

```
âžœ  ~ pip install mimesis 
```

Enter fullscreen mode Exit fullscreen mode

如果由于某种原因，您无法在 pip 的帮助下安装软件包，请尝试手动安装，如下所示:

```
(venv) âžœ  ~ git clone https://github.com/lk-geimfari/mimesis.git
(venv) âžœ  cd mimesis/
(venv) âžœ  python3 setup.py install
# or
(venv) âžœ make install 
```

Enter fullscreen mode Exit fullscreen mode

### 生成数据

最初，我们计划使用一个小型 web 应用程序 Flask 的例子来展示数据生成，但是我们决定不这样做，因为不是每个人都熟悉 Flask，也不是每个人都愿意改变它。因此，我们将只在 Python 上展示这一点。如果您想将所有内容转移到 Flask 或 Django 上的项目中，您只需定义一个静态方法，该方法将运行与当前模型相关的所有操作，并在需要初始数据库加载时调用它，如下例所示。烧瓶(Flask-SQLAlchemy)的模型看起来像这样:

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

现在让我们转换到 shell 模式:

```
(venv) âžœ python3 manage.py shell 
```

Enter fullscreen mode Exit fullscreen mode

并生成数据。在此之前，我们需要确保数据库和模型是可用的。

```
>>> db
<SQLAlchemy engine='sqlite:///db.sqlite'>

>>> Patient

<class 'app.models.Patient'>

>>> Patient()._bootstrap(count=40000, locale='en') # generate 40Ðº entries in English. 
```

Enter fullscreen mode Exit fullscreen mode

### 简介

值得注意的是，我们将展示这个库的基本功能，我们将使用几个最常见的类提供者，因为它们太多了，无法一一详述。如果这篇文章激发了你对图书馆的兴趣，你可以访问文章末尾列出的有用链接，找到更多信息。

图书馆很简单。开始处理数据所需要做的就是创建一个类提供者。应用程序中最常见的数据类型是个人用户数据，如姓名、信用卡信息等。这种类型的数据有一个特殊的类提供者——`â€ŠPersonal()`,它以如下所示的行的形式从语言标准中获取代码:

```
>>> from mimesis import Personal
>>> person = Personal('is')
>>> for _ in range(0, 3):
...     person.full_name(gender='male')
...
'Karl BrynjÃºlfsson'
'RÃ¶gnvald EiÃ°sson'
'VÃ©steinn RÃ­kharÃ°sson' 
```

Enter fullscreen mode Exit fullscreen mode

几乎每个网络应用程序都需要电子邮件来注册。自然，该库支持借助于`Personal()`类的`email()`方法生成电子邮件的能力，如下:

```
>>> person.email(gender='female')
'lvana6108@gmail.com'

>>> person.email(gender='male')
'john2454@yandex.com' 
```

Enter fullscreen mode Exit fullscreen mode

上面的方法有一个小问题，如果应用程序使用多种类型的类提供者，这可能会导致代码有点“脏”。在这种情况下，您应该使用对象`Generic()`，该对象从一个对象
向所有提供者授予访问权限

```
>>> from mimesis import Generic
>>> g = Generic('pl') # pl – code of Poland (ISO 639-1). >>> g.personal.full_name()
'LonisÅ‚awa PodsiadÅ‚o'
>>> g.datetime.birthday(readable=True)
'Listopad 11, 1997'
>>> g.code.imei()
'011948003071013'
>>> g.food.fruit()
'Cytryna'
>>> g.internet.http_method()
'PUT'
>>> g.science.math_formula()
'A = (h * (a + b)) / 2' 
```

Enter fullscreen mode Exit fullscreen mode

组合数据给你一个广阔的实验领域。例如，您可以创建模拟(女性)Visa (Maestro，MasterCard)信用卡持卡人:

```
>>> user = Personal('en')
>>>
>>> def get_card(sex='female'):
...     owner = {
...       'owner': user.full_name(sex),
...       'exp_date': user.credit_card_expiration_date(maximum=21),
...       'number': user.credit_card_number(card_type='visa')
...       }
...     return owner
>>>
>>> for _ in range(0, 3):
...     get_card()
...
{'exp_date': '02/20', 'owner': 'Laverna Morrison', 'card_number': '4920 3598 2121 3328'}
{'exp_date': '11/19', 'owner': 'Melany Martinez', 'card_number': '4980 9423 5464 1201'}
{'exp_date': '01/19', 'owner': 'Cleora Mcfarland', 'card_number': '4085 8037 5801 9703'} 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，该库支持超过 21 个类提供者，提供所有可能情况下的数据(如果没有，欢迎您的 PR 纠正这种可怕的不公正)。例如，如果您正在开发一个专门用于运输和物流的应用程序，并且您需要生成运输模型，那么您可以通过使用`Transport()`类提供者来轻松地完成这项工作，该类提供者包含与运输相关的数据:

```
>>> from mimesis import Transport
>>> trans = Transport()
>>> for _ in range(0, 5):
...     trans.truck()
...
'Seddon-2537 IM'
'Karrier-7799 UN'
'Minerva-5567 YC'
'Hyundai-2808 XR'
'LIAZ-7174 RM' 
```

Enter fullscreen mode Exit fullscreen mode

或者您可以指明传输掩码型号:

```
>>> for _ in range(0, 5):
...     # Here # (sharp) - placeholder for numbers, @ - for letters ...     trans.truck(model_mask="##@")
...
'Henschel-16G'
'Bean-44D'
'Unic-82S'
'Ford-05Q'
'Kalmar-58C' 
```

Enter fullscreen mode Exit fullscreen mode

测试网络应用程序(博客是一个很好的例子)时，你经常需要生成文本数据(文本、句子、标签等)。).手动输入文本又长又无聊，多亏了一个类提供者`Text()` :
，模仿可以让你避免这种情况

```
>>> from mimesis import Text
>>> text = Text('en')
>>> text.text(quantity=3)

'Language includes means for creating light parallel processes  and their interactions via exchanging asynchronous messages according to the actors’ model. Python supports several programming paradigms, including structural, object-oriented, functional, imperative and aspect-oriented. For instance, some functions that use comparison of examples to choose one calculating option or extracting data points looks similar to an equation.' 
```

Enter fullscreen mode Exit fullscreen mode

可以得到一个随机单词列表:

```
>>> text = Text('pt-br')
>>> text.words(quantity=5)
['poder', 'de', 'maior', 'sÃ³', 'cima'] 
```

Enter fullscreen mode Exit fullscreen mode

生成街道名称:

```
>>> from mimesis import Address
>>> address = Address('en')
>>> address.address()
'77 Shephard Trace' 
```

Enter fullscreen mode Exit fullscreen mode

获取与所选语言相关的州/地区/省的名称。在本例中，它是美国的一个州:

```
>>> address.state()
'Texas' 
```

Enter fullscreen mode Exit fullscreen mode

该库也有罗马化西里尔语言的方法(目前只支持俄语和乌克兰语):

```
>>> from mimesis.decorators import romanized
>>> @romanized('ru')
... def name_ru():
...     return 'Ð’ÐµÑ€Ð¾Ð½Ð¸ÐºÐ° Ð”ÐµÐ½Ð¸ÑÐ¾Ð²Ð°'
...
>>> @romanized('uk')
>>> def name_uk():
...     return 'Ð•Ð¼Ñ–Ð»Ñ–Ñ ÐÐºÑƒÐ»ÐµÐ½ÐºÐ¾'
...
>>> name_ru()
'Veronika Denisova'
>>> name_uk()
'EmÑ–lÑ–ja Akulenko' 
```

Enter fullscreen mode Exit fullscreen mode

事实上，有很多可能性，你可以想出大量很好的用例，其中的数据看起来比我们的例子更有用。我们期待着从我们的用户那里得到它们。我们很乐意了解您如何成功地将该库应用到您的项目中。

## 有用的链接

文章的第二部分。
项目在 [GitHub](https://github.com/lk-geimfari/mimesis) 上可用。
在 [ReadTheDocs](http://mimesis.readthedocs.io/en/latest/) 上提供的文档。