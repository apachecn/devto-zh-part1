# 按流类型重组和还原的静态特设分析

> 原文：<https://dev.to/mizchi/static-hoc-analysis-for-recompose-and-redux-by-flowtype-39h2>

[流量@60.1](mailto:flow@60.1)

需要这些定义

*   [https://github . com/ACD lite/re compose/blob/master/types/flow-typed/re compose _ v 0.24 . x/flow _ v 0.55 . x-/re compose _ v 0.24 . x . js](https://github.com/acdlite/recompose/blob/master/types/flow-typed/recompose_v0.24.x/flow_v0.55.x-/recompose_v0.24.x.js)
*   [https://github . com/flow type/flow-typed/blob/master/definitions/NPM/redux _ v3 . x . x/flow _ v 0.55 . x-/redux _ v3 . x . x . js](https://github.com/flowtype/flow-typed/blob/master/definitions/npm/redux_v3.x.x/flow_v0.55.x-/redux_v3.x.x.js)

```
/* @flow */
import React from 'react'
import { bindActionCreators, combineReducers } from 'redux'
import { compose, lifecycle, pure, type HOC } from 'recompose'
import { connect } from 'react-redux'

// reducer
const INC = 'inc'

const inc = () => ({ type: INC })

type Counter = {
  value: number
}

type Action = $Call<typeof inc>

const counter = (state: Counter = { value: 0 }, action: Action): Counter => {
  switch (action.type) {
    case INC: {
      return { value: state.value + 1 }
    }
    default: {
      return state
    }
  }
}

const rootReducer = combineReducers({ counter })

// Get RootState from result of rootReducer
type RootState = $Call<typeof rootReducer>

// compose HOCs

type OuterProps = {
  foo: string
}

type Props = {
  foo: string,
  bar: number,
  actions: {
    inc: Function
  }
}

const connector = connect(
  (state: RootState, props) => {
    return {
      foo: props.foo,
      bar: state.counter.value
    }
  },
  dispatch => bindActionCreators({ inc }, dispatch)
)

const enhancer: HOC<Props, OuterProps> = compose(
  connector,
  pure,
  lifecycle({
    componentDidMount() {
      console.log('mounted')
    }
  })
)

// props is resolved by hoc
const MyComponent = enhancer(props => {
  return (
    <div>
      {props.foo}
      {props.bar}
      {props.actions.inc}
    </div>
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 在 flowtype 上检查它们

您可以检查和完成 mapStateToProps 和 MyComponent 的 Props 的部分代码。