# 节点服务:Javascript 和。网在另一边会合

> 原文：<https://dev.to/rionmonster/nodeservices-where-javascript-and-net-meet-back-on-the-other-side>

*这篇文章最初发表在[我的博客](http://rion.io)上。*

Javascript 无处不在。它在浏览器里，在服务器上，它爬进你的窗户，把你的人抓走。因为这是一种很容易掌握的语言(但不可能掌握)，当你想在服务器上做一些事情时，它会非常有用。

这是 Node.js 等技术的真正吸引力所在，但是在. NET 领域中很容易做到。这篇文章将提供一个非常基本的概述，介绍如何在. net 中定位和执行一些任意的 Javascript 代码。NET 而无需接触浏览器。

## 什么是节点服务？

我让创作者史蒂夫·桑德森解释如下:

*   NodeServices 为。NET 代码在 Node.js 环境中的服务器上运行 JavaScript。你可以用它在运行时在你的 ASP.NET 核心应用中使用 NPM 包中的任意功能。

*   大多数应用程序开发人员不需要直接使用它，但是如果您想实现自己的功能，包括从。NET 在运行时。

你也可以在 GitHub 上找到项目本身，这远远超出了仅仅与. NET 中的节点进行交互。

## 设置节点服务

这个节点服务的特殊例子依赖于。NET Core 2.0，如果你想跟进的话，你可以从这里安装它。步骤*可能*适用于早期版本，但是如果你遇到问题，考虑尝试 2.0。

在这个例子中，我们将构建一个简单的 Web API，它将依赖 NodeServices 来执行一些操作。因此，开始之前，我们首先需要创建一个新项目:

```
dotnet new webapi 
```

Enter fullscreen mode Exit fullscreen mode

如果你不是命令行人士， [Visual Studio 2017 更新 3](https://www.visualstudio.com/vs/preview/) *应该*有必要的工具来创建。NET Core 2.0 应用程序。

## 配置节点服务

开始使用 NodeServices 非常简单。您基本上只需要通过下面的命令将`Microsoft.AspNetCore.NodeServices` NuGet 包包含在您的应用程序中:

```
dotnet add package Microsoft.AspNetCore.NodeServices 
```

Enter fullscreen mode Exit fullscreen mode

然后，您也应该在您的项目定义中看到它:

```
<ItemGroup>
    <!-- Others omitted for brevity -->
    <PackageReference Include="Microsoft.AspNetCore.NodeServices" Version="2.0.0" />
</ItemGroup> 
```

Enter fullscreen mode Exit fullscreen mode

然后，您需要配置必要的中间件，以便在您的应用程序中使用您的`Startup.cs`文件的`ConfigureServices()`方法:
中的服务

```
public void ConfigureServices(IServiceCollection services)
{
    // This will configure the NodeServices service
    services.AddNodeServices();
} 
```

Enter fullscreen mode Exit fullscreen mode

在配置服务之后，您现在将能够使用依赖注入来处理在控制器级别将其注入到您的应用程序中:

```
public class ValuesController : Controller
{
        private readonly INodeServices _nodeServices;

        public ExampleController(INodeServices nodeServices)
        {
            _nodeServices = nodeServices;
        }

        // Other actions here
} 
```

Enter fullscreen mode Exit fullscreen mode

或者在具有`[FromServices]`属性的单个方法/动作级别:

```
public async Task<IActionResult> Example([FromServices] INodeServices nodeServices)
{
       // TODO: Cool stuff
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经连接了实际的节点服务，让我们看看在 Javascript 方面需要做些什么，让双方能够很好地合作。

## 实际使用 NodeServices

因为我们将从内部调用 Javascript 代码。NET 中，我们将首先需要定义我们想要调用的实际代码。首先，我们将**在当前项目的根目录下创建一个新的脚本文件夹，并在其中创建一个名为 Add.js 的新文件**:

[![add.js-in-solution](img/f7e09f072d251a7d297d569e994f80bc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gawctKuZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://rion.io/conteimg/2017/06/node-services-example.png)

这个 Add.js 文件将作为一个单独的模块运行，它将导出一个函数，这个函数将被我们的 C#代码调用。为了简单起见，我们将简单地将两个数相加，并通过回调函数返回结果:

```
module.exports = function(a, b, callback) { 
  let result = a + b;
  callback(result); 
}; 
```

Enter fullscreen mode Exit fullscreen mode

跳回。NET 方面的东西，我们可以配置我们的服务，使用`InvokeAsync<T>()`方法指向我们的新 Javascript 文件，该方法需要被调用文件的路径和一个类型来指示预期的返回类型:

```
public async Task<long> Add(int x = 11, int y = 31)
{
    return await _nodeServices.InvokeAsync<long>("Scripts/Add.js", x, y);
} 
```

Enter fullscreen mode Exit fullscreen mode

在运行应用程序并到达端点后，您将很快看到预期的结果，并且在响应的创建过程中没有客户端 Javascript 受到损害:

[![add-example](img/129f2500c8666e10e88d37216b6c6911.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--bP8XGhJM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://rion.io/conteimg/2017/08/add-example.gif)

现在，如果你熟悉 Javascript，你就会知道它可以做各种疯狂的 T2，尤其是当你使用像 T0 这样非常危险的函数时。我们将创建另一个名为 Eval.js 的文件，添加到我们现有的脚本文件夹中，如下所示:

```
module.exports = function (callback, x) {
    let result = eval(x);
    callback(null, result);
}; 
```

Enter fullscreen mode Exit fullscreen mode

出于演示的目的，让我们创建另一个方法，它接受一些任意文本，并在我们的 Web API 控制器中对其进行评估:

```
public async Task<string> Eval(string expression = "6 * 7")
{
    return await _nodeServices.InvokeAsync<string>("Scripts/Eval.js", expression);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在下面的例子中看到这一点:

[![eval-example](img/b9c2bbb913fea824b3442f9fb3a465a0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--n7XAOT_0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://rion.io/conteimg/2017/08/eval-example.gif)

在你可能找不到你正在寻找的完美的 NuGet 包的场景中，NodeServices 确实大放异彩，但是在 npm 上或者在巨大的 Javascript 生态系统中的某个地方有一个 nu get 包。只需获取您需要的代码，或者下载实际的 npm 包本身(及其所需的依赖项)并按照您的期望使用它。

让我们看看，如果我们决定使用一个奇特的 npm 包，使用一点文本来生成 QR 码，类似的东西可能如何工作。首先，我们需要安装合适的 npm 包:

```
npm install qr-image 
```

Enter fullscreen mode Exit fullscreen mode

同样——如果你不是命令行爱好者，你可以直接从 GitHub 上的源代码下载或者使用你选择的其他包管理器。

一旦你下载了 QR 包，你可以在你的脚本文件夹中创建一个名为 QR.js 的新文件。因为我们现在在一个节点世界中，你只需要通过一个指向你的包的`require()`语句连接适当的依赖项，并添加下面的代码:

```
let qr = require('./qr-image');
module.exports = function (callback, text) {
    var result = qr.imageSync(text, { type: 'png' });

    var data = [];
    result.forEach(i => {
        data.push(i);
    });

    callback(null, data);
}; 
```

Enter fullscreen mode Exit fullscreen mode

这将执行以下操作:

*   连接我们的 QR 包以供使用。
*   使用传入的字符串数据生成 QR 码图像。
*   将图像数据读入我们的 C#代码将使用的 byte[]。

此时，我们可以用我们的 API 编写相应的方法:

```
public async Task<IActionResult> QR(string text = "42")
{
    var data = await _nodeServices.InvokeAsync<byte[]>("Scripts/QR.js", text);
    return File(data, "image/png");
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们点击该方法时，我们看到值按预期返回:

[![qr-example](img/0119d46622be6d04f4c7c8390dffb999.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vnKVZv9g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://rion.io/conteimg/2017/08/qr-example.gif)

这只是冰山一角，但它展示了在其中集成整个节点生态系统是多么容易。NET 来构建各种应用程序。

## 试试看！

如前所述，npm 和节点生态系统是巨大的，有大量有用的包，你可以轻松地下载并集成到你的 ASP.NET 应用中。

如果您不想手动完成所有这些步骤，请随意从该演示中提取代码，或者查看 Steve Sanderson 在 NodeServices repo 上找到的其他示例:

*   [参见 GitHub 上的这些例子](https://github.com/rionmonster/NodeServicesExample/)
*   [查看 Steve Sanderson 在 GitHub 上的例子](https://github.com/aspnet/JavaScriptServices/tree/dev/samples/misc/NodeServicesExamples)