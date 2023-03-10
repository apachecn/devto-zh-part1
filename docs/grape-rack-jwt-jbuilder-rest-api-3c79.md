# 使用{grape} + Rack::JWT + Jbuilder 在 REST API 中实现令牌验证

> 原文：<https://dev.to/shiena/grape-rack-jwt-jbuilder-rest-api-3c79>

# 前言

在 Rails4 制作的 web APP 中追加了面向智能手机的 API，尝试使用现有的用户模型实现了令牌认证。 我自己虽然有过 Java 的经验，但是因为 Rails 天还很浅，所以可能会变成奇怪的代码。

# 为了编入 JWT 而研究的东西

## 这次编入的东西

*   [齿条::JWT](https://github.com/eparreno/rack-jwt)
    *   将{grape}的 http_basic、http_digest 作为参考编入！

## 不能很好地移动的东西

*   [葡萄::敲](https://github.com/gottfrois/grape-knock)
    *   因为没有追随 Knock 的多用户对应，所以无法动作
*   [敲门](https://github.com/nsarno/knock)
    *   Rails 力低，不知道如何嵌入
*   [铁轨，定计，JWT 和被遗忘的典狱长](https://medium.com/@goncalvesjoao/rails-devise-jwt-and-the-forgotten-warden-67cfcf8a0b73#.5qvqxhj44)
    *   Rails 力低，不知道组装后的使用方法

# URL design

尝试实现以下两种类型的令牌发放 API 和需要验证的 API。

## 令牌发行 API

```
curl -X POST \
     -H 'Content-Type: application/json' \
     -d '{"email": "foo@example.com", "password": "secret"}' \
     http://localhost:3000/api/v1/user/token
{"token": "ここにJWT", "expire": 7200} 
```

## 需要认证的 API

```
curl -X GET \
     -H 'Content-Type: application/json' \
     -H 'Authorization Bearer ここにJWT' \
     http://localhost:3000/api/v1/user/profile
{"email": "foo@example.com", "name": "Example person"} 
```

# 代码列表

这是追加、变更后的代码列表。

```
+---Gemfile
+---app
|   +---apis
|   |   +---root.rb
|   |   \---profile.rb
|   \---views
|       \---api
|           +---token.jbuilder
|           \---profile.jbuilder
\---config
    +---application.rb
    \---routes.rb 
```

```
gem 'grape'
gem 'grape-jbuilder'
gem 'rack-jwt' 
```

```
config.paths.add File.join('app', 'apis'), glob: File.join('**', '*.rb')
config.autoload_paths += Dir[Rails.root.join('app', 'apis', '*')]
config.middleware.use(Rack::Config) do |env|
  env['api.tilt.root'] = Rails.root.join 'app', 'views', 'api'
end 
```

```
mount API::Root => '/' 
```

```
module API
  class Root < Grape::MiddleWare::Base
    prefix 'api'
    content_type :json, 'application/json; charset=UTF-8'
    format :json
    version 'v1', using: :path

# Rack::JWTをGrapeに組み込み
    Grape::Middleware::Auth::Strategies.add(
      :jwt_auth,
      Rack::JWT::Auth,
      ->(options) { [
        [:secret, :verify, :options, :exclude].
          select { |key| options.has_key?(key) }.
          collect { |key| [key, options[key]] }.to_h
      ] }
    )

# 認証しないURLを定義する
    namespace :user do
      mount API::Token
    end

# 認証するURLを定義する
    namespace :user do
      auth :jwt_auth, {secret: Rails.application.secrets.secret_key_base}
      before do
# Rack::JWTが追加するキーをチェック
        error!('Unauthorized.', 401) unless env['jwt.payload']
        error!('Unauthorized.', 401) unless env['jwt.payload']['data']
        error!('Unauthorized.', 401) unless env['jwt.payload']['data']['id']

        @current_user = User.find_by_id env['jwt.payload']['data']['id']

        error!('Access Denied.', 403) unless @current_user
      end

      mount API::Profile
    end
  end
end 
```

```
module API
  class Token
    params do
      requires :email, type: String
      requires :password, type: String
    end
    post '/token', jbuilder:'token' do
      user = User.find_by_email params[:email]
      error!('Unauthorized.', 401) unless user
      error!('Unauthorized.', 401) unless user.valid_password?(params[:password])

      now = Time.current.to_i
      expire = 7200
      payload = {
        data: {id: user.id},
        exp: now + expire,
        iat: now
      }
      jwt = Rack::JWT::Token.encode(payload, Rails.application.secrets_key_base)
      @token = {token: jwt, expire: expire}
    end
  end
end 
```

```
module API
  class Profile
    get '/profile', jbuilder:'profile' do
# @current_user を利用するので特に処理はない
    end
  end
end 
```

```
json.(@token, :token, :expire) 
```

```
json.(@current_user, :email, :name) 
```

# 关于令牌生成和认证部分

*   Gemfile
*   配置/应用程序. rb
*   config/routes.rb

【https://github.com/ruby-grape/grape#rails】の説明通りに{grape}をRailsに組み込みます。

* * *

*   app/API/root . Rb

首先用不认证的 URL 和认证的 URL 划分`namespace`块。
至少发行令牌的 URL 需要对不进行认证的人进行定义。

接下来按照[注册自定义身份验证](https://github.com/ruby-grape/grape#register-custom-middleware-for-authentication)的说明装入 Rack::JWT，但没有写`auth`的模块。 这是因为{grape}的中间件和 Rack::JWT 的参数不同，无法将块传递给 Rack::JWT。 取而代之的是用`before`检查在 Rack::JWT 中设定的键。
并且`auth`的第 3 个自变量(`{secret: Rails.application.secrets.secret_key_base}`的部分)被传递给`Strategies.add`的第 3 个自变量的模块，由于该模块的返回值被传递给`Rack::JWT::Auth`的构造函数的第 2 个自变量，所以`:secret`、`:verify`

* * *

*   app/API/token . Rb
*   app/views/api/token.jbuilder

选中客户端发送的`email`和`password`后，使用 Rack::JWT 的`Token.encode`生成 JWT 的令牌。 编码用的秘密文字使用了`Rails.application.secrets_key_base`，但其他定义的文字也可以使用。

* * *

*   app/API/profile . Rb
*   app/views/api/profile.jbuilder

这是为了说明要认证的 URL 而用{grape}和 Jbuilder 制作的样本。

# 总结

虽然依赖于 Rack::JWT 的内部实现，但是成功地在{grape}中实现了使用 JWT 的令牌认证。 我知道更聪明的方法哦！ 如果有这样的人的话，请一定要告诉我！ ！ ！

# Reference

*   [[ rails4]用 grape 简单实现 API 将 API 分成多个版本的方法](http://qiita.com/hkusu/items/2ca0323cc276ab31e926)
*   [用 Grape + Rails 制作使用了 Jbuilder 的 JSON 格式 API](http://qiita.com/zaru/items/fa64a4e00b76ea6ce36f)
*   [齿条::JWT](https://github.com/eparreno/rack-jwt)
*   [葡萄::敲](https://github.com/gottfrois/grape-knock)
*   [敲门](https://github.com/nsarno/knock)
*   [铁轨，定计，JWT 和被遗忘的典狱长](https://medium.com/@goncalvesjoao/rails-devise-jwt-and-the-forgotten-warden-67cfcf8a0b73#.5qvqxhj44)