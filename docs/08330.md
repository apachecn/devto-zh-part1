# Mobx 走进一个 package.json

> 原文：<https://dev.to/joeymink/mobx-walks-into-a-packagejson>

在几个小的 React 应用之后，我们决定攻击一个大的。它将从我们的服务器端 API 获得更多的数据。这些数据将由许多组件共享。以前的经验告诉我们，将数据传播给所有感兴趣的组件是很繁琐的。我们的方法通常是:

1.  从顶层组件中获取数据，`setState`获取结果。
2.  将获取处理程序从顶级组件传递到子组件。
3.  通过属性将顶级组件的获取结果传递给子组件。

过分简化(去掉我头顶上的假 j):

```
class Data {
  luckyNumber = 0;

  fetchLuckyNumber(callback){
    apiCall('/api/lucky_number', callback);
  }
}

<TopLevelComponent data={new Data()}/> 
class TopLevelComponent extends React.Component {

  /* plumbing */
  fetchLuckyNumber(){
    data.fetchLuckyNumber((num) => (this.setState({ lucky_number: num }));
  }

  render() {
    return <SubComponent data={this.props.data}
             /* plumbing */
             fetchLuckyNumber={this.fetchLuckyNumber} />
  }
}

class SubComponent extends React.Component {
  render() {
    return <p>
             Lucky number: {this.props.data.luckyNumber}!
             <a onClick={this.props.fetchLuckyNumber}>Update Lucky Number</a>
           </p>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

从这个人为的例子中得到的启示很少。*首先*，应用程序的所有数据获取都是由 TopLevelComponent 发起的。*第二个*，当 TopLevelComponent 设置状态(触发子组件的呈现)时，分发获取的数据。*第三个*，子组件必须从父组件接收处理程序来触发获取和分发。这是可行的，但是很冗长——如果有很多嵌套的子组件，情况会变得更糟。

有一些库可以帮助解决这个问题，我的一个同事推荐了 Mobx。ZOMG，我很高兴他这么做了。原因如下:

```
class Data {
  @observable luckyNumber = 0;

  fetchLuckyNumber(callback){
    /* luckyNumber assignment triggers Mobx's magic */
    apiCall('/api/lucky_number', (num)=>(this.luckyNumber = num);
  }
}

<TopLevelComponent data={new Data()}/> 
class TopLevelComponent extends React.Component {
  render() {
    return <SubComponent data={this.props.data} />
  }
}

@observer
class SubComponent extends React.Component {
  render() {
    /* Referencing luckyNumber automatically makes this component reactive */
    return <p>
             Lucky number: {this.props.data.luckyNumber}!
             <a onClick={this.props.data.fetchLuckyNumber}>Update Lucky Number</a>
           </p>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以在上面，每次 data.luckyNumber 的值改变时，SubComponent 都会重新呈现。Mobx 将确保调用 SubComponent 的 render 方法，这使得管道变得更加容易(或者更好地说，不存在？).

I ðŸ’•.