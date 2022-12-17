# 用于编辑的 React 表单应该更新底层模型吗？

> 原文：<https://dev.to/goshacmd/should-a-react-form-for-editing-update-the-underlying-model>

在一个想象的世界中，表单只是用来从头开始输入一些东西，您对 React 中表单的了解可能就足够了。然而，在现实世界中，表单通常是活跃的生物——您可以返回到它们并更改您的数据。

到目前为止，我们的表单是相当独立和孤立的。

用于输入新数据的表单是这样开始的:

```
constructor() {
  super();
  this.state = {
    email: '',
    password: '',
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

而他们用的大概是这样的东西:

```
<SignUpForm /> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到它们没有收到任何道具，它们将当前值保存在状态中，并用空值初始化状态。如果您所需要的只是通过表单收集全新的数据，这是非常完美的。

但是，如果你有一个不同的形式…例如，编辑一篇文章。它将不得不接受目前的名称和机构。大概是这样:

```
<PostForm title={someTitle} body={someBody} /> 
```

Enter fullscreen mode Exit fullscreen mode

除此之外，现在有几种可能性可以进一步发展:

*   使表单本身完全受控于其父表单。它意味着*而不是*保持自己的状态，表单总是要接收字段值，以及改变它们的回调。就像受控/非受控输入一样，这将使整个表单受控。

```
 <PostForm
    title={...}
    body={...}
    onChangeTitle={...}
    onChangeBody={...}
  /> 
```

Enter fullscreen mode Exit fullscreen mode

然而，这种情况并不常见。只有一小部分形式需要有这样的东西。当您对表单所做的更改需要立即反映在应用程序的其余部分时，可以使用它。

它也不是没有缺点。这样做意味着无效的未保存数据现在有了一种离开表单的方法:如果您直接用正在进行的表单值更新一些全局状态，您的应用程序可能会消耗部分或错误的数据。

*   **通过 props 初始化表单状态。**表单仍然有自己的状态和输入值。它将简单地使用传递的字段值作为起点。

用作:

```
 <PostForm title={someTitle} body={someBody} /> 
```

Enter fullscreen mode Exit fullscreen mode

表单将接受初始属性，这些属性将被传递给它的`constructor`，并根据它们设置初始状态:

```
 class PostForm extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        title: props.title || '',
        body: props.body || '',
      };
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

在某种程度上，这类似于使窗体相对于其父窗体“不受控制”。它维护自己的状态，并使用传递的属性作为字段的默认值。

提交表单时，新值应传达给家长:

```
 <PostForm
    title={someTitle}
    body={someBody}
    onSubmit={(newTitle, newBody) => { ... }}
  /> 
```

Enter fullscreen mode Exit fullscreen mode

与单个输入不同，让表单“不受控制”是制作表单的首选方法。表单不是直接改变一件事情的方法，相反，它是一个*请求*去改变一件事情。

想想改名字的文书工作。它不会在你填写的时候直接改变你的名字；它不会“绑定”到你的名字上。它只是用一些数据(你现在的名字)初始化，你填好新名字，拿给官僚，它才起作用。

网络上的表单也是如此——它们只有在提交后才会做一些事情。

*本帖最初发表于[goshakk . name](https://goshakkk.name/react-forms-for-editing/)T3】*

* * *

我专门写了 React 中的表单和其他与 React 相关的东西。
如果你喜欢你在这里看到的，[订阅这里](http://goshakkk.name/subscribe/)以确保你不会错过我的下一篇文章。