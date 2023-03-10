# ReactJS 和 Laravel -运行基本设置-第 2 部分

> 原文：<https://dev.to/devtalhaakbar/reactjs-and-laravel---running-through-a-basic-setup---part-2-20p>

在这篇大文章中，我们将使用 Laravel 和 ReactJS 来设置认证系统。我们将使用 Laravel 5.5 附带的 Auth 脚手架。无论如何，在我们开始运行代码之前，将我们的目标，即身份验证系统，分成更小的部分是一个好主意:

*   登录页面
*   登录页面
*   创建帐户页面
*   仪表板页面

有了上面的想法，我们可以确定我们会有四个组件:`SignInPage`、`CreateAccountPage`、`LandingPage`、`DashboardPage`。假设这些组件将分别显示在`/signin`、`/account/create`、`/`和`/dashboard`URL 上。考虑到这一点，我们可以快速预测我们的路由器组件会是什么样子:

```
<BrowserRouter>
    <div>
        <Route exact path="/" component={LandingPage} />
        <Route path="/signin" component={SignInPage} />
        <Route path="/account/create" component={CreateAccountPage} />
        <Route path="/dashboard" component={DashboardPage} />
    </div>
</BrowserRouter> 
```

Enter fullscreen mode Exit fullscreen mode

在你询问之前，`<div>`是必要的，因为`<BrowserRouter>`只接受一个孩子。继续，让我们制作组件:

```
const LandingPage = (props) => {
    return <div>Landing Page</div>;
};
const SignInPage = (props) => {
    return <div>Sign In Page</div>;
};
const CreateAccountPage = (props) => {
    return <div>Create Account Page</div>;
};
const DashboardPage = (props) => {
    return <div>Dashboard Page</div>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们让它们保持简单；目前为止。另外，请注意所有四个组件都是无状态的。我总是从无状态组件开始，然后如果需要的话，让它们有状态。当然，除非你已经知道你的组件有状态。不管怎样，我们继续前进。

如果到目前为止你还没有猜到，当然，上面所有的代码经过一些调整后将进入我们的`/resources/assets/js/components/App.js`文件。让我们看看`App.js`到目前为止应该是什么样子:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import {
    BrowserRouter,
    Route
} from 'react-router-dom';

let LandingPage = (props) => {
    return <h1 className="mt-5">Landing Page</h1>;
};
let SignInPage = (props) => {
    return <h1 className="mt-5">Sign In Page</h1>;
};
let CreateAccountPage = (props) => {
    return <h1 className="mt-5">Create Account Page</h1>;
};
let DashboardPage = (props) => {
    return <h1 className="mt-5">Dashboard Page</h1>;
};

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
        <BrowserRouter>
            <div>
                <Route exact path="/" component={LandingPage} />
                <Route path="/signin" component={SignInPage} />
                <Route path="/account/create" component={CreateAccountPage} />
                <Route path="/dashboard" component={DashboardPage} />
            </div>
        </BrowserRouter>
        );
    }
};

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

是的，原则上，`LandingPage`、`SignInPage`、`CreateAccountPage`、`DashboardPage`应该在它们自己的文件中，并且应该导入到`App.js`中，但是，为了简单起见，我们保持原样。接下来，我们可以制作一个包含`Link`组件的`Header`组件。所以，让我们从`react-router-dom`导入`Link`，并如下声明【T9:

```
let Header = (props) => {
    return (
        <nav className="navbar navbar-expand-lg navbar-light bg-light">
            <div className="container px-2 px-sm-3">
                <Link className="navbar-brand" to="/">DEV.TO</Link>
                <button className="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                    <span className="navbar-toggler-icon"></span>
                </button>

                <div className="collapse navbar-collapse" id="navbarSupportedContent">
                    <ul className="navbar-nav ml-auto">
                        <li className="nav-item">
                            <Link className="nav-link" to="/signin">Sign In</Link>
                        </li>
                        <li className="nav-item">
                            <Link className="btn btn-primary ml-lg-2 ml-sm-0 mt-2 mt-lg-0" to="/account/create">Create Account</Link>
                        </li>
                    </ul>
                    <ul className="navbar-nav ml-auto">
                        <li className="nav-item">
                            <Link className="btn btn-primary ml-lg-2 ml-sm-0 mt-2 mt-lg-0" to="/dashboard">Dashboard</Link>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我将仪表板链接放在一个单独的`<ul>`中，因为很快我们将在标题中引入一个三元运算符形式的条件`if/else`。但是，我们继续跑吧！

我非常想跳到`SignInPage`部分，但是，让我们看看目前为止的`App.js`文件:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import {
    BrowserRouter,
    Link,
    Route
} from 'react-router-dom';

let LandingPage = (props) => {
    return <h1 className="mt-5">Landing Page</h1>;
};
let SignInPage = (props) => {
    return <h1 className="mt-5">Sign In Page</h1>;
};
let CreateAccountPage = (props) => {
    return <h1 className="mt-5">Create Account Page</h1>;
};
let DashboardPage = (props) => {
    return <h1 className="mt-5">Dashboard Page</h1>;
};
let Header = (props) => {
    return (
        <nav className="navbar navbar-expand-lg navbar-light bg-light">
            <div className="container px-2 px-sm-3">
                <Link className="navbar-brand" to="/">DEV.TO</Link>
                <button className="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
                    <span className="navbar-toggler-icon"></span>
                </button>

                <div className="collapse navbar-collapse" id="navbarSupportedContent">
                    <ul className="navbar-nav ml-auto">
                        <li className="nav-item">
                            <Link className="nav-link" to="/signin">Sign In</Link>
                        </li>
                        <li className="nav-item">
                            <Link className="btn btn-primary ml-lg-2 ml-sm-0 mt-2 mt-lg-0" to="/account/create">Create Account</Link>
                        </li>
                    </ul>
                    <ul className="navbar-nav ml-auto">
                        <li className="nav-item">
                            <Link className="btn btn-primary ml-lg-2 ml-sm-0 mt-2 mt-lg-0" to="/dashboard">Dashboard</Link>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    );
};

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
        <BrowserRouter>
            <div>
                <Header />
                <Route exact path="/" component={LandingPage} />
                <Route path="/signin" component={SignInPage} />
                <Route path="/account/create" component={CreateAccountPage} />
                <Route path="/dashboard" component={DashboardPage} />
            </div>
        </BrowserRouter>
        );
    }
};

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

