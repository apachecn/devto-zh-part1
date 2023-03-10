# 借助 async/await 在 2018 年实现 ReactJS 应用的现代化

> 原文：<https://dev.to/codeprototype/modernize-your-reactjs-application-with-asyncawait-in-2018-1l9j>

从 2018 年开始，您可以通过整合 async/await 特性来实现 ReactJS 应用的现代化。假设您已经使用了基于 Promise 的 HTTP 客户端库，如 [axios](https://github.com/axios/axios) ，您可以轻松地重构现有代码，或者您可以在新代码中开始使用 async/await。

不管是哪种方式还是两种方式，这里有一个小片段可以帮助你:

```
import axios from 'axios';
...
class Login extends Component {
    constructor(props, context) {
        super(props, context);
        this.onLogin = this.onLogin.bind(this);
        ...
    }
    async onLogin() {
        const { email, password } = this.state;
        try {
            const response = await axios.post('/login', { email, password });
            console.log(response);
        } catch (err) {
            //handle error
        }
    }
    ...
} 
```

所以代码变化非常小。但是运行 **webpack** 可能会导致错误。该错误可能会类似于 *regeneratorRuntime 未定义*。在这种情况下，这是一个简单的修复:

```
npm install babel-plugin-transform-runtime --save-dev 
```

然后在。babelrc 文件，简单加

```
{
    ...
    "plugins": [
        ["transform-runtime", {
            "regenerator": true
        }]
    ]
} 
```

除了添加**babel-plugin-transform-runtime**和对。babelrc 文件，没有 **babel-runtime** ，没有 **polyfill** 是必须的。