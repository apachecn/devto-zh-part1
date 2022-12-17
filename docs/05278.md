# 把一个 Webapp 放到网上最简单的方法(Flask + Heroku + Postgres)

> 原文：<https://dev.to/paultopia/the-easiest-possible-way-to-throw-a-webapp-online-flask--heroku--postgres-185o>

这里有一个经常出现的情况。我想创建一个快速的 CRUD 应用程序，或者，对我来说，更像是一个 C 应用程序(例如，“嘿，研究助理，把所有这些东西都转储到数据库中”)。我可以使用谷歌表单之类的东西，但这总是会导致奇怪的输出，比如奇怪的格式，出现在谷歌表单中。

所以我开始研究 Heroku 上的 Flask，有几个原因:

1.  没有数据库连接之类的争论。如果使用 flask-heroku 库，甚至不需要为代码提供连接 postgres 的环境变量。
2.  做出改变实际上就是按下 git 遥控器。
3.  你可以通过`heroku pg:psql`、*或*与真正的数据库外壳交互，你可以编写固定的查询并在线查看，或者你甚至可以将它们同步到 google sheets(尽管最后一个看起来有点不可靠)。
4.  日志就像`heroku logs`一样简单，从应用程序中记录一些东西就像`print()`。
5.  没有 ngnix 和其他服务器配置的东西混乱。
6.  至少从 herokuapp.com 发出的申请来看，HTTPS 也是可以处理的。很明显，这很好。
7.  顺便说一句，你可以免费获得一个“睡着了很多，但对小项目来说足够好”的+ 10k postgres 行。

因此，让我们来完成最简单的设置。

## 第一步:设置并安装所有的东西。

(安装细节将取决于您的平台，所以我将留给您去谷歌。)

