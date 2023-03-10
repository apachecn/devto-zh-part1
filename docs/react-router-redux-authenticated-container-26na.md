# React 路由器冗余认证容器

> 原文：<https://dev.to/committedsw/react-router-redux-authenticated-container-26na>

使用专用的身份验证组件将身份验证与您的组件分开。

当用 [Redux](https://github.com/reactjs/redux) 和 [react-router-redux](https://github.com/reactjs/react-router-redux) 编写 [React](https://facebook.github.io/react/) 应用程序时，应用程序中的一些路由可能需要认证用户，而其他的则不需要。为了分离身份验证问题，我们使用一个特定的容器来检查身份验证，并在呈现子对象之前进行检查。如果身份验证失败，那么您可以通过显示一个错误或更有可能重定向到登录来做出响应。

## 路线

为了简化 main render 方法，我们使用一个函数将孩子包装在一个`AuthenticatedComponent`中，这样在 main render 方法中就可以清楚地看到应用程序的哪些部分受到了保护。

```
ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      <Route path="/" component={AppParent}>
        <Route path="login" component={LoginContainer}/>
        <Route path="logout" component={LogoutContainer}/>
        <Route path="about" component={AboutPage}/>
        <Route path="help" component={HelpPage}/>
        <Route path="user" component={requireAuth(UserContainer)}/>
        <Route path="admin" component={requireAuth(AdminContainer)}/>
        <Route path="main" component={requireAuth(MainContainer)}>
          <Route path="one" component={FirstPage}/>
          <Route path="two" component={SecondPage}/>
        </Route>
      </Route>
    </Router>
  </Provider>,
  document.getElementById('app')
) 
```

## 认证组件

然后我们如下实现`requireAuth`函数。您可能需要根据您的身份验证操作来更改身份验证检查的细节，并且您可以让`authFailed`函数做您喜欢的任何事情，在本例中，我们使用路径重定向到登录，这样我们可以在成功登录后重定向回来。

```
import React, { Component, PropTypes } from 'react'
import { connect } from 'react-redux'
import { push } from 'react-router-redux'

export function requireAuth(ChildComponent) {

  class AuthenticatedComponent extends Component {

    componentWillMount() {
      this.checkAuth(this.props.isAuthenticated);
    }

    componentWillReceiveProps(nextProps) {
      this.checkAuth(nextProps.isAuthenticated);
    }

    checkAuth(isAuthenticated) {
      if (!isAuthenticated) {
        this.props.authFailed();
      }
    }

    render() {
      return (
        <div>
         { this.props.isAuthenticated === true ?  
           <ChildComponent { ...this.props } /> : null }
        </div>
      )

    }
  }

  const mapStateToProps = (state) => ({
    auth: state.auth,
    isAuthenticated: state.auth.login.isAuthenticated,
  });

  const mapDispatchToProps = (dispatch, ownProps) => {
    return {
      authFailed: () => {
        let location = ownProps.location
        let redirect = encodeURIComponent(location.pathname + location.search)
        dispatch(push(`/login?next=${redirect}`))
      },
    }
  }

  return connect(mapStateToProps, mapDispatchToProps)(AuthenticatedComponent);

} 
```

auth 细节对于子组件可能很有用，比如显示用户名，但并不是所有的子组件都需要它。一个好的方法是让它成为孩子的上下文对象。这可以通过在类中添加以下内容来实现:

```
class AuthenticatedComponent extends Component {

  \\...

  static childContextTypes = {
    auth: PropTypes.object.isRequired
  }

  getChildContext() {
    return {
      auth: this.props.auth
    }
  }

  \\...
} 
```