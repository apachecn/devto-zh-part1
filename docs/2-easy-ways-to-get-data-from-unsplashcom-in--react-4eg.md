# 在 React 中从 Unsplash.com 获取数据的两种简单方法

> 原文：<https://dev.to/createdd/2-easy-ways-to-get-data-from-unsplashcom-in--react-4eg>

[![](img/19ec1d06dabe38b678778f653301a370.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--fBaIHLn6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1497493292307-31c376b6e479%3Fdpr%3D2%26auto%3Dformat%26fit%3Dcrop%26w%3D1199%26h%3D799%26q%3D80%26cs%3Dtinysrgb%26crop%3D%26bg%3D)

*[【un flash . com/photos/k _ t9zj3se 8k](https://unsplash.com/photos/k_T9Zj3SE8k)*

因为 Unsplash.com 发布了他们的 API，而我非常喜欢他们的内容，所以我决定写一篇关于如何使用 React 的短文。Unsplash 太牛逼了！:)尽情享受。

[Github 回购可在此处获得…](https://github.com/DDCreationStudios/fetchingInReact)

* * *

> ## "All negative things-stress, challenges-are my opportunities to rise." -Kobe Bryant

## 设置基础知识

为了建立基础，我使用了我做的另一个项目的代码库，使用:

*   创建-反应-应用
*   React 组件主要在列表中呈现图像

## 用获取 API 获取数据

*   像这样使用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) :

```
componentDidMount() {
    fetch('https://api.unsplash.com/photos/?client_id=' + cred.APP_ID)
        .then(res => res.json())
        .then(data => {
            this.setState({ imgs: data });
        })
        .catch(err => {
            console.log('Error happened during fetching!', err);
        });
} 
```

Enter fullscreen mode Exit fullscreen mode

*   获取数据时使用`componentDidMount`生命周期(用 DOM 表示)
*   描述一个使用 Promise 功能的获取方法
*   将调用转换成 JSON 对象，并将其传递给 state
*   之后，只需从获取的链接中渲染出每个图像

[查看这些步骤后的 Github 回购…](https://github.com/DDCreationStudios/fetchingInReact/tree/basicFetch)

## 使用库获取数据

抓取也可以由许多库中的一个来完成。我将使用 [axios](https://github.com/mzabriskie/axios) ，因为它提供了一些很酷的功能，如:

*   从浏览器发出 XMLHttpRequests
*   从 node.js 发出 http 请求
*   支持 Promise API
*   拦截请求和响应
*   转换请求和响应数据
*   取消请求
*   JSON 数据的自动转换
*   防止 XSRF 的客户端支持

所以接下来的步骤是:

*   添加 axios 包
*   只需将 fetch 方法修改为 axios 包中的方法

```
componentDidMount() {
    axios
        .get('https://api.unsplash.com/photos/?client_id=' + cred.APP_ID)
        .then(data => {
            this.setState({ imgs: data.data });
        })
        .catch(err => {
            console.log('Error happened during fetching!', err);
        });
} 
```

Enter fullscreen mode Exit fullscreen mode

非常简单，效果很好:)

[查看这些步骤后的 Github 回购…](https://github.com/DDCreationStudios/fetchingInReact/tree/basicFetch)

#### 长这样:

[![](img/ebad4760051b9eee7896f2a6a6d32360.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LlbNt8eM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../assets/RFETCH/Screenshot1.png)

## 添加搜索功能

*   修改获取的链接(添加查询和搜索参数)
*   使您的请求动态化，将搜索查询连接到您的应用程序
*   添加 searchbar 组件
*   确保绑定所有方法(使用箭头函数或手动绑定它们)

```
performSearch = query => {
  axios
    .get(
      `https://api.unsplash.com/search/photos/?page=1&per_page=10&query=${query}&client_id=${cred.APP_ID}`
    )
    .then(data => {
      this.setState({ imgs: data.data.results });
    })
    .catch(err => {
      console.log('Error happened during fetching!', err);
    });
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 润色 React 代码

*   为输入使用 ref-属性

```
this.props.onSearch(this.query.value);
--------
ref={input => (this.query = input)} 
```

Enter fullscreen mode Exit fullscreen mode

*   为 performSearch 方法设置一个默认值，并将 performSearch 放入 componentDidMount 生命周期中
*   当使用 if 语句找不到图像时，渲染出不同的组件
*   当提取未完成时，使用条件呈现来呈现段落(将一个标志设置为状态，并在 fetch 方法中更改它)

```
<div className="main-content">
    {this.state.loadingState
        ? <p>Loading</p>
        : <ImgList data={this.state.imgs} />} </div> 
```

Enter fullscreen mode Exit fullscreen mode

[查看这些步骤后的 Github 回购…](https://github.com/DDCreationStudios/fetchingInReact/tree/axiosSearch)

* * *

这非常简单，已经展示了您可以用 API 做多少事情:)

* * *

## 适应 Unsplash 准则

当使用 API 时，一定要阅读他们的指导方针。Unsplash API 指南

因此，作为一个例子，我不相信 Unsplash 或摄影师。因此，我必须通过从数据中检索更多信息来改进我的应用程序，并向所有者添加积分:

```
const Img = props =>
    <li>
        <a href={props.link}>
            <img src={props.url} alt="Unsplash Image here" />
        </a>
        <p>
            Photo by
            <a href={props.user}>{props.name}</a>
            <a href={props.link}> See on Unsplash</a>
        </p>
    </li>; 
```

Enter fullscreen mode Exit fullscreen mode

#### 现在的样子

[![](img/9e9ef615f6700c0e530a60f44f3f62df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HjtSHWjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/../assets/RFETCH/Screenshot2.png)

[在 Github 上看到完成的应用程序…](https://github.com/DDCreationStudios/fetchingInReact)

## 有用链接&学分

*   [Unsplash.com](https://unsplash.com/)
*   我参加了一个树屋课程，其中涵盖了一个采用类似方法的应用程序:

[![](img/fa056e5d9e8f6d340fc39df801a0e936.png)T2】](http://referrals.trhou.se/danieldeutsch3)

如果你从这篇文章中有所收获，请告诉我你的意见或想法。请确保关注更多内容:)