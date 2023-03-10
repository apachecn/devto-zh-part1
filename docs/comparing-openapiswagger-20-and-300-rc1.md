# 比较 OpenAPI/Swagger 2.0 和 3.0.0-rc1

> 原文：<https://dev.to/mikeralphson/comparing-openapiswagger-20-and-300-rc1>

[Kin Lane](https://twitter.com/kinlane),[API 布道者](https://apievangelist.com/)最近联系到我，问我是否有一个 OpenAPI 3.0.0 定义的好例子。

作为 [swagger2openapi](https://github.com/mermade/swagger2openapi) 的一部分，我不断更新无处不在的 Swagger [Petstore](https://github.com/Mermade/swagger2openapi/blob/master/conversions/swagger2openapi/openapi.json) 示例 api 定义的转换，一些 3.0.0 [示例](https://github.com/OAI/OpenAPI-Specification/tree/OpenAPI.next/examples/v3.0)最近被添加到 [OpenAPI 规范](https://github.com/OAI/OpenAPI-Specification)库中，但我们都同意我应该寻找一些真实世界的东西，展示 OpenAPI 2.0 (fka Swagger)和 OpenAPI 3.0.0 之间的主要变化。

事实证明，找到一个好的候选定义比我想象的要难，即使我是包含 500 多个真实 API 的 APIs.guru 集合的维护者。

我给自己定了一些基本原则:

*   该定义必须源自 Swagger 2.0 格式
*   它必须在源代码中有效，而不是像 APIs.guru 中的许多 API 那样修补
*   它应该包括 oAuth 认证
*   它必须有`body`或`formData`参数
*   它必须有`query`或`path`参数

幸运的是，我将所有 APIs.guru APIs 的元数据提取到一个数据库中，以便于分析。如果我在 APIs.guru 中找不到一个例子，我总是可以扩大搜索范围，包括我从 GitHub 和 [SwaggerHub](https://swaggerhub.com) 中索引的 45，000 多个 API。

我需要向 API 元数据表添加几列，然后使用我的约束进行查询，并按大小排序以找到最简洁的候选定义。

最终的赢家是 Authentiq。在 Kin 的[博客帖子](http://apievangelist.com/2017/05/22/a-working-example-of-openapi-version-3-dot-0-to-learn-from/)中提到的 Io API。

所以，让我们继续，开始看看 [Swagger 2.0 原始版本](https://gist.github.com/MikeRalphson/0c42c2778ea7a7a6fc0b5d59b4d7868e)和[转换的 OpenAPI 3.0.0 定义](https://gist.github.com/MikeRalphson/72e8172bddfa4de7330c9e218b95401f)之间的差异。

### 元数据

```
-swagger: '2.0' +openapi: 3.0.0-RC1 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们轻轻地弄湿我们的脚。OpenAPI 规范中对 Swagger 的所有引用都被改为 OpenAPI，并且在 API 定义中包含了`swagger`属性。

虽然版本号仍然是一个字符串，但它现在是[SEM ver](http://semver.org/)-major . minor . patch 兼容的。这意味着当 OpenAPI 3.0.0 规范发布时，可以发布补丁版本以澄清措辞、示例等，这不会影响规范本身，并且可以发布次要版本以向后兼容的方式添加新功能。只有突破性的改变才会产生 OpenAPI 4.x.x 版本。

补丁版本之后的任何字符都只是信息性的，应该被工具忽略。事实上，在确定兼容性时，工具作者应该只查看主要和次要(例如 3.0)版本。

对`info`对象的另一个改变是`termsOfService`现在必须是一个 URL。这应该不会影响许多 API，因为这似乎一直是标准的做法。

### API 端点定义

```
-host: connect.authentiq.io
-basePath: /
-schemes:
-  - https +servers:
+  - url: 'https://connect.authentiq.io/' 
```

Enter fullscreen mode Exit fullscreen mode

分离出`host`、`basePath`和`schemes`，进入一个`servers`数组，每个都有一个`url`属性，允许一个 API 有多个端点。与 Swagger 2.0 不同，OpenAPI 3 还通过可替换的`variables`支持 [url 模板](https://tools.ietf.org/html/rfc6570)(此处未显示，因为它们不会存在于转换后的定义中)。

### 内容-类型

```
-consumes:
-  - application/x-www-form-urlencoded
-  - application/json
-produces:
-  - application/x-www-form-urlencoded
-  - application/json
-  - application/problem+json
-  - text/html 
```

Enter fullscreen mode Exit fullscreen mode

顶级的`consumes`和`produces`数组被去掉了。正如我们将在后面看到的，每个`requestBody`和`response`现在可以指定多种内容类型。

### 查询和路径参数

```
 parameters:
         - name: client_id
           in: query
-          type: string
           description: >
             A client ID obtained from the
             [Dashboard](https://dashboard.authentiq.com/).
           required: true
+          schema:
+            type: string 
```

Enter fullscreen mode Exit fullscreen mode

`parameters`和`headers`不再是与`schema`对象共享`type`和`items`属性的对象，取而代之的是它们包含一个属性。如果`parameters`和`headers`的定义基于`content-type`而变化，它们可以选择性地拥有一个`content`对象来代替`schema`。

我发现这是一个无法将有效的 Swagger 2.0 定义无损转换为 OpenAPI 3.0 的领域。`tsv`(制表符分隔的值)的`array` `collectionFormat`已被删除，并且不再可能为数组内的数组定义嵌套分隔符，例如`a|b,c|d`。如果你需要这些特性，现在是在 OpenAPI 规范库中提出[问题](https://github.com/OAI/OpenAPI-Specification/issues)的好时机。

### 表单数据参数

```
 parameters:
         - name: Authorization
           in: header
           description: |
             HTTP Basic authorization header.
           required: false
-          type: string
-        - name: client_id
-          in: formData
-          description: |
-            The registered client ID.
-          required: true
-          type: string 
```

Enter fullscreen mode Exit fullscreen mode

这就是有趣的地方，所有的`in: formData`消失了。我们很快就会看到他们以新的面貌再次出现。对于`body`参数也是如此。

### 参考对象

```
 responses:
         '200':
-          $ref: '#/responses/Token' +          $ref: '#/components/responses/Token' 
```

Enter fullscreen mode Exit fullscreen mode

这是在 OpenAPI 3.0.0 中进行的重构的一个例子。来自`/definitions`的模式现在位于`/components/schemas`下，顶层的`responses`对象移到了`/components/responses`——所有的`$ref`都需要更新以保持定义的引用完整性。

### 请求体

```
+      requestBody:
+        content:
+          application/x-www-form-urlencoded:
+            schema:
+              type: object
+              properties:
+                client_id:
+                  description: |
+                    The registered client ID.
+                  type: string
+                client_secret:
+                  description: |
+                    The registered client ID secret.
+                  type: string
+                  format: password 
```

Enter fullscreen mode Exit fullscreen mode

这里我们看到先前的`formData`参数已经被转换成一个新对象的属性，保存在`operation`对象的`requestBody`属性下。

`consumes`数组值成为`content`对象图的键。

### 回应

```
 responses:
         '200':
           description: A list of Client Objects.
-          schema:
-            type: array
-            items:
-              $ref: '#/definitions/Client' +          content:
+            application/json:
+              schema:
+                type: array
+                items:
+                  $ref: '#/components/schemas/Client'
+            application/x-www-form-urlencoded:
+              schema:
+                type: array
+                items:
+                  $ref: '#/components/schemas/Client' 
```

Enter fullscreen mode Exit fullscreen mode

在`responses`下，每个旧的`produces`数组值可以有自己的模式。

### 响应标题

```
 responses:
         '201':
           description: Client created
           headers:
             Location:
               description: URL of new client resource
-              type: string +              schema:
+                type: string 
```

Enter fullscreen mode Exit fullscreen mode

与`parameters`一样，`headers`不再有`type`，而是由一个`schema`或`content`对象定义。

### 定义结构

```
 parameters:
-        - $ref: '#/parameters/client_id' +        - $ref: '#/components/parameters/client_id' 
```

Enter fullscreen mode Exit fullscreen mode

可重用的`parameters`已经从顶层的`parameters`对象移到了`/components/parameters`。

### 安全定义

```
-securityDefinitions:
-  client_secret:
-    description: Session management by confidential clients.
-    type: oauth2
-    flow: password
-    tokenUrl: 'https://connect.authentiq.io/token'
-    scopes:
-      clients: Enable client management +  securitySchemes:
+    client_secret:
+      description: Session management by confidential clients.
+      type: oauth2
+      flows:
+        password:
+          tokenUrl: 'https://connect.authentiq.io/token'
+          scopes:
+            clients: Enable client management 
```

Enter fullscreen mode Exit fullscreen mode

顶层`securityDefinitions`成为`/components/securitySchemes`对象。您可以看到每个 oAuth2 方案现在允许多个`flows`。

### 总结

这个例子就是这样。

这种转换的走查(通过正在进行的转换器，跟踪发布候选规范)并没有展示 OpenAPI 3.0.0 的任何新特性，如`links`和`callbacks`或`cookie`参数，但有望展示手动转换 API 定义时的一些主要变化领域，或者如果您使用像 [swagger2openapi](https://github.com/mermade/swagger2openapi) 这样的转换器，可以帮助您找到事情的发展方向。

也没有显示 RC2 候选版本的变更，包括对`discriminator`属性的变更。密切关注即将到来的释放。

像往常一样，如果你发现任何看起来不正确的规范，请不要犹豫与我联系。总是感激地接受反馈。