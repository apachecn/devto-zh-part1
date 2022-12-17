# 将 React ErrorBoundary 与 HOCs 一起使用

> 原文：<https://dev.to/tdkn/using-react-errorboundary-with-hocs-1ml5>

```
// withErrorBoundary.js

import React, { Component } from 'react'
import PropTypes from 'prop-types'

class ErrorBoundary extends Component {
  constructor(props) {
    super(props)
    this.state = { hasError: false }
  }

  componentDidCatch(error, info) {
    this.setState({ hasError: true })
    console.group('componentDidCatch')
    console.error(error)
    console.info(info)
    console.groupEnd('componentDidCatch')
  }

  render() {
    if (this.state.hasError) {
      return <h1>An Error Occurred 😢</h1>
    }
    return this.props.children
  }
}

export default Component => props => (
  <ErrorBoundary>
    <Component {...props} />
  </ErrorBoundary> ) 
```

Enter fullscreen mode Exit fullscreen mode

易于与 ES7 装饰器一起使用...`@withErrorBoundary`

```
// App.js

import withErrorBoundary from './withErrorBoundary.js'

@withErrorBoundary
export default class App extends Component {

  simulateError = () => {
    throw new Error('Sample Error')
  }

  render() {
    return (
      <div>
        <h1 onClick={this.simulateError}>Hello World</h1>
      </div>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode