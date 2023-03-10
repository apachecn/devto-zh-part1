# 在 swagger 中添加代理授权头

> 原文：<https://dev.to/meanin/adding-proxy-authorization-header-in-swagger-4o2>

大家好，
我已经把这个问题贴在了[栈溢出](https://stackoverflow.com/questions/47509258/add-proxy-authorization-header-in-swagger)上，但是我想，在这里我可以更快地找到感兴趣的人。

我需要在 swagger ui 上添加特定的标题。网芯。有什么方法可以包含这样的头吗？

已经尝试过:

*   实现 IOperationFilter:

```
public void Apply(Operation operation, OperationFilterContext context)
{
    if (operation.Parameters == null)
        operation.Parameters = new List<IParameter>();

    if (operation.Parameters.All(p => p.Name != "Proxy-Authorization"))
    {
        operation.Parameters.Add(new NonBodyParameter
        {
            Name = "Proxy-Authorization",
            In = "header",
            Description = "Proxy-Authorization token",
            Required = true,
            Type = "string"
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*   添加安全定义:

```
options.AddSecurityDefinition("Authorization", new ApiKeyScheme()
{
    In = "header",
    Description = "Please insert Proxy Authorization Secret into field",
    Name = "Proxy-Authorization",
    Type = "apiKey"
}); 
```

Enter fullscreen mode Exit fullscreen mode

两个都不行。当我更改头名称时，一切都正常(`HttpContext.Request.Headers`包含所需的头)，但是这个特定的(`Proxy-Authorization`)头从调用中消失了。

你遇到过这个问题吗？怎么解决？

编辑:

我确认这不是中的问题。仅限网络核心。满了。net 框架也有这个 bug。

此外，它只影响带有前缀`proxy-`的报头。