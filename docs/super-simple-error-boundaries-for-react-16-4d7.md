# React 16+的超级简单误差边界

> 原文：<https://dev.to/fixitup2/super-simple-error-boundaries-for-react-16-4d7>

# `<EitheRx />`

React 的超级简单、可重复使用的二进制错误边界。你要去的，规定的，错误边界的助手。

## 用法

```
$ npm i --save eitherx 
```

Enter fullscreen mode Exit fullscreen mode

```
import Either from 'eitherx'

// Wrapper-component Style
<Either>
  {/* Either . . . */}
  <p>"Happy Child ðŸ˜„"</p>
  {/* Or . . . */}
  <p>"Error child â˜¹ï¸ (but at least your UI is happy)"</p>
</Either>

// OR use the `render` and `catchError` props

// Render-prop Style
<Either
  render={() => (<p>"Happy Child ðŸ˜„"</p>)}
  catchError={({ error, info }) => (
    <div>
      <p>{`Info: ${info}`}</p>
      <p>{`Error: ${error}`}</p>
    </div>
  )}
/> 
```

Enter fullscreen mode Exit fullscreen mode

### 包装组件样式

`Eitherx`要么渲染第一个子组件，除非渲染时出现错误，然后渲染第二个子组件。很简单，ðÿ˜„.

如果没有传递第二个子节点，并且出现错误，`null`将被返回以进行 React 渲染。

### 渲染-道具风格

使用这种风格，你必须为`render` *和* `catchError`传递一个函数。如果出现错误，从`catchError`返回的组件
将被渲染。否则`Eitherx`会渲染从`render`返回的组件。

`catchError`函数接收一个带有字段`error`和`info`的对象，这两个字段都是直接从
[React 错误边界](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html#introducing-error-boundaries)传递过来的。

### 处理错误

无论哪种风格，您都可以传递一个`handleError`回调属性来添加额外的错误处理、日志记录等。这也允许您通过从`handleError`返回 false 来
过滤某些错误。

##### 举例

```
<Eitherx
  handleError={({ error, info }) => {
    console.log(info)
    console.error(error)
  }}
  render={() => (<Component>"Render Prop"</Component>)}
  catchError={() => (<p>"Catch Prop"</p>)}
/> 
```

Enter fullscreen mode Exit fullscreen mode