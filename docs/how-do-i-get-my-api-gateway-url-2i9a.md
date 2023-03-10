# 我如何获得我的 API 网关 URL？

> 原文：<https://dev.to/richdevelops/how-do-i-get-my-api-gateway-url-2i9a>

有时候，您需要知道 Lambda 中服务的 API 网关 URL。最近，当我的一个 Lambda 需要向它正在使用的第三方服务提供回调 URL 时，我就遇到了这种情况。

似乎很多人都在解决这个问题，他们使用[无服务器](https://serverless.com/)部署他们的 API，然后复制 URL 并重新部署，将 URL 硬编码到他们的`serverless.yml`中作为环境变量。

如果你是其中之一，那么**停止**。这是一条艰难的道路，而且有两个非常负面的副作用。

1.  很难将应用程序部署到其他阶段，而且
2.  它引入了一个风险，即您将使用不同阶段的 API 网关 URL 去部署到一个阶段会破坏一些东西。

那么，我该如何解决这个问题呢？

您可以使用无服务器变量和 CloudFormation 的组合来重构 API GW URL，而不是对其进行硬编码。下面是我如何设置 API 网关 URL，以便将其作为 GW_URL 环境变量传递给我的 Lambda。

```
provider:
  environment:
    GW_URL:
      Fn::Join:
        - ""
        - - "https://"
          - Ref: "ApiGatewayRestApi"
          - ".execute-api.${self:custom.region}.amazonaws.com/${self:custom.stage}" 
```

Enter fullscreen mode Exit fullscreen mode

在我的代码中，我现在可以使用。

```
process.env.GW_URL; 
```

Enter fullscreen mode Exit fullscreen mode

如果你使用的是[无服务器离线插件](https://github.com/dherault/serverless-offline)(或者类似的)，那么你可能想把它和[每阶段环境变量](//%7B%7B%20site.baseurl%20%7D%7D/blog/using-environment-variables-with-the-serverless-framework)结合起来。您需要使用每个阶段的环境变量，因为这种方法假设您使用 CloudFormation 进行部署，但是在本地运行意味着没有 CloudFormation 来解析 URL。相反，您需要为本地开发硬编码一个 URL。

下面是“备忘单”版本，但我强烈建议您阅读帖子中的[，因为完整版本对其进行了更深入的解释，并涵盖了为每个环境创建单独文件等主题。](//%7B%7B%20site.baseurl%20%7D%7D/blog/using-environment-variables-with-the-serverless-framework) 

```
provider:
  environment:
    GW_URL: ${self:provider.${self:custom.stage}.GW_URL}

custom:
  stage: "${opt:stage, self:provider.stage}"
  prod:
    GW_URL:
      {
        "Fn::Join":
          [
            "",
            [
              "https://",
              { "Ref": "ApiGatewayRestApi" },
              ".execute-api.${self:custom.region}.amazonaws.com/${self:custom.stage}",
            ],
          ],
      }
  dev:
    GW_URL: "http://localhost:3000/" 
```

Enter fullscreen mode Exit fullscreen mode

使用这种技术，您还可以从 CloudFormation 堆栈中导出 API 网关 URL，以便将其导入到其他堆栈中。这是解决服务发现问题的一种方法。

```
resources:
  Outputs:
    ApiUrl:
      Description: "The API Gateway URL"
      Value:
        Fn::Join:
          - ""
          - - "https://"
            - Ref: ApiGatewayRestApi
            - ".execute-api.${self:custom.region}.amazonaws.com/${self:custom.stage}" 
```

Enter fullscreen mode Exit fullscreen mode