现在，`SignInPage`将主要包含一种形式。并且，对于表单管理，我们包含了依赖于`redux`的`redux-form`。让我们快速设置两者:

```
...

import { createStore, combineReducers } from 'redux'
import { reducer as formReducer } from 'redux-form'

...

const rootReducer = combineReducers({
  form: formReducer,
  // my other reducers come here
});

const store = createStore(rootReducer); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个 Redux 存储，但是要将这个存储与 React 组件一起使用，我们需要使用`react-redux`包附带的`Provider`组件。很简单(我们只需要用`Provider`把我们的`BrowserRouter`包起来，把`store`当道具传过去)所以，我们就来看看目前为止我们的`App.js`:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import {
    BrowserRouter,
    Link,
    Route
} from 'react-router-dom';

import { createStore, combineReducers } from 'redux'
import { reducer as formReducer } from 'redux-form'

import { Provider} from 'react-redux'

// Other components are here collapsed

const rootReducer = combineReducers({
  form: formReducer,
  // my other reducers come here
});

const store = createStore(rootReducer);

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
        <Provider store={store}>
            <BrowserRouter>
                <div>
                    <Header />
                    <Route exact path="/" component={LandingPage} />
                    <Route path="/signin" component={SignInPage} />
                    <Route path="/account/create" component={CreateAccountPage} />
                    <Route path="/dashboard" component={DashboardPage} />
                </div>
            </BrowserRouter>
        </Provider>
        );
    }
};

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们修改`SignInPage`组件:

```
class SignInPage extends Component {
    constructor(props) {
        super(props);

        this.processSubmit = this.processSubmit.bind(this);
    }

    componentWillMount() {
        // do something like setting default state
    }

    processSubmit(values) {
        // do something with the values
    }

    render() {
        const { handleSubmit, submitting } = this.props;

        return (
            <div className="container mt-5">
                <div className="row justify-content-center">
                    <div className="col-6">
                        <div className="card">
                            <div className="card-body">
                            <h2 className="text-center font-weight-light mb-4">Sign into your account</h2>
                                <form onSubmit={handleSubmit(this.processSubmit)}>
                                    <Field
                                        label="Email Address"
                                        name="email"
                                        component={FormField}
                                        id="email"
                                        type="text"
                                        className="form-control"
                                    />
                                    <Field label="Password" name="password" component={FormField} id="password" type="password" className="form-control" />
                                    <div className="form-check">
                                        <label className="form-check-label">
                                            <Field name="remember" component="input" type="checkbox" className="form-check-input mt-2" value="1" />
                                            Remember me
                                        </label>
                                    </div>
                                    <div className="form-group mt-4">
                                        <button type="submit" className="btn btn-secondary" disabled={submitting}>Continue</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        );
    }
};

