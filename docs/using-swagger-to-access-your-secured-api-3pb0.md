# 使用 Swagger 访问您的安全 api

> 原文：<https://dev.to/gerbrandvd/using-swagger-to-access-your-secured-api-3pb0>

斯瓦格，http://swagger.io/是让你的 api 可访问的好方法。不仅提供了文档，而且您的用户、合作开发人员或您自己都可以试用您的 api。但是，如果您的 API 是使用基于令牌的安全系统(如 webtokens)来保护的呢？

嗯，修改你的 swagger 前端来允许给请求添加任何令牌是很容易的。

首先在你的 swagger 模板文件中添加以下内容:

```
<form onsubmit="addApiKeyAuthorization()">
                <div class="input">
                    <input placeholder="access_token" id="input_apiKey" name="input_apiKey" type="text">
                    <input type="submit" value="Authenticate"/>
                </div>
            </form> 
```

然后在标题中添加下面的 Javascript 代码

```
<script>
    function addApiKeyAuthorization(){
        var apiKey = getUrlVars().input_apiKey;

        if (apiKey && apiKey.trim() != "") {
            console.log("initialzing oauth via api-key")
            var apiKeyAuth = new SwaggerClient.ApiKeyAuthorization("Authorization", "Bearer " + apiKey, "header");
            window.swaggerUi.api.clientAuthorizations.add("bearer", apiKeyAuth );
            //For clarity, also add in the input field
            $('#input_apiKey')[0].value=apiKey
        }
    }

</script> 
```