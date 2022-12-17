# 在 IIS 中启用跨源资源共享(CORS)

> 原文：<https://dev.to/adamkdean/enable-cross-origin-resource-sharing-cors-in-iis-4bcm>

如果你把你的 API 托管在与你的应用不同的域，比如 api.domain.com，你可能会遇到一些跨源请求的障碍。在 IIS 中，这很容易修复或者禁用。

在 WebAPI 项目的 web.config 中，放入以下内容:

```
<httpProtocol>
    <customHeaders>
        <add name="Access-Control-Allow-Origin" value="*" />
        <add name="Access-Control-Allow-Headers" value="Origin, X-Requested-With, Content-Type, Accept" />
    </customHeaders>
</httpProtocol> 
```

这将实现跨来源资源共享(CORS ),并允许您重新开始工作。