SignInPage = reduxForm({
    form: 'signin',
    validate
})(SignInPage); 
```

Enter fullscreen mode Exit fullscreen mode

在我们继续之前，有一些事情需要消化:

*   `SignInPage`组件不再是只渲染其他组件并通过`props`的可怜的哑组件。
*   `reduxForm`装饰器和`Field`组件已经从`redux-form`包中导入，所以我们对`redux-form`的导入声明现在看起来像这样:

```
import {
    reducer as formReducer,
    reduxForm,
    Field
} from 'redux-form' 
```

Enter fullscreen mode Exit fullscreen mode

*   注意`Field`把一个`component`作为道具。您指定的任何组件都将在`Field`中呈现。我们指定的内部组件获得了一些很好的道具，可以用来使输入交互。下面是我如何编写一个基本的`FormField`组件:

```
const FormField = ({
        label,
        input,
        type,
        name,
        className,
        meta: { touched, error, warning }
}) => (
    <div className="form-group">
        {
            label &&
            <label htmlFor={name}>{label}</label>
        }
        <input {...input } name={name} type={type} className={
            `${className} ${
                touched && (
                    (error && 'is-invalid')
                )
            }`
        } />
        {
            touched &&
                (error && <span className="invalid-feedback">{error}</span>)
        }
    </div>
); 
```

Enter fullscreen mode Exit fullscreen mode

*   仔细观察新`SignInPage`组件中的这一行:

```
const { handleSubmit, submitting } = this.props; 
```

Enter fullscreen mode Exit fullscreen mode

`handleSubmit`和`submitting`道具来自装饰后的签到页面组件。`handleSubmit`接受一个回调，将`values`作为第一个参数传递。`submitting`只是一个布尔值，它告诉表单是否处于提交状态，这有助于禁用提交按钮，以防止在处理一个请求时出现多个请求。

*   另外，请注意下面几行中的`validate`属性:

```
SignInPage = reduxForm({
    form: 'signin',
    validate: validatorSignInForm,
})(SignInPage); 
```

Enter fullscreen mode Exit fullscreen mode

属性必须包含一个返回字段错误信息的函数。我将使用一个简单的基于 JavaScript 的验证库`validate.js`来实现`validatorSignInForm`，你可以用`npm install --save validate.js`来安装它。验证器函数如下:

```
const validatorSignInForm = (values) => {
    const result = validate(values, {
        email: {
            presence: {
                message: '^Please enter your email address.'
            },
            email: {
                message: '^Please enter a valid email address.'
            }
        },
        password: {
            presence: {
                message: '^Please enter your password.'
            }
        }
    });

    return result;
}; 
```

Enter fullscreen mode Exit fullscreen mode

你应该知道`validate()`来自于`validate.js`库，所以现在你只需要知道它把`values`作为它的第一个参数，把格式良好的对象中的每个值的规则作为它的第二个参数。

到目前为止，我们已经查看了许多代码，但是，您将会看到，一旦您浏览并理解了这些代码，您在为您的应用程序创建交互式表单时将不会遇到任何问题。

现在，让我们把更新后的`SignInPage`组件和目前为止我们在`App.js`中编写的代码放在一起，看看它现在是什么样子:

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

import {
    BrowserRouter,
    Link,
    Route
} from 'react-router-dom';

import { createStore, combineReducers } from 'redux'
import {
    reducer as formReducer,
    reduxForm,
    Field
} from 'redux-form'

import { Provider} from 'react-redux'

// Other components are here collapsed

const validatorSignInForm = (values) => {
    const result = validate(values, {
        email: {
            presence: {
                message: '^Please enter your email address.'
            },
            email: {
                message: '^Please enter a valid email address.'
            }
        },
        password: {
            presence: {
                message: '^Please enter your password.'
            }
        }
    });

    return result;
};

class SignInPage extends Component {
    constructor(props) {
        super(props);

        this.processSubmit = this.processSubmit.bind(this);
    }

    componentWillMount() {
        // do something like setting default state
    }

    processSubmit(values) {
        // do something with the values
    }

    render() {
        const { handleSubmit, submitting } = this.props;

        return (
            <div className="container mt-5">
                <div className="row justify-content-center">
                    <div className="col-6">
                        <div className="card">
                            <div className="card-body">
                            <h2 className="text-center font-weight-light mb-4">Sign into your account</h2>
                                <form onSubmit={handleSubmit(this.processSubmit)}>
                                    <Field
                                        label="Email Address"
                                        name="email"
                                        component={FormField}
                                        id="email"
                                        type="text"
                                        className="form-control"
                                    />
                                    <Field label="Password" name="password" component={FormField} id="password" type="password" className="form-control" />
                                    <div className="form-check">
                                        <label className="form-check-label">
                                            <Field name="remember" component="input" type="checkbox" className="form-check-input mt-2" value="1" />
                                            Remember me
                                        </label>
                                    </div>
                                    <div className="form-group mt-4">
                                        <button type="submit" className="btn btn-secondary" disabled={submitting}>Continue</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        );
    }
};

SignInPage = reduxForm({
    form: 'signin',
    validate: validatorSignInForm
})(SignInPage);

const rootReducer = combineReducers({
  form: formReducer,
  // my other reducers come here
});

const store = createStore(rootReducer);

class App extends Component {
    constructor(props) {
        super(props);
    }

    render() {
        return (
        <Provider store={store}>
            <BrowserRouter>
                <div>
                    <Header />
                    <Route exact path="/" component={LandingPage} />
                    <Route path="/signin" component={SignInPage} />
                    <Route path="/account/create" component={CreateAccountPage} />
                    <Route path="/dashboard" component={DashboardPage} />
                </div>
            </BrowserRouter>
        </Provider>
        );
    }
};

ReactDOM.render(<App />, document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

# 稍歇-息

到目前为止，我们已经看了很多代码。你一定不知所措。休息一下。吸入空气。回到我们开始处理签到表单的地方。

那么，你有没有注意到我把组件`SignInPage`的 processSubmit 方法留为空？我们现在的目标是看看我们将如何实现它。其实施细分如下:

*   向登录路由发送一个带有值的 AJAX 请求
*   等待回应
*   根据请求结果做某事

现在，为了发送 AJAX 请求，我们将使用`axios`——一个基于 promise 的 AJAX 库。别担心，已经装好了。可以像下面这样开始使用:

```
processSubmit(values) {
    axios
    .post('/ajax/login', values)
    .then( (response) => {
        console.log(response.data);
    })
    .error( (err) => {
        console.log(err);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

在看到请求成功完成之前，需要进行一些设置:

*   您需要将`Auth::routes();`放入我们在`/routes/web.php`的第 1 部分中创建的`ajax`路由组中。
*   您需要确保您的`web.blade.php`中有包含 CSRF 令牌的`<meta>`标记，否则，Laravel 将抛出 TokenMismatchException，而不是处理登录请求。因此，请确保有如下内容:

```
<meta name="csrf-token" content="{{ csrf_token() }}"> 
```

Enter fullscreen mode Exit fullscreen mode

*   默认情况下，`LoginController`中的`AuthenticatesUsers` trait 有一个实现重定向的方法`sendLoginResponse`。问题是我们不想重定向，我们想返回用户的详细信息。因此，复制下面几行代码，并将其粘贴为驻留在`/app/Http/Controllers/Auth/LoginController.php`中的`LoginController`的方法:

```
/**
 * Send the response after the user was authenticated.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return \Illuminate\Http\Response
 */
protected function sendLoginResponse(Request $request)
{
    $request->session()->regenerate();

    $this->clearLoginAttempts($request);

    $user = $this->guard()->user();

    if($this->authenticated($request, $user)) {
        return response()->json([
            'success' => true,
            'user' => $user
        ], 200);
    }
}

/**
 * The user has been authenticated.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  mixed  $user
 * @return mixed
 */
protected function authenticated(Request $request, $user)
{
    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   类似地，`AuthenticatesUsers`特征有`sendFailedLoginResponse`，它也需要被覆盖。所以，把下面的台词抄进`LoginController`:

```
/**
 * Get the failed login response instance.
 *
 * @param  \Illuminate\Http\Request  $request
 * @return \Symfony\Component\HttpFoundation\Response
 */
protected function sendFailedLoginResponse(Request $request)
{
    return response()->json([
        'success' => false,
        'message' => trans('auth.failed')
    ], 422);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您通过提交表单进行测试，您将能够收到 JSON 响应。还是没有？让我知道。

***未完待续三。在第 3 部分中，将继续使用`SignInPage`组件的`processSubmit`。此外，我们还将了解如何使用 redux 管理用户详细信息，防止在访客模式下访问路由等等。***

我之所以要把它分成第三部分，是因为 dev.to post 编辑器因为内容太多而变得很慢，这很烦人！