# 为部署到 Azure 应用服务的 Angular 应用添加 URL 重写

> 原文：<https://dev.to/diskdrive/adding-url-rewrite-for-an-angular-app-deployed-to-azure-app-service-2j0h>

当将 Angular 应用部署到 Azure 应用服务上时，路由将不工作，因为 Angular 使用客户端路由，而默认情况下，在应用服务上，路由将是服务器端的。因此，任何不是根域的东西都不会指向 index.html。

为了解决这个问题，你需要设置应用程序服务，这样任何路线都可以指向 index.html。

Azure 应用服务在 IIS 上后台运行，因此您需要一个 web.config 文件来重写 URL。为此，您需要创建一个 web.config 文件。

```
<configuration>

  <system.webServer>

    <rewrite>

      <rules>

        <rule name="angularjs routes"

        stopProcessing="true">

          <match url=".\*" />

          <conditions logicalGrouping="MatchAll">

            <add input="{REQUEST\_FILENAME}"

            matchType="IsFile" negate="true" />

            <add input="{REQUEST\_FILENAME}"

            matchType="IsDirectory" negate="true" />

            <add input="{REQUEST\_URI}"

            pattern="^/(api)" negate="true" />

          </conditions>

          <action type="Rewrite" url="/" />

        </rule>

      </rules>

    </rewrite>

  </system.webServer>

</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要把它添加到 angular-cli.json 文件的 Assets 位

```
 {

"$schema": "./node\_modules/@angular/cli/lib/config/schema.json",

"project": {

"name": "quiz"

},

"apps": [

{

"root": "src",

"outDir": "dist",

"assets": [

"assets",

"favicon.ico",

"web.config"

], 
```

Enter fullscreen mode Exit fullscreen mode

当您现在运行" **ng build"** 时，它会将 web.config 保存到 dist 文件夹中。