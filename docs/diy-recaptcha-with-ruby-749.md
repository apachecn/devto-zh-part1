# 用红宝石 DIY reCAPTCHA

> 原文：<https://dev.to/sophiedebenedetto/diy-recaptcha-with-ruby-749>

*这篇文章最初发表在我的个人博客上，[伟大的代码冒险](http://www.thegreatcodeadventure.com/google-recaptcha-ruby/)。*

reCAPTCHA 是一个反机器人工具，您可以在您的 web 应用程序中实现，以防止不良行为者以编程方式填写表单并向您的端点发送垃圾邮件。在这篇文章中，我们将使用 Google reCAPTCHA 2 客户端库和我们自己的手工验证客户端来实现 Google reCAPTCHA 协议。

## 我不是机器人

验证码是一种机制，通过它我们可以有计划地确定用户是人还是机器人。CAPTCHA 实际上代表“完全自动化的公共图灵测试，以区分计算机和人类”。典型的验证码会要求用户提交一些只有人类(或赛昂人？那部分还是有点模糊...)可以提供。例如，从图像中键入一些模糊的字母，选择一组包含汽车或街道标志的图像。

[![](img/fd1c95dafa9d6232b2c03928d5142010.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--BNad6zrY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1ihlac2rj9yexuxjp2q.png)  [来源](https://cdn.shopify.com/s/files/1/0289/1534/products/QMx_BSG_Spot_a_Cylon-2_988X988_60ecb559-caf0-40e2-9e9b-45478b3797b2.jpg?v=1401820189)

Captcha 有许多应用程序——防止垃圾评论，阻止机器人注册你的应用程序，以及以机器人驱动的登录形式抗击 DDoS 攻击，等等。

## 谷歌 reCAPTCHA 2

谷歌提供了一个相对较新的验证码协议——“reCAPTCHA”——允许用户通过简单的点击按钮来验证他们不是机器人。不需要通过输入单词或识别图片来“解决”传统的验证码。

集成 Google reCAPTCHA 非常简单。首先，我们需要向 Google reCAPTCHA 服务注册我们的应用程序，并接收我们应用程序的站点密钥和秘密密钥。

1.  登录你的谷歌账户，访问[https://www.google.com/recaptcha/intro/](https://www.google.com/recaptcha/intro/)，点击右手边的“获取 reCAPTCHA”按钮。
2.  在下一页上，从“reCAPTCHA 类型”列表中选择“reCAPTCHA 2 ”,并输入您的应用程序的域。
3.  最后，从下一页的“密钥”部分获取你的站点密钥和秘密密钥。请注意客户端和服务器端的说明。我们将使用这些指南在我们的网站上实现 reCAPTCHA。

## 在客户端实现 reCAPTCHA

我们将使用 reCAPTCHA 作为 Rails 应用程序的注册页面。我们希望我们的用户在提交注册表单之前验证他们不是机器人。

首先，我们需要加载 reCAPTCHA 库。我们将在应用程序布局中这样做，以便它可以在我们想要使用它的任何页面上使用。

```
<!-- app/views/layouts/application.html.erb -->
<!DOCTYPE html>
<html>
  <head>
    ...
    <script src='https://www.google.com/recaptcha/api.js'>
    </script>
  </head>

  <body>
    <%= render partial: 'layouts/navbar' %>
    <div class="container">
      <%= yield %>
    </div>
  </body>
</html> 
```

有两种方法可以将 reCAPTCHA 包含在特定页面上--自动和显式。

为了[在页面上自动呈现](https://developers.google.com/recaptcha/docs/display#auto_render)reCAPTCHA 小部件，我们只需在页面的任意位置添加以下内容:

```
<div class="g-recaptcha" data-sitekey="your_site_key"></div> 
```

然而，我们希望对小部件的外观和行为有更多的控制。例如，我们想要设置小部件的主题和语言。对于这个级别的控制，我们将使用[显式渲染方法](https://developers.google.com/recaptcha/docs/display#explicit_render)。

为了显式呈现 reCAPTCHA 小部件，我们将执行以下操作:

1.  定义一个 onload 回调函数，并插入 reCAPTCHA 资源。
2.  利用 reCAPTCHA `grecaptcha.render`功能配置 reCAPTCHA。
3.  在 reCAPTCHA 将呈现的负载上定义 HTML 元素。

让我们定义 onload 函数并插入 Javascript 资源。注意，当我们插入资源时，我们将`onload`参数设置为 onload 回调函数的名称`onRecaptchaElementLoad`，将`render`参数设置为`explicit`。还要注意，我们对我们的`script`标签使用了`async defer`标志，以确保脚本不会在其余页面加载之前运行。

```
# app/views/registrations/new.html.erb
<%=javascript_tag do %>
  var onRecaptchaElementLoad = function() {
    // coming soon!
  };
<%end%>

<script src='https://www.google.com/recaptcha/api.js?onload=onRecaptchaElementLoad&render=explicit' async defer></script> 
```

现在让我们定义我们的函数体来使用 [`grecaptcha.render`](https://developers.google.com/recaptcha/docs/display#js_api) 函数。该函数接受两个参数:

1.  其加载将触发此回调的元素的 ID。
2.  一组用于配置 reCAPTCHA 小部件的选项。

```
# app/views/registrations/new.html.erb
<%=javascript_tag do %>
  var onRecaptchaElementLoad = function() {
    grecaptcha.render('recaptcha', {
      'sitekey' : '<%= j ENV["REACAPTCHA_SITE_KEY"]%>',
      'hl': '<%= j locale %>',
      'theme': 'dark'
    });
  };
<%end%>
... 
```

`sitekey` param 是必需的，它被设置为我们向 Google reCAPTCHA 服务注册应用程序时收到的站点密钥的值。在 dotenv gem 的帮助下，我将我的站点密钥添加到我的 ENV 变量中。

我们还设置可选参数`hl`、语言名称和`theme`。这个代码片段假设我们有一个视图帮助器方法`locale`，它返回一个有效的语言代码(“en”、“de”、“fr”等)。

现在我们已经定义了回调函数，让我们将 reCAPTCHA 元素，一个带有“reCAPTCHA”的`id`的`div`添加到页面中。该要素应添加到注册表的中的*。* 

```
# app/views/registrations/new.html.erb
<%= form_for @user do |f| %>
  ...
  <div id="recaptcha"></div>
  <%= f.submit %>
<% end %> 
```

这就是我们的客户端集成。我们的表单现在将提交以下参数:

```
{"g-recaptcha-response" => "<recaptcha code>"} 
```

让我们继续我们的服务器端实现。

## 服务器端

我们将在控制器中验证我们的 reCAPTCHA 响应。虽然我们现在只把 reCAPTCHA 小部件放在我们的注册表单上，但我绝对期待在其他页面上使用 reCAPTCHA。例如，假设几个星期后，我发现我的登录或密码重置页面被大量的 bot 表单提交尝试垃圾邮件。我希望能够快速、轻松地将 reCAPTCHA 验证应用到这些控制器以及未来可能出现的其他控制器中。我想在不重复代码的情况下这样做。

因此，考虑到这种未来的需求，以及保持代码干燥的愿望，我们将把我们的 reCAPTCHA 验证代码放在一个控制器模块中，这个模块将包含在`RegistrationsController`中。

### 验证控制器模块中的 reCAPTCHA

我们将在`app/controllers/concerns`中定义我们的模块。它将调用一个 before action，该 action 将依次调用我们自己的 reCAPTCHA 验证服务(即将推出！).如果验证失败，它将重定向。它将利用`ActiveSupport::Concern`来访问一些漂亮干净的`included; do`语法。

```
# app/controllers/concerns/recaptcha_verifiable.rb
require 'active_support/concern'

module RecaptchaVerifiable
  extend ActiveSupport::Concern

  included do
    before_filter :recaptcha, only: [:create]
  end

  def recaptcha
    reroute_failed_recaptcha && return unless RecaptchaVerifier.verify(params["g-recaptcha-response"], request.ip)
  end

  def reroute_failed_recaptcha
    @person           = Person.new
    flash.now[:error] = "Please verify you are not a robot."
    render action: "new"
  end
end 
```

我们将把这个模块包含在我们的`RegistrationsController`中，并且我们可以把它包含在我们将来想要添加 recaptcha 的任何控制器中。

```
# app/controllers/registrations_controller.rb
class RegistrationsController < ApplicationController
  include RecaptchaVerifiable
  ...
end 
```

既然我们已经教会了我们的控制器如何验证 reCAPTCHA，以及如果验证失败该做什么，那么让我们构建验证服务。

#### 构建我们自己的 reCAPTCHA 验证器

我们将在`app/services`中定义我们的验证器。我们的服务只有一个责任:给定一个 reCAPTCHA 响应，如果响应有效，则返回`true`,如果无效，则返回`false`。

让我们思考一下这个责任。为了让我们的验证服务承担这个责任，它是否需要知道 reCAPTCHA 机制，也就是说，我们正在使用 Google reCAPTCHA？不要！

我们的服务将使用依赖注入来调用一个`recaptcha_client`。该客户端将负责通过外部 API 执行 reCAPTCHA 验证，在我们的例子中是 Google reCAPTCHA API。

```
# app/services/recaptcha_verifier.rb
class RecaptchaVerifier
  def self.verify(response, remote_ip, recaptcha_client=GoogleRecaptcha)
    new(response, remote_ip, recaptcha_client).verify
  end

  def initialize(response, remote_ip, recaptcha_client)
    @recaptcha_response = response
    @remote_ip          = remote_ip
    @recaptcha_client   = recaptcha_client.new
  end

  def verify
    return false unless recaptcha_response
    recaptcha_client.verify_recaptcha(response: recaptcha_response, remoteip: remote_ip)
  rescue
    false
  end

  private

  attr_reader :recaptcha_client, :recaptcha_response, :remote_ip
end 
```

这里，我们从参数、IP 地址(这是 Google reCAPTCHA API 需要的参数，我们很快就会看到这个 API)和 reCAPTCHA 客户端的第三个可选参数传入 reCAPTCHA 响应。我们将客户端默认为`GoogleRecaptcha`，接下来我们将定义一个类来处理实际的 Google reCAPTCHA API 请求。

我们的服务很简单。它:

*   在没有 reCAPTCHA 响应的情况下返回`false`(如果我们的控制器端点在没有参数`"g-recaptcha"`的情况下遇到*,就会发生这种情况——这肯定是一个坏角色的迹象)*
*   调用客户端来验证 reCAPTCHA 响应，返回该调用的值，我们假设该值为`true`或`false`。

现在我们准备构建我们的`GoogleRecaptcha`客户端。

#### 构建我们自己的 Google reCAPTCHA 客户端

我们的 Google reCAPTCHA 客户端将作为 reCAPTCHA 流程中的实际验证机制。因此，它只有一项工作:向 Google reCAPTCHA API 发送验证请求并解析响应。

我们将在`lib/`中定义我们的客户。

```
# lib/google_recaptcha.rb
class GoogleRecaptcha
  BASE_URL   = "https://www.google.com/".freeze
  VERIFY_URL = "recaptcha/api/siteverify".freeze

  def initialize
    @client = Faraday.new(BASE_URL)
  end

  def verify_recaptcha(params)
    response = perform_verify_request(params)
    success?(response)
  end

  def success?(response)
    JSON.parse(response.body)["success"]
  end

  private

  attr_reader :client

  def perform_verify_request(params)
    client.post(VERIFY_URL) do |req|
      req.params = params.merge({secret: ENV["RECAPTCHA_SECRET_KEY"]})
    end
  end
end 
```

我们的客户端希望收到一个散列参数，其中包含 Google reCAPTCHA API 所需的参数`remoteip`(指向原始用户请求的 IP)和`response`(指向来自原始请求的`"g-recaptcha"`参数的响应)。

对于这些参数，我们添加了键/值对:`{secret: ENV["RECAPTCHA_SECRET_KEY"]}`(假设我们已经将我们的密钥设置为一个 ENV 变量)。

这允许我们向 Google API 的验证端点 : `https://www.google.com/recaptcha/api/siteverify`发送[验证请求](https://developers.google.com/recaptcha/docs/verify)

谷歌将对此请求做出如下回应:

```
{  "success":  true|false,  "challenge_ts":  timestamp,  "hostname":  string,  "error-codes":  [...]  } 
```

我们将解析响应体的 JSON，根据`"success"`键的值返回`true`或`false`。

就是这样！这允许我们在控制器`before_action` :
中调用我们的服务

```
# app/controllers/registrations_controller.rb, by way of the RecaptchaVerifiable module

RecaptchaVerifier.verify(recaptcha_response, ip_address) 
```

它将依次调用我们的`GoogleRecaptcha`客户端返回`true`或`false`，这取决于 reCAPTCHA 响应的有效性。

你的网站现在是正式的机器人证明！

[![](img/891c9cea854b333b533f88173fba7977.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6JdrzDzD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmadrhlrb76opju749o8.png) 
[来源](https://wallpapercave.com/wp/CY8JOOV.jpg)