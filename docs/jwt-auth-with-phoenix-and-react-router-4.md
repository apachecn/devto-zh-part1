# JWT 用凤凰和反应路由器 4 认证

> 原文：<https://dev.to/sophiedebenedetto/jwt-auth-with-phoenix-and-react-router-4>

由于我显然无法满足 [JWT](http://www.thegreatcodeadventure.com/jwt-auth-in-rails-from-scratch/) [认证](http://www.thegreatcodeadventure.com/jwt-authentication-with-react-redux/)，下面看看如何使用 React 路由器 4，通过 React + Redux 前端应用程序使用它来认证您闪亮的新 Phoenix API。

在本帖中，我们将讨论:

*   使用 [React Router 4](https://github.com/ReactTraining/react-router) 建立常规路由和认证路由。
*   使用 React router 的`routerMiddleware`来教我们的商店如何处理 React Router 提供给我们的操作。
*   借助 [Comeonin](https://github.com/riverrun/comeonin) 和 [Guardian](https://github.com/ueberauth/guardian) 构建一个简单的 Phoenix API 端点进行认证。
*   使用 React 建立到 Phoenix websocket 和通道的连接。
*   使用 Guardian 插件对来自 React 的使用 JWT 的传入 API 请求进行身份验证。

## 配置路线

首先，我们将配置我们的路由并将该配置附加到 DOM 以呈现我们的组件树。

*就本文而言，假设我们正在构建一个聊天应用程序，用户可以访问聊天室索引`/chats`，并进入聊天室`chats/:id`，开始聊天*

```
# web/static/js/routes/index.js

import React               from 'react';
import { Route, Redirect } from 'react-router-dom'
import App                 from '../containers/app';
import Navigation          from '../views/shared/nav';
import RegistrationsNew    from '../views/registrations/new';
import SessionsNew         from '../views/sessions/new';
import Chats               from '../views/chats';
import Actions             from '../actions/sessions';

export default function configRoutes() {
  return (
    <div>
      <Navigation />
      <Route exact path="/" component={App} />
      <Route path="/sign_up" component={RegistrationsNew} />
      <Route path="/sign_in" component={SessionsNew} />
      <AuthenticatedRoute path="/chats" component={Chats} />
    </div>
  );
}

const AuthenticatedRoute = ({ component: Component, ...rest }) => (
  <Route {...rest} render={props => (
    localStorage.getItem('phoenixAuthToken') ? (
      <Component {...props}/>
    ) : (
      <Redirect to={{
        pathname: '/sign_in',
        state: { from: props.location }
      }}/>
    )
  )}/> ) 
```

Enter fullscreen mode Exit fullscreen mode

如果您熟悉 React Router 的早期版本，这些代码中的大部分可能看起来很熟悉。

我们已经定义了一个函数`configRoutes`，它使用 React Router DOM 的`Route`组件来定义一组路由。我们将每个路径映射到要呈现的组件，并在文件的顶部导入组件。

我们定义了以下路线:

*   `/`，根路径，指向我们的容器组件`App`。
*   `/sign_up`，它指向存放我们的注册表单的组件。
*   `/sign_in`，指向表单中包含我们的标志的组件。
*   `/chats`，指向聊天索引组件。这个路由是我们的受保护的，或认证的路由。

现在，让我们更仔细地看看这条经过身份验证的路由。

### 定义认证路由

我们的认证路由实际上只是一个功能组件。它由包含键`component`的`props`调用，设置为我们传入的`Chats`组件。

我们的功能组件返回一个`Route`组件。这个`Route`组件的`render()`函数负责从 props 渲染`Chats`组件，或者重定向。

让我们仔细看看这个`render()`函数:

```
props => (
  localStorage.getItem('phoenixAuthToken') ? (
    <Component {...props}/>   
  ) : (
    <Redirect to={{
      pathname: '/sign_in',
      state: { from: props.location }
    }}/>
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数根据`localStorage`中`phoenixAuthToken`键的存在与否来确定我们是否有一个经过身份验证的用户。稍后，我们将构建将从凤凰城收到的 JWT 存储在`localStorage`的功能。

如果令牌存在，我们将继续调用作为道具传递到我们的`Route`中的组件，即`Chats`组件。

如果没有找到令牌，我们将使用 React 路由器 DOM 中的`Redirect`组件来执行重定向。

就是这样！现在，我们将使用 ReactDOM 将路由配置附加到 DOM，从而将组件树附加到 DOM。

### 配置商店和路由器组件

```
import React from 'react';
import ReactDOM from 'react-dom';
import { createStore, applyMiddleware } from 'redux'
import { Provider} from 'react-redux'
import thunk from 'redux-thunk'
import createHistory from 'history/createBrowserHistory'
import {
  ConnectedRouter as Router,
  routerMiddleware
} from 'react-router-redux'
import {
  Route,
  Link
} from 'react-router-dom'

import configRoutes from './routes'
import rootReducer from './reducers'

const history = createHistory()
const rMiddleware = routerMiddleware(history)

const store = createStore(
  rootReducer,
  applyMiddleware(thunk, rMiddleware)
)

ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      <div>
        {configRoutes()}
      </div>
    </Router>
  </Provider>,
  document.getElementById('main_container')
); 
```

Enter fullscreen mode Exit fullscreen mode

这里有几点需要指出。

首先，我们使用 React 路由器的`routerMiddleware`。React Router 为我们提供了一组操作创建器功能，用于操作浏览器历史记录:

*   `push(location)`
*   `replace(location)`
*   `go(number)`
*   `goBack()`
*   `goForward()`

我们将在登录用户后使用`push`进行重定向。

然而，开箱即用的 Redux 存储不知道如何处理这些动作的分派。这就是`routerMiddleware`出现的原因。我们通过调用带有浏览器历史实例参数的`routerMiddleware`函数来创建一个`routerMiddleware`实例。

然后，我们通过`applyMiddlware`函数将这个中间件实例传递给我们的商店。现在，当我们分派上面列出的任何操作时，商店将通过将它们应用到我们的浏览器历史来处理它们。

值得注意的是，我们*仍然*需要将我们的浏览器历史实例传递给我们的`Router`。这将确保我们的路线与浏览器历史的位置和商店同步。

现在我们已经设置好了路由，让我们构建授权流。

## 组件的签到

我们的登录表单将存在于我们的`sessions/new.js`组件中。让我们把它建出来:

```
# /views/sessions/new.js

import React   from 'react';
import { connect }          from 'react-redux';
import { Link }             from 'react-router-dom';
import Actions              from '../../actions/sessions';

class SessionsNew extends React.Component {
  handleSubmit(e) {
    e.preventDefault();
    const { dispatch } = this.props;

    const data = {
      email: this.refs.email.value,
      password: this.refs.password.value
    };

    dispatch(Actions.signIn(data));
  }

  render() {
    const { errors } = this.props;

    return (
      <div className="container">
        <div className="container">
          <form 
            className="form-horizontal" 
            onSubmit={::this.handleSubmit}>
            <fieldset>
              <legend>Sign In</legend>
              <div className="form-group">
                <label className="col-lg-2">email</label>
                <div className="col-lg-10">
                  <input 
                    className="form-control" 
                    ref="email" 
                    id="user_email" 
                    type="text" 
                    placeholder="email" required={true} />
                </div>
              </div> 
              <div className="form-group">
                <label className="col-lg-2">password</label>
                <div className="col-lg-10">
                  <input 
                    className="form-control" 
                    ref="password" 
                    id="user_password" 
                    type="password" 
                    placeholder="password" required={true} />
                </div>
              </div>
              <br/>       
            <button type="submit">Sign in</button>
            </fieldset>
          </form>
          <Link to="/sign_up">Sign up</Link>
      </div>
      </div>
    );
  }
}

export default connect()(SessionsNew) 
```

Enter fullscreen mode Exit fullscreen mode

我们的表单非常简单，它有一个用户电子邮件字段和一个用户密码字段。在提交表单时，我们发送一个动作，将一个`POST`请求发送到我们的 Phoenix API 的登录路径。

让我们现在就开始行动吧。

## 行动中的标志

```
# /actions/sessions.js

import { push }      from 'react-router-redux';
import Constants     from '../constants';
import { Socket }    from 'phoenix';
import { httpPost }  from '../utils';

const Actions = {
  signIn: (creds) => {
    return dispatch => {
      const data = {
        session: creds,
      };
      httpPost('/api/v1/sessions', data)
      .then((response) => {
        localStorage.setItem('phoenixAuthToken', 
          response.jwt);
        setCurrentUser(dispatch, response.user);
        dispatch(push('/challenges'));
      })
      .catch((error) => {
        error.response.json()
        .then((errorJSON) => {
          dispatch({
            type: Constants.SESSIONS_ERROR,
            error: errorJSON.error,
          });
        });
      });
    };
  }
}

export default Actions 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们定义我们的`Actions`常量来实现一个函数`signIn()`。我们也使用这个文件来定义一个助手函数，`setCurrentUser()`。

`signIn()`函数依赖于我们在另一个文件`httpPost()`中定义的工具，向 Phoenix API 的登录端点发出我们的`POST`请求。

`httpPost()`函数依赖 Fetch 来发出 web 请求:

```
# web/utils/index.js

import fetch        from 'isomorphic-fetch';
import { polyfill } from 'es6-promise';

const defaultHeaders = {
  Accept: 'application/json',
  'Content-Type': 'application/json',
};

function headers() {
  const jwt = localStorage.getItem('phoenixAuthToken');

  return { ...defaultHeaders, Authorization: jwt };
}

export function checkStatus(response) {
  if (response.ok) {
    return response;
  } else {
    var error      = new Error(response.statusText);
    error.response = response;
    throw error;
  }
}

export function parseJSON(response) {
  return response.json();
}

export function httpPost(url, data) {
  const body = JSON.stringify(data);

  return fetch(url, {
    method: 'post',
    headers: headers(),
    body: body,
  }).then(checkStatus)
    .then(parseJSON);
} 
```

Enter fullscreen mode Exit fullscreen mode

*注意:这个文件将包含我们对 API 的所有 HTTP 请求，并依赖于`headers()`函数来使用令牌构建认证头，一旦我们认证了我们的用户*，这个令牌将存储在`localStorage`中。

因此，我们使用`httpPost`函数向 API 发出认证请求，如果请求成功，我们从响应体中获取`jwt`密钥，并将其存储在`localStorage`中。我们将很快构建这个端点，但是现在我们假设它存在并返回一个成功的响应主体:

```
{  jwt:  <some  token>,  user:  <serialized  user>  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们仔细看看负责这个动作的`signIn()`函数中的代码:

```
localStorage.setItem('phoenixAuthToken', response.jwt);
setCurrentUser(dispatch, response.user);
dispatch(push('/challenges')); 
```

Enter fullscreen mode Exit fullscreen mode

在我们在`localStorage`中设置了`phoenixAuthToken`之后，我们调用我们的助手函数`setCurrentUser`，并使用`dispatch`函数来调用一个路由更改。这种路由更改是在 React Router Redux 的`push`动作创建器功能的帮助下实现的。(还记得我们使用`routerMiddleware`使我们的商店能够处理`push`动作吗？)

我们几乎已经准备好进一步研究`setCurrentUser()`函数了。但是首先，让我们构建我们的 Phoenix API 的身份验证端点。

## 登录 API 端点

### 凤凰授权依赖

为了认证用户，我们将使用 Comeonin 库。为了给我们的用户生成一个 JWT 令牌，我们将依赖于 Guardian 库。

让我们将这些依赖项添加到我们的`mix.exs`文件中，并确保在我们的应用程序启动时启动 Comeonin 应用程序。

```
# mix.exs
...

def application do
  [
    mod: {PhoenixPair, []},
    applications: [:phoenix, :phoenix_pubsub, :phoenix_html, :cowboy, :logger, :gettext, :phoenix_ecto, :postgrex, :comeonin]
  ]
end

...
defp deps do
  [{:phoenix, "~> 1.2.1"},
   {:phoenix_pubsub, "~> 1.0"},
   {:phoenix_ecto, "~> 3.0"},
   {:postgrex, ">= 0.0.0"},
   {:phoenix_html, "~> 2.6"},
   {:phoenix_live_reload, "~> 1.0", only: :dev},
   {:gettext, "~> 0.11"},
   {:cowboy, "~> 1.0"},
   {:comeonin, "~> 2.0"},
   {:guardian, "~> 0.9.0"}]
end 
```

Enter fullscreen mode Exit fullscreen mode

### 定义路线

我们将在`/api/v1`下定义我们的 API 端点，并像这样定义我们的登录路径:

```
# /web/router.ex

  scope "/api", PhoenixPair do
    pipe_through :api

    scope "/v1" do
      post "/sessions", SessionsController, :create
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

### 定义控制器

`SessionsController`将实现一个创建函数，其中包含授权用户的代码。

```
# web/controllers/api/v1/sessions_controller.ex

defmodule PhoenixPair.SessionsController do 
  use PhoenixPair.Web, :controller

  alias PhoenixPair.{Repo, User}

  plug :scrub_params, "session" when action in [:create]

  def create(conn, %{"session" => session_params}) do
    case PhoenixPair.Session.authenticate(session_params) do
    {:ok, user} ->
      {:ok, jwt, _full_claims} = user 
        |> Guardian.encode_and_sign(:token)
      conn
        |> put_status(:created)
        |> render("show.json", jwt: jwt, user: user)
    :error ->
      conn
      |> put_status(:unprocessable_entity)
      |> render("error.json")
    end
  end

  def unauthenticated(conn, _params) do 
    conn
    |> put_status(:forbidden)
    |> render(PhoenixPair.SessionsView, "forbidden.json", 
      error: "Not Authenticated!")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

### 认证用户

我们的`create`函数依赖于一个助手模块`PhoenixPair.Session`来验证给定电子邮件和密码的用户。

```
# web/services/session.ex

defmodule PhoenixPair.Session do
  alias PhoenixPair.{Repo, User}
  def authenticate(%{"email" => e, "password" => p}) do
    case Repo.get_by(User, email: e) do
      nil -> 
        :error
      user ->
        case verify_password(p, user.encrypted_password) do
          true ->
            {:ok, user}
          _ ->
            :error
        end
    end
  end

  defp verify_password(password, pw_hash) do
    Comeonin.Bcrypt.checkpw(password, pw_hash)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个模块实现了一个函数`authenticate/1`，该函数期望被一个映射的参数调用，该映射与一个键为`"email"`和`"password"`的映射相匹配。

它使用电子邮件通过
查找用户

```
 Repo.get_by(User, email: email) 
```

Enter fullscreen mode Exit fullscreen mode

如果没有找到用户，我们的 case 语句将执行`nil ->`子句并返回原子`:error`。

如果找到一个用户，我们将调用我们的`verify_password`助手函数。该函数使用`Comeonin.Bcrypt.checkpw`来验证密码。如果验证成功，我们将返回元组`{:ok, user}`，其中`user`是我们的`Repo.get_by`查询返回的用户结构。

### 生成一个 JWT

回到我们的控制器，如果对`.Session.authenticate`的调用返回成功元组`{:ok, user}`，我们将使用 Guardian 生成一个 JWT。

```
...
{:ok, jwt, _full_claims} = user 
   |> Guardian.encode_and_sign(:token)
   conn
     |> put_status(:created)
     |> render("show.json", jwt: jwt, user: user) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们对`Guardian.encode_and_sign(user, :token)`的调用成功，我们将使用我们的会话视图来呈现下面的 JSON 负载:

```
{jwt:  jwt,  user:  user} 
```

Enter fullscreen mode Exit fullscreen mode

```
# web/views/sessions_view.ex

defmodule PhoenixPair.SessionsView do
  use PhoenixPair.Web, :view

  def render("show.json", %{jwt: jwt, user: user}) do
    %{
      jwt: jwt,
      user: user
    }
  end

  def render("error.json", _) do
    %{error: "Invalid email or password"}
  end

  def render("forbidden.json", %{error: error}) do
    %{error: error}
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果对`.Session.authenticate`的调用不成功，或者如果我们使用 Guardian 生成令牌的尝试不成功，我们将呈现一个错误。

现在我们的端点已经启动并运行，让我们回到 React 应用程序，讨论如何为当前用户设置一个成功的有效负载。

## 设置当前用户

在 React 和 Phoenix app 中设置当前用户是什么意思？我们希望利用凤凰频道的力量为我们的用户建立实时通信功能。因此，当我们“设置当前用户”时，我们需要为该用户建立一个套接字连接，并将该用户连接到他们自己的 Phoenix 通道。

在 React 端，我们将当前用户的信息存储在 state 中，在`session`键下，在`currentUser`键下:

```
# state
{
  session: 
    currentUser: {
      name: <a href="http://beatscodeandlife.ghost.io/">"Antoin Campbell"</a>,       email: "antoin5@5antoins.com"
    },
    ...
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，在我们的`signIn()`动作中调用的`setCurrentUser()`函数应该处理这两种责任。

### 建立当前用户的套接字连接

我们将从 Phoenix 导入`Socket`，并使用 Socket API 来建立我们用户的套接字连接。

```
import { Socket } from 'phoenix';

export function setCurrentUser(dispatch, user) {
  const socket = new Socket('/socket', {
    params: {token: localStorage.getItem('phxAuthToken') },
    logger: (kind, msg, data) => { console.log(`${kind}: ${msg}`, data); },
  });

  socket.connect();

  const channel = socket.channel(`users:${user.id}`);
  if (channel.state != 'joined') {
    channel.join().receive('ok', () => {
      dispatch({
        type: Constants.SOCKET_CONNECTED,
        currentUser: user,
        socket: socket,
        channel: channel,
      });
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们来分析一下。

*   首先，我们通过以下方式实例化一个新的`Socket`实例:

```
 const socket = new Socket('/socket', {
  params: {token: localStorage.getItem('phxAuthToken')},
  logger: (kind, msg, data) => { console.log(`${kind}: ${msg}`, data); 
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在这个实例上调用`connect`函数:

```
socket.connect() 
```

Enter fullscreen mode Exit fullscreen mode

这具有调用我们的`UserSocket`的`connect`函数的效果，参数为`%{"token" => token}`。我们需要定义这个套接字来实现`connect`函数:

```
web/channels/user_socket.ex

defmodule PhoenixPair.UserSocket do
  use Phoenix.Socket
  alias PhoenixPair.{Repo, User, GuardianSerializer, Session}

  ## Channels
  channel "users:*", PhoenixPair.UsersChannel

  ## Transports
  transport :websocket, Phoenix.Transports.WebSocket
  transport :longpoll, Phoenix.Transports.LongPoll

  def connect(%{"token" => token}, socket) do
    case Guardian.decode_and_verify(token) do
      {:ok, claims} ->
        case GuardianSerializer.from_token(claims["sub"]) do
          {:ok, user} ->
            {:ok, assign(socket, :current_user, user)}
          {:error, _reason} ->
            :error
        end
      {:error, _reason} ->
        :error
    end
  end

  def connect(_params, socket), do: :error

  def id(socket) do
    "users_socket:{socket.assigns.current_user.id}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的`connect`函数使用 Guardian 从 params 中解码 JWT。如果解码成功，我们将再次使用 Guardian 从反序列化的令牌有效载荷中提取用户结构。然后，我们将把这个结构分配给套接字存储系统中的键`:current_user`。这个套接字由我们可能为此用户打开的所有附加通道共享。因此，我们在这个插座上建立的任何未来通道都可以通过`socket.assigns.current_user`访问当前用户。

我们的`UserSocket`还实现了一个`connect`函数，它与预期的参数模式不匹配。这个函数将简单地返回`:error`。

```
def connect(_params, socket), do: :error 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们定义一个`id`函数，它返回这个套接字的名称，用我们的用户 ID 命名:

```
def id(socket) do: 
  "users_socket:#{socket.assigns.current_user.id}"
end 
```

Enter fullscreen mode Exit fullscreen mode

套接字 id 将允许我们识别给定用户的所有套接字，并因此通过特定用户的套接字广播事件。例如:

```
PhoenixPair.Endpoint.broadcast("users_socket:#{user.id}", "disconnect", %{}) 
```

Enter fullscreen mode Exit fullscreen mode

既然我们的用户套接字知道如何处理连接调用，让我们回到 React 应用程序的`setCurrentUser()`函数，并连接到`UsersChannel`。

### 连接到用户通道

我们将定义我们的`UsersChannel`来响应一个`join`函数，如果连接成功，返回套接字连接。

```
# web/channels/users_channel.ex

defmodule PhoenixPair.UsersChannel do 
  use PhoenixPair.Web, :channel

  def join("users:" <> user_id, _params, socket) do
    {:ok, socket} 
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将让 React 中的`setCurrentUser`函数发送一条消息来加入这个通道:

```
export function setCurrentUser(dispatch, user) {
  ...
  const channel = socket.channel(`users:${user.id}`);
  if (channel.state != 'joined') {
    channel.join().receive('ok', () => {
      dispatch({
        type: Constants.SOCKET_CONNECTED,
        currentUser: user,
        socket: socket,
        channel: channel
      });
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过
`socket.channel(`用户:${user.id} `)`获得我们的通道实例。然后，我们通过呼叫`channel.join()`加入频道。这将触发我们在`UsersChannel`中定义的`join`函数。

在函数调用上，我们链接了对`receive`的调用。当我们从通道得到“ok”响应时，将调用的`receive`函数。

一旦成功地加入了通道，我们就可以向 reducer 发送一个动作来更新当前用户以及套接字和通道的状态。我们希望将最后两项存储在 React 应用程序的状态中，这样我们就可以在构建聊天应用程序时使用它们来实现通道通信。

## 发出认证 API 请求

既然我们已经在 React 应用程序的状态中正确地存储了当前用户，并在`localStorage`中正确地存储了当前用户的 JWT，那么让我们看看我们将如何向 Phoenix API 发出后续的认证请求。

我们已经在`web/static/js/utils/index.js`中定义了一组助手函数，它们使用 Fetch 来发出 API 请求。这些函数依赖于一个助手方法`headers`，使用来自`localStorage` :
的令牌来设置授权头

```
import React        from 'react';
import fetch        from 'isomorphic-fetch';
import { polyfill } from 'es6-promise';

const defaultHeaders = {
  Accept: 'application/json',
  'Content-Type': 'application/json',
};

function headers() {
  const jwt = localStorage.getItem('phoenixAuthToken');

  return { ...defaultHeaders, Authorization: jwt };
}

export function checkStatus(response) {
  if (response.ok) {
    return response;
  } else {
    var error = new Error(response.statusText);
    error.response = response;
    throw error;
  }
}

export function parseJSON(response) {
  return response.json();
}

export function httpGet(url) {

  return fetch(url, {
    headers: headers(),
  })
  .then(checkStatus)
  .then(parseJSON);
}

export function httpPost(url, data) {
  const body = JSON.stringify(data);

  return fetch(url, {
    method: 'post',
    headers: headers(),
    body: body,
  })
  .then(checkStatus)
  .then(parseJSON);
} 

... 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们对 Phoenix API 的所有请求都使用了我们在这里定义的函数，`httpPost`，`httpGet`等等。，将在授权头中包含 JWT。

现在我们必须教会我们的 Phoenix 控制器使用这个头来授权传入的请求。幸运的是，Guardian 为我们做了很多这方面的工作。

来看看我们的`ChatsController`。

```
defmodule PhoenixPair.ChatsController do 
  use PhoenixPair.Web, :controller

  plug Guardian.Plug.EnsureAuthenticated, handler: PhoenixPair.SessionsController

  alias PhoenixPair.{Repo, User, Challenge}

  def index(conn, _params) do
    challenges = Repo.all(Chat) 
    render(conn, "index.json", chats: chats)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这是具有所有授权魔力的一行:

```
plug Guardian.Plug.EnsureAuthenticated, handler: PhoenixPair.SessionsController 
```

Enter fullscreen mode Exit fullscreen mode

[这个插件](https://hexdocs.pm/guardian/Guardian.Plug.EnsureAuthenticated.html)检查授权报头中的有效 JWT。
如果没有找到，它调用处理程序模块中的`unauthenticated`函数。在我们的例子中，这是我们之前定义的`PhoenixPair.SessionsController.unauthenticated`函数。

在开发我们的应用程序时，我们可以将这个插件添加到任何和所有经过验证的控制器中。

## 结论

到目前为止，我发现 React 和 Phoenix 配合得非常好。我确实带着一点恐惧接触了这个认证特性，之前没有使用过 React Router 4，也没有在 Phoenix 中做过任何基于令牌的认证。

然而，由于 React 路由器和 Guardian 提供的工具，在 React 前端和 Phoenix API 后端之间集成 JWT 身份验证是非常无缝的。

编码快乐！