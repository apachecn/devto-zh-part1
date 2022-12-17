# 使用 Flask 用 15 行代码为您的 python 脚本创建 RestAPI😎

> 原文：<https://dev.to/aaahmedaa/create-restapi-for-your-python-scripts-using-flask-with-15-line-of-code-10ml>

## 创建用于保存工作的 virtualenv

-如果您没有安装 virtualenv，请使用`pip install virtualenv`
-执行以下操作:-
`mkdir flask-safe-env`
`virtualenv .`
`Scripts/activate`如果您在执行激活脚本时遇到任何问题，请检查此[链接](https://virtualenv.pypa.io/en/stable/userguide/#admonition%20note)
`mkdir rest-api`
`cd rest-api`
-使用
`pip install flask`
`pip install flask-restful`
安装此应用程序所需的软件包，或者如果您运行了 github repo】

## 创建项目文件

我们可以在一个脚本中创建所有的应用程序，但为了增加项目的可靠性，我们将在另一个名为 sumTwoNumbers.py 的脚本中创建 sum 函数，并将其导入 app.py 脚本中以使用它。

#### sumtwodumbers . py

很简单，创建一个接受两个参数的函数，返回两个数的和

```
def sumTwo(a, b):
    return int(a)+int(b) 
```

Enter fullscreen mode Exit fullscreen mode

#### app.py

我们创建 flask 应用程序和 api 模块的实例，然后使用 GET 请求在 url 中添加接受两个参数的路由，我们将从 sumTwoNumbers 脚本中调用函数

```
import sumTwoNumbers
from flask import Flask, request
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

class sumNumbers(Resource):
    def get(self, first_number, second_number):
        return {'data': sumTwoNumbers.sumTwo(first_number,second_number)}

api.add_resource(sumNumbers, '/sumtwonumbers/<first_number>/<second_number>')

if __name__ == '__main__':
     app.run() 
```

Enter fullscreen mode Exit fullscreen mode

-要运行应用程序，请执行命令`python app.py`
-转到[http://localhost:5000/sumtwo numbers/10/60](http://localhost:5000/sumtwonumbers/10/60)您可以用您想要的数字替换这些数字

你可以在这里找到项目文件: [Sweet-github](https://github.com/aa-ahmed-aa/Flask-RESTAPI)