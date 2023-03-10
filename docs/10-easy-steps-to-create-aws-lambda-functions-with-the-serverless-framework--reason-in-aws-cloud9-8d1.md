# 使用 AWS Cloud 中的无服务器框架和 Reason 创建 AWS Lambda 函数的 10 个简单步骤 9

> 原文：<https://dev.to/kayis/10-easy-steps-to-create-aws-lambda-functions-with-the-serverless-framework--reason-in-aws-cloud9-8d1>

*[封面图片由凯尔·詹姆斯在闪烁。](https://www.flickr.com/photos/jameskm03/)T3】*

在阅读了由[丹尼尔·戈兰特](https://twitter.com/dangolant)撰写的[这篇令人敬畏的教程](https://dev.to/dangolant/creating-serverless-functions-with-python-and-aws-lambda-dli)之后，我在 [AWS Lambda](https://aws.amazon.com/de/lambda/) 试了试自己。为了使整个事情比仅仅使用 Node.js 的[无服务器框架](https://serverless.com/)而不是 Python 更有趣，我添加了 [AWS Cloud9](https://aws.amazon.com/cloud9/) 和 [Reason](https://reasonml.github.io/) 。

## 我们在做什么？

有一个新的在线 IDE 叫做 AWS Cloud9。在 EC2 上运行，附带许多开发工具。

AWS Lambda 要老一些，它是一种在云中运行你编写的函数而不干扰服务器的方式。当你有更多的用户时，AWS 就会开始复制它们。

无服务器框架是使 AWS Lambda 更易访问的一种方式，因为使用它的常规方式有点麻烦。

理性是脸书创造的语言。使用 BuckleScript 编译器，它是 OCaml 编译器的后端，Reason 被编译成 JavaScript。它基本上是带有类似 JavaScript 语法的 OCaml。

## 需要做什么？

1.  设置 AWS 帐户
2.  向帐户添加 IAM 用户
3.  向该用户添加 Cloud9 权限和管理权限
4.  使用创建的用户登录 AWS
5.  创建云 9 环境
6.  安装和配置 BuckleScript
7.  安装和配置无服务器
8.  将 JavaScript 代码转换为 Reason
9.  将原因编译成 JavaScript
10.  将功能部署到 AWS Lambda

## 如何

### 1。设置 AWS 帐户

首先，我们需要一个 AWS 帐户来访问我们想要使用的服务。

您可以在处创建一个

如你所见，包含了 12 个月的免费使用。只要你使用免费等级的服务，你就可以使用。

### 2。向帐户添加 IAM 用户

下一步是设置一个 IAM 用户，这样你就可以不使用你的根帐户来访问服务。

为此，您首先需要登录到 [AWS 管理控制台](https://console.aws.amazon.com/console/home)

左上角是一个*服务*菜单，在这里你可以从*安全、身份&合规*类别中选择 *IAM* 。

点击左侧的*用户*并选择*添加用户*。

给它一个你能记住的名字，最好是小写的，也给它一个*程序化访问*，这个名字以后会被无服务器框架使用。另外 *AWS 管理控制台访问*，这样你就可以用密码登录来创建你的 Cloud9 环境。

然后点击*下一步:权限*

### 3。向该用户添加 Cloud9 权限和管理权限

选择*直接附加现有策略*。

在这里，您必须搜索管理员访问权限和 T2 用户权限并检查它们。

第一个是无服务器框架需要的，第二个，你猜对了，是 Cloud9 需要的。

现在你可以去*查看*检查是否一切正常，然后*完成*。

如果您使用了自动生成的密码，该密码将显示在*完成*屏幕中，您必须**保存该密码以便登录**。

*关闭*该向导，您现在应该会看到一个用户列表。单击新用户的用户名。

点击*选项卡中的*安全凭证，这给你一个*控制台登录链接*，保存它。

然后签出。

### 4。使用创建的用户登录 AWS

现在尝试使用您保存的*控制台登录链接*登录 *AWS 管理控制台*。

它应该是这样的:

```
https://<NUMBER>.signin.aws.amazon.com/console 
```

Enter fullscreen mode Exit fullscreen mode

输入新用户的用户名和密码并登录。

### 5。创建云 9 环境

打开左上角的*服务*菜单，在*开发者工具*类别中选择*云 9* 。

这里有一个按钮*创建环境*你必须点击。

给它一个名字，然后做下一步

配置设置可以保持不变，只是需要将*实例类型*从`t2.micro`更改为`t2.small`，因为 BuckleScript 将编译 OCaml 编译器，这需要 1GB 以上的内存。我们将在 BuckleScript 安装结束时将其设置回原位。

下一步是再次查看*，点击*创建环境*，这可能需要几分钟时间。*

Cloud9 环境附带了一个预安装的 EC2 实例。你可以得到 Linux、AWS-CLI、Git、Nodejs (6.11 via nvm，可以修改)、GCC 和一大堆其他开发工具。

### 6。安装和配置 BuckleScript

当整个事情已经开始，你可以运行

```
npm i -g bs-platform 
```

Enter fullscreen mode Exit fullscreen mode

开箱即用。同样，由于 OCaml 的编译，这需要几分钟的时间。

在这之后，您可以使用

```
bsb -init my-first-service -theme basic-reason 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要更改在新项目文件夹中创建的`bsconfig.json`,这样无服务器框架稍后会找到您的输出 JS 文件。

```
suffix: ".bs.js" 
```

Enter fullscreen mode Exit fullscreen mode

到

```
suffix: ".js" 
```

Enter fullscreen mode Exit fullscreen mode

可以用`npm run build`运行编译器。您应该看到一个`lib`文件夹，其中有一些临时文件和一个`src/demo.js`文件，这是您成功构建的文件。

#### 插曲:将 EC2 实例设置回 t2.micro

在这之后，我建议你去 [AWS 管理控制台](https://console.aws.amazon.com/console/home)，这次用你的 AWS Root 帐户用户登录，将`t2.small`实例切换回`t2.micro`。

转到`Compute`类别中的`Services`和`EC2`。

选择`Instances`

用*实例类型* `t2.small`右击你的 Cloud9 实例，将*实例状态*设置为*停止*。

然后在这个实例上再次点击右键，使用*实例设置*改变*实例类型*。

在此菜单中，您现在可以选择`t2.micro`。如果您重启 Cloud9 环境，那么将会使用这个类型。

注销 root 帐户用户并作为新创建的用户重新登录 [AWS 管理控制台](https://console.aws.amazon.com/console/home)并返回到您的 Cloud9 环境*(服务- > Cloud9)*

### 7。安装和配置无服务器

现在我们需要无服务器框架来测试和部署我们将写入 AWS Lambda 的功能。

```
npm i -g serverless 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的`my-first-service`项目文件夹中，您需要创建一个带有 aws-nodejs 模板的无服务器项目。

```
serverless create --template aws-nodejs --path my-first-service 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的项目文件夹中创建另一个`my-first-service`文件夹。

您需要将这两个文件移到这个新文件夹中。

`handler.js`必须从`my-first-folder/my-first-folder`移动到`my-first-folder/src`

`serverless.yml`必须从`my-first-folder/my-first-folder`移动到`my-first-folder`

将`serverless.yml`更改为此

```
service: my-first-service

provider:
  name: aws
  runtime: nodejs6.10
  stage: dev
#  region: eu-west-1

functions:
  hello:
    handler: src/handler.hello
    events:
      - http:
          path: hello
          method: get 
```

Enter fullscreen mode Exit fullscreen mode

我使用了地区`eu-west-1`这可能对你来说是不同的，但是这个配置是可选的，它将默认使用美国地区。

如您所见，第一个函数`hello`被配置为在发生`HTTP GET /hello`事件时运行。

无服务器在`src/handler.js`内部查找导出的函数`hello`。

部署由无服务器运行创建的`hello`功能

```
serverless deploy 
```

Enter fullscreen mode Exit fullscreen mode

Cloud9 为您管理预安装的 AWS-CLI，因此无服务器框架可以简单地使用您的 Cloud9 用户来完成它的工作。

如果一切顺利，您将获得 AWS Lambda 函数正在运行的 HTTP 端点的 URL。

要再次*取消部署*您的函数，您可以运行

```
serverless remove 
```

Enter fullscreen mode Exit fullscreen mode

此外，还有一种在本地“测试”功能的方法:

```
serverless invoke local --function <FUNCTION_NAME> 
```

Enter fullscreen mode Exit fullscreen mode

### 8。将 JavaScript 代码转换为 Reason

现在我们可以部署了，我们需要合理地重写我们的代码。

为此，请将

```
handler.js 
```

Enter fullscreen mode Exit fullscreen mode

到…里面

```
handler.re 
```

Enter fullscreen mode Exit fullscreen mode

并将其内容替换为

```
type jsobject = {. "message": string};
[@bs.scope "JSON"] [@bs.val] external stringify : jsobject => string = "stringify";

let hello = (_event, _context, callback) => {
  let body = {
    "message" : "Hello from Reason!"
  };

  let response = {
    "statusCode": 200,
    "body": stringify(body)
  };

  [@bs] callback(Js.Nullable.null, response);
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是做什么的？

首先，它使得全局`JSON.stringify`方法合理可用。Reason 中的所有东西都必须被类型化，所以我为 message 对象创建了一个简单的 jsobject 类型，然后告诉 Reason,`JSON.stringify`方法需要该类型的输入。

然后我定义我的`hello`函数，这将成为我的 AWS Lambda 函数。我在未使用的参数前加上下划线，因为我只打算使用`callback`。

在函数中，我创建了一个 jsobject，并将其放入`stringify`。这成为一个(HTTP) `response`对象的`"body"`，它也需要一个`"statusCode"`。

最后，我调用带有错误参数的`null`和我的`response`的`callback`函数。`[@bs]`防止回调的 currying。

### 9。将原因编译成 JavaScript

当我们奔跑时

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

BuckleScript 将创建一个`src/handler.js`，它看起来与我们开始时的那个非常相似。

```
// Generated by BUCKLESCRIPT VERSION 2.1.0, PLEASE EDIT WITH CARE
'use strict';

function hello(_, _$1, callback) {
  var body = {
    message: "Hello from Reason!"
  };
  var response = {
    statusCode: 200,
    body: JSON.stringify(body)
  };
  return callback(null, response);
}

exports.hello = hello;
/* No side effect */ 
```

Enter fullscreen mode Exit fullscreen mode

因为原因文件是一个模块，所有的原因模块都默认导出它们的成员，所以我们不需要在原因代码中显式导出函数，但是正如我们看到的，它仍然会被导出。

### 10。将功能部署到 AWS Lambda

现在我们可以再次运行部署了

```
serverless deploy 
```

Enter fullscreen mode Exit fullscreen mode

并且可以导航到调用函数的端点，这次是用 Reason 编写的。

现在应该可以打印了

```
{"message": "Hello from Reason!"} 
```

Enter fullscreen mode Exit fullscreen mode

## 
 [T3】
结论](#conclusion) 

这是一个非常简单的例子，说明了如何让这两种前沿技术协同工作。

Reason 和 Serverless 都各自为政，让我们纯粹用 JavaScript 文件与它们接口。真正优秀的团队成员。

JS-Interopt 的原因有点繁琐，但至少你从中获得了一个完全*和*类型良好的代码库。

当你知道 lambda 的`event`和`context`对象的结构时，你可以用它来键入你的函数，甚至比我在这个小例子中展示的还要多，这将大大减少错误。

AWS Cloud9 也很好，但由于它也相当新，它没有你通过使用 Atom 或 VSCode 定制的所有插件。它预装了许多开发工具，甚至设置了 AWS-CLI，这当然很酷，但目前我不认为它是本地编辑器的可行替代品。

*感谢[丹尼尔·戈兰特](https://twitter.com/dangolant)检查我是否写了废话:D*