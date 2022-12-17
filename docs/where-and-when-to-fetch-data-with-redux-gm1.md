# 何时何地使用 Redux 获取数据

> 原文：<https://dev.to/dceddia/where-and-when-to-fetch-data-with-redux-gm1>

> 如果一个组件需要数据来进行渲染，并且您想用 Redux 获取数据并保存在 Redux 存储中，那么什么时候是进行 API 调用的最佳时机呢？

TL；dr-**开始`componentDidMount`生命周期挂钩**中的行动

## 这是如何工作的，用代码表示

假设您想要显示产品列表。您已经有了一个响应于`GET /products`的后端 API，因此您创建了一个 Redux 动作来执行获取:

productActions.js

```
export function fetchProducts() {
  return dispatch => {
    dispatch(fetchProductsBegin());
    return fetch("/products")
      .then(handleErrors)
      .then(res => res.json())
      .then(json => {
        dispatch(fetchProductsSuccess(json.products));
        return json.products;
      })
      .catch(error => dispatch(fetchProductsFailure(error)));
  };
}

// Handle HTTP errors since fetch won't.
function handleErrors(response) {
  if (!response.ok) {
    throw Error(response.statusText);
  }
  return response;
} 
```

Enter fullscreen mode Exit fullscreen mode

边注: **`fetch()`不抛出 HTTP 错误**。如果你习惯了像 [axios](https://github.com/axios/axios) 这样的东西，这真的很令人困惑。阅读[此处](https://www.tjvantoll.com/2015/09/13/fetch-and-errors/)了解更多关于获取和错误处理的信息。

获取数据的 Redux 动作通常会围绕 API 调用编写开始/成功/失败动作。这不是一个要求，但它很有用，因为它让您跟踪正在发生的事情——比方说，通过设置一个“loading”标志`true`来响应 Begin 操作，然后在成功或出错后设置`false`。这些动作如下所示:

productActions.js

```
export const FETCH_PRODUCTS_BEGIN = 'FETCH_PRODUCTS_BEGIN';
export const FETCH_PRODUCTS_SUCCESS = 'FETCH_PRODUCTS_SUCCESS';
export const FETCH_PRODUCTS_FAILURE = 'FETCH_PRODUCTS_FAILURE';

export const fetchProductsBegin = () => ({
  type: FETCH_PRODUCTS_BEGIN
});

export const fetchProductsSuccess = products => ({
  type: FETCH_PRODUCTS_SUCCESS,
  payload: { products }
});

export const fetchProductsError = error => ({
  type: FETCH_PRODUCTS_FAILURE,
  payload: { error }
}); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们会让 reducer 在收到`FETCH_PRODUCTS_SUCCESS`动作时将产品保存到 Redux store 中。当获取开始时，它还将设置一个`loading`标志为真，当获取完成或失败时，设置为假。

product reducer . js〔t0〕

```
import {
  FETCH_PRODUCTS_BEGIN,
  FETCH_PRODUCTS_SUCCESS,
  FETCH_PRODUCTS_FAILURE
} from './productActions';

const initialState = {
  items: [],
  loading: false,
  error: null
};

export default function productReducer(state = initialState, action) {
  switch(action.type) {
    case FETCH_PRODUCTS_BEGIN:
      // Mark the state as "loading" so we can show a spinner or something
      // Also, reset any errors. We're starting fresh.
      return {
        ...state,
        loading: true,
        error: null
      };

    case FETCH_PRODUCTS_SUCCESS:
      // All done: set loading "false".
      // Also, replace the items with the ones from the server
      return {
        ...state,
        loading: false,
        items: action.payload.products
      };

    case FETCH_PRODUCTS_ERROR:
      // The request failed, but it did stop, so set loading to "false".
      // Save the error, and we can display it somewhere
      // Since it failed, we don't have items to display anymore, so set it empty.
      // This is up to you and your app though: maybe you want to keep the items
      // around! Do whatever seems right.
      return {
        ...state,
        loading: false,
        error: action.payload.error,
        items: []
      };

    default:
      // ALWAYS have a default case in a reducer
      return state;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们只需要将产品传递到一个显示它们的`ProductList`组件中，并负责开始数据获取。

ProductList.js

```
import React from "react";
import { connect } from "react-redux";
import { fetchProducts } from "productActions";

class ProductList extends React.Component {
  render() {
    if (error) {
      return <div>Error! {error.message}</div>;
    }

    if (loading) {
      return <div>Loading...</div>;
    }

    return (
      <ul>
        {products.map(product =>
          <li key={product.id}>{product.name}</li>
        )}
      </ul>
    );
  }
}

const mapStateToProps = state => ({
  products: state.products.items,
  loading: state.products.loading,
  error: state.products.error
});

export default connect(mapStateToProps)(ProductList); 
```

Enter fullscreen mode Exit fullscreen mode

## 但是会渲染两次！

这是一个非常普遍的问题。是的，它会不止一次的渲染。

它会在空的状态下渲染，然后在加载状态下重新渲染，再重新渲染*再*有产品要展示。恐怖啊！3 渲染！(如果您直接跳到“加载”状态，您可以将它减少到 2)

你可能会因为性能而担心“不必要的”渲染，但不要担心:单次渲染非常快。如果你正在开发一个应用程序，他们很难注意到，做一些分析，找出为什么会这样。

可以这样想:当没有产品时，或者当它们正在加载时，或者当出现错误时，应用程序需要显示一些东西。您可能不想在数据准备好之前只显示一个空白屏幕。这给了你一个做决定的好机会。

## 但是组件不该取！

从架构的角度来看，如果有一个父“东西”(组件、函数、路由器或其他什么)在加载组件之前自动获取数据，那就更好了。然后，组件可以幸福地不知道任何肮脏的 API 废话；他们可以简单地等待，等待别人把数据放在银盘子里递给他们。多好的生活啊！

有很多方法可以解决这个问题，但是和所有事情一样，也有权衡。神奇的数据加载器是神奇的(更难调试，更难记住它们如何/何时/为什么工作)。他们可能需要更多而不是更少的代码。

## 剥这只猫皮的多种方法

有许多方法可以分解这个代码。没有“最好的方法”，因为这些东西存在于一个范围内，并且因为对一个用例来说“最好的”可能对另一个用例来说是“最差的”。

“获取`componentDidMount`中的数据”并不是*中的一种真正的方式*，但是它很简单，而且它完成了工作。

但是，如果你不喜欢这样做，你可以尝试以下方法:

*   将 API 调用移出 Redux 动作，移入一个`api`模块，并从动作中调用它。(更好地分离关注点)
*   让组件直接调用 API 模块，然后让它在数据返回时调度动作，[就像 Dan Abramov 在这个视频](https://egghead.io/lessons/javascript-redux-dispatching-actions-with-the-fetched-data)中展示的那样。
*   使用像 [redux-dataloader](https://github.com/kouhin/redux-dataloader) 或 [redux-async-loader](https://github.com/recruit-tech/redux-async-loader) 或[马克·埃里克森的数据提取库列表中的其他库之一](https://github.com/markerikson/redux-ecosystem-links/blob/master/component-data-fetching-preloading.md)这样的库
*   制作一个包装器组件来进行获取——在上面的例子中，它可能被称为`ProductListPage`。然后“页面”负责取数据，“列表”只接受数据并呈现出来。
*   使用[重组](https://github.com/acdlite/recompose)将`componentDidMount`钩子拉出到它自己的高阶包装器组件中

就像我说的，有很多方法可以做到这一点:)

## 动作步骤

试着自己实现吧！实践是最好的学习方法。

如果你没有自己的后端服务器，就使用 Reddit——如果你追加，他们的 URL 会返回 JSON。json”到底，如[www.reddit.com/r/reactjs.json](//www.reddit.com/r/reactjs.json)。

制作一个小小的 React + Redux 应用程序，显示来自/r/reactjs 的帖子。

[何时何地用 Redux](https://daveceddia.com/where-fetch-data-redux/) 取数据最初由戴夫·塞德迪亚于 2018 年 2 月 02 日在[戴夫·塞德迪亚](https://daveceddia.com)发布。

[代码项目](http://www.codeproject.com)