1.  获得一个 Heroku 帐户并[安装 heroku cli](https://devcenter.heroku.com/articles/heroku-cli) 。
2.  确保所有代码都在 git repo 中，因为我们将使用 git 来推送 Heroku。
3.  在本地安装 postgres，这样你就可以使用 Heroku psql，从 Heroku 下载到本地数据库，等等。
4.  建立一个 python 虚拟环境。无论如何，这通常是一个好主意，但是在这里特别重要，因为您将使用`pip freeze`来获得对 Heroku 的需求，所以您想要确保您有一个自包含的和可复制的环境。

我是 anaconda 的忠实粉丝，所以我只使用 [conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) 来为我处理这个问题，但是其他虚拟环境管理器也应该工作得很好。

顺便提一下，下面的代码假设 Python 3 的最新版本。它可能在 Python 2 上也能很好地工作，但是谁知道呢？

1.  pip-安装下列库。

**烧瓶** —当然，这是基于烧瓶的教程。这是用 Python 实现基本 web 内容的最简单的方法。

这是一个非常简单的库，负责获取 Heroku 环境变量，并以正确的方式将它们传递给 Flask 应用程序。严格来说没有必要，但为什么要让自己的日子更难过呢？

**SQLAlchemy**—Python 中连接关系数据库的标准库。这真的很复杂，但在只刷绝对表面的同时，在下一个库的帮助下，它似乎对我来说很好。

值得注意的是，与 Python 的禅相反，SQLAlchemy 似乎有一百万种不同的方法来做任何事情，因此不同的教程和文档可能对表创建语法等有稍微不同的方法。

**Flask-SQLAlchemy** —简化 SQLAlchemy API 以用于 Flask。

**Psycopg2** — Postgres 数据库驱动。

仅仅是服务器和你的代码之间的一个通信层，在只有几个用户的快速和肮脏的黑客应用中可能不是严格强制的，但是不会有伤害，并且可以帮助管理一次发生的大量请求。更多信息，请参见[对 WSGI](https://www.fullstackpython.com/wsgi-servers.html) 的解释，以及[的真实报道](https://ironboundsoftware.com/blog/2016/06/27/faster-flask-need-gunicorn/)。

## 第二步:写一些代码

我们来做一个基础的 app 好吗？出于最小示例的目的，让我们将所有这些都放在一个名为 app.py 的文件中。

### A .基础知识:

```
from flask import Flask, render_template, url_for
from flask_sqlalchemy import SQLAlchemy
import sys
import json
from flask_heroku import Heroku
app = Flask( __name__ )
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
heroku = Heroku(app)
db = SQLAlchemy(app) 
```

Enter fullscreen mode Exit fullscreen mode

这里的大部分工作只是在全局名称空间中创建对象，这些对象将保存您的应用程序和数据库。

`Heroku(app)`行只是[把你的环境变量放到它们需要的地方](https://github.com/kennethreitz/flask-heroku)。

`app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False`行是为了[修复一个糟糕的默认配置带来的性能问题(并消除一个恼人的警告)](https://stackoverflow.com/questions/33738467/how-do-i-know-if-i-can-disable-sqlalchemy-track-modifications/33790196#33790196)。

剩下的应该很清楚了。

### B .建立数据库

```
class Dataentry(db.Model):
    __tablename__ = "dataentry"
    id = db.Column(db.Integer, primary_key=True)
    mydata = db.Column(db.Text())

    def __init__ (self, mydata):
        self.mydata = mydata 
```

Enter fullscreen mode Exit fullscreen mode

很简单。您创建一个继承自 db 对象引入的模型类的类，然后将表模式放入该类的字段中。

SQLAlchemy 将[很乐意为您自动增加一个整数主键](http://docs.sqlalchemy.org/en/rel_1_1/core/metadata.html#sqlalchemy.schema.Column.params.autoincrement)。

无论您想要什么列，您都可以声明并传递它们的类型。SQLAlchemy 有[一堆可用的类型](http://docs.sqlalchemy.org/en/latest/core/types.html)，包括所有的基本类型，还包括特定于数据库供应商的类型、不同风格的日期时间等。

然后，对象的构造函数([好吧，实际上是初始化函数](http://spyhce.com/blog/understanding-new-and-init))只是根据从用户那里收到的数据来设置属性。

嘿，也许我们应该得到一些数据？

### C .设置接收数据的路线

我在这里假设我们只是使用一个标准的 HTML 表单来生成 post 请求，所以让我们设置一个路由来接收它。

```
@app.route("/submit", methods=["POST"])
def post_to_db():
    indata = Dataentry(request.form['mydata'])
    data = copy(indata. __dict__ )
    del data["_sa_instance_state"]
    try:
        db.session.add(indata)
        db.session.commit()
    except Exception as e:
        print("\n FAILED entry: {}\n".format(json.dumps(data)))
        print(e)
        sys.stdout.flush()
    return 'Success! To enter more data, <a href="{}">click here!</a>'.format(url_for("enter_data")) 
```

Enter fullscreen mode Exit fullscreen mode

好的，这个稍微复杂一点。

第一行只是一个装饰器，它告诉 Flask 下面的函数服务于一个路由，给它提供服务的路由(参见 Flask 文档中所有关于路由的有趣内容)，并告诉它接受什么方法。

该函数初始化我们在上面创建的对象，并将我们在表单上收到的数据传递给它。

注意这是作为一个`request`对象上的字段，它实际上是一个全局的。这是一个 Flask 的东西，我认为这是一个真正的 WTF 设计决策——Flask 应该让你把它作为一个参数传递给函数，但是 Flask 的人选择了让它成为一个全局的，所以它是一个全局的。

然后我在这里有一些真正的凭直觉的错误处理。Heroku 不允许您将数据保存到文件系统中——您可以选择数据库或什么都不做。但是它有内置的日志记录功能(虽然它只能保存大约 1500 行日志，除非您花钱从别人那里购买服务来保存更多的日志)，而且它使获取日志变得非常容易:应用程序保存到 stdout 的任何内容都会记录到一个日志中。

由于数据库写入可能很挑剔，所以我只是捕捉所有错误并记录它们，以及它试图写入的所有数据。(由于用户不需要知道数据库写失败，我告诉他们继续写。)

一些稍微晦涩的机制:

*   `indata. __dict__`是包含对象所有属性的字典。它也将包含 SQLAlchemy 添加的内容，其中一些不可序列化为 JSON，所以我只是出于记录的目的删除了它。
*   `sys.stdout.flush()`是可取的[只是为了确保当你认为它会](https://stackoverflow.com/questions/10019456/usage-of-sys-stdout-flush-method)时，所有东西都会在 stdout 中着陆。

return 发送一些 html 来显示给用户。在这里，我只是使用了一个原始字符串，以便向用户提供一些反馈来确认，是的，他们确实提交了表单，然后提示他们提交更多的数据。

注意 Flask `url_for`函数可以取另一个路由对应的函数，然后智能地在那里插入 url。因此，这将插入对应于 enter_data 函数的路由的 url，我们可能应该这样写...

### C .为用户输入数据设置路线

```
@app.route("/")
def enter_data(): 
    return render_template("dataentry.html") 
```

Enter fullscreen mode Exit fullscreen mode

那很容易，不是吗？这将向用户呈现一个 [Jinja2](http://jinja.pocoo.org/docs/2.9/) 模板。这显然应该为他们提供一个添加数据的表单。这里有一个最小模板的例子:

```
<html>
    <head>
        data entry
    </head>
    <body>
        <form method="POST" action="{{ url_for('post_to_db') }}">
            <label for="mydata">Gimme your data, fool!</label>
            <input type="text" id="mydata" name="mydata">
            <button type="submit">IT FEEDS IT THE DATA</button>
        </form>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这没什么好奇怪的，除了一个例外:注意 Flask 很友好地将`url_for`函数注入到模板中，所以您可以将您的视图与您对 routes 所做的任何事情以及您运行的任何服务器等等分离开来。

### 婷完成它

```
if __name__ == ' __main__':
    #app.debug = True
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

从命令行调用时，运行应用程序。

我把注释掉的那一行放在那里，以展示 Flask 的另一个真正可爱的地方:它有一个*令人惊奇的*调试器；如果你在调试模式下运行它，那么当事情发生时，你将能够直接从它生成的网页上检查状态。显然不要在制作中使用这个，除非你*想要*让恶意演员的日子好过。

就是这样，这就是我们需要的最小应用程序的所有代码！

## 第三步:heroku 的设置

你需要一个 requirements.txt 文件来告诉 Heroku 你需要什么库。

`pip freeze > requirements.txt`

那很容易。您还需要一个 Procfile 来告诉 Heroku 运行什么:

`echo "web: gunicorn app:app" > Procfile`

有一个. gitignore 很好，尤其是如果你还打算把它放在 github 上。这是我最少的 Mac 用户。gitignore:

```
*.bak
*.pyc
.DS_Store 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:部署！

现在你需要提交所有这些东西，一旦都提交了，就只需要`heroku create CHOOSEYOURNAME`。这将使用您在最后一个参数中给出的名称创建一个应用程序。然后`git push heroku master`会在 Heroku 上得到。

真的就这么简单！顺便说一句，heroku create 命令的输出应该给出你的应用程序的 url，可能类似于 chooseyourname.herokuapp.com。

然而，还有一个步骤，那就是设置数据库。两个子步骤:

*   在 Heroku 中创建数据库。免费版是爱好开发版(这里有 10，000 行的限制)。其他的其实都挺贵的。就这么简单`heroku addons:create heroku-postgresql:hobby-dev`

(如果你有不止一个数据库，你将不得不[做更多的工作](https://devcenter.heroku.com/articles/heroku-postgresql#establish-primary-db)来连接它，但是对于一件简单的事情，你不应该有麻烦。)

*   在新数据库中创建表。最简单的方法是在 heroku 服务器上启动一个 Python REPL，然后[在应用程序内创建表格](http://flask-sqlalchemy.pocoo.org/2.1/api/#flask.ext.sqlalchemy.SQLAlchemy.create_all) : `heroku run python`从命令行，然后在 REPL、`from app import db`和`db.create_all()`。

**就是这样！你完了！**如果一切顺利，该应用程序应该是活的和正常运行的。

## 现在怎么办？

*   如果您想更新应用程序中的(非数据库)代码，只需将新的更改推送到 Heroku remote 即可。
*   要查看日志，请转到 repo 并执行`heroku logs`。
*   要获得数据库外壳，请执行`heroku pg:psql`。
*   要在线查看数据，最简单的方法可能是使用 [dataclips](https://devcenter.heroku.com/articles/dataclips) 来查看保存的查询。
*   要在本地获取数据，您可以使用`heroku pg:pull`在本地下拉数据。请参阅下面链接的 Heroku Postgres 文档，了解关于该命令的更多详细信息。

## 等一下，本地开发、测试等呢？？

我不打算涵盖所有这些东西，因为这篇文章太长了，但建立一个实际连接的本地 Postgres 可能是一个好主意，这样您可以在部署之前进行测试。有关更多信息，请参见下面的参考资料。

有些人为应用程序的本地版本和 Heroku 版本创建单独的 git 分支。

## 有用的参考文献

*   [【heroku python 指南】](https://devcenter.heroku.com/articles/getting-started-with-python#introduction)
*   Heroku Postgres 指南
*   一个非常好的教程，但是在 Heroku 的东西上有点过时了——也从使用本地 postgres 安装进行测试开始，这可能是你想要做的。
*   [探索烧瓶](http://exploreflask.com/en/latest/deployment.html)
*   [一个漂亮的烧瓶+ Postgres 教程，包含大量 SQLAlchemy 故障排除信息](https://www.theodo.fr/blog/2017/03/developping-a-flask-web-app-with-a-postresql-database-making-all-the-possible-errors/)
*   详细但过时的 Heroku/Flask/Postgres 教程，带迁移！
*   [让 Postgres 使用 python web 框架的简单易懂的解释](http://killtheyak.com/use-postgresql-with-django-flask/)
*   [烧瓶大教程](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world)
*   [官方烧瓶教程](http://flask.pocoo.org/docs/0.12/tutorial/)