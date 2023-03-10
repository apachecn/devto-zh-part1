# 在 IIS 上启用 AngularJS HTML5 路由

> 原文：<https://dev.to/adamkdean/enable-angularjs-html5-routing-on-iis-4ac>

AngularJS 能够使用 HTML5 路由，这意味着您的应用程序不需要使用散列符号(#)和路由，而是可以去掉散列，仍然作为单页应用程序运行。例如:

```
Without HTML5 routing: http://domain.com/#/user/15 -> http://domain.com/index.html
With HTML5 routing: http://domain.com/user/15 -> http://domain.com/index.html 
```

不过，要做到这一点，您需要设置某种服务器端 url 重写。要在 IIS 中做到这一点，您应该首先下载并安装 URL 重写模块(Web 平台安装程序会有它)。之后，我们需要建立一些规则。

如果你想要循序渐进的版本，请遵循以下步骤:

1.  打开有问题的网站，进入 URL 重写模块。
2.  添加一个入站规则，将其命名为“AngularJS Conditions”。设置模式匹配所有你想排除的目录，我用`(app/.*|assets/.*|common/.*)`。将动作更改为`None`，并点击应用。
3.  添加另一个入站规则，将其命名为“AngularJS 通配符”。将模式设置为`(.*)`，将动作保持为`Rewrite`，并输入您的`index.html`的相对 URL，然后单击 Apply。
4.  测试你的网站。
5.  如果您的 css/js 请求被重写，那么您的条件规则就有问题。
6.  如果您得到 404.0 错误，您的通配符规则有问题。
7.  回去工作。

如果你没有耐心，只想要 web.config，那么，你可以这样做:

```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <clear />
        <rule name="AngularJS Conditions" stopProcessing="true">
          <match url="(app/.*|assets/.*|common/.*|modules/.*|config/.*)" />
          <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
          <action type="None" />
        </rule>
        <rule name="AngularJS Wildcard" enabled="true">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll" trackAllCaptures="false" />
          <action type="Rewrite" url="index.html" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration> 
```

您现在应该已经为 HTML5 路由设置好了。