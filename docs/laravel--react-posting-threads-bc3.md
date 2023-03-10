# Laravel + React:发布主题

> 原文：<https://dev.to/nathanheffley/laravel--react-posting-threads-bc3>

*原贴于[nathanheffley.com](https://www.nathanheffley.com/blog/laravel-react-posting-threads)T3】*

这是如何使用 Laravel 和 React 创建论坛系列课程的第一课。如果你还没看过我们[策划论坛](https://www.nathanheffley.com/blog/laravel-react-planning-a-forum)的介绍，你应该先看看！

要开始使用论坛，我们首先需要的是创建帐户、登录和创建主题的能力。幸运的是，Laravel 附带了 Artisan `make:auth`命令，我们将充分利用这一点。因为这在其他地方已经讨论得很透彻了，比如在 [Laravel 认证文档](https://laravel.com/docs/5.5/authentication)中，我就不再赘述了。我只是简单地运行了`php artisan make:auth`命令，并没有修改任何相关文件。

## 创建新线程

创建论坛的第一个目标是允许用户发布帖子。使用默认认证系统，我们得到一个`/home`路由和寻呼。我们将利用这一点向用户显示重要信息，比如他们创建的线程和收到的通知。不过现在，我们将添加一个带有表单的侧边栏来创建一个新页面。该模板非常简单，所以用我们的新主页替换自动生成的模板中的内容。

```
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row">
        <div class="col-md-5 col-md-offset-7">
            <div id="create-thread"></div>
        </div>
    </div>
</div>
@endsection 
```

Enter fullscreen mode Exit fullscreen mode

id 为`create-thread`的 div 是我们的 React 表单将要去的地方。对于像表单这样简单的东西使用 React 可能看起来有点矫枉过正，但是它将允许我们在前进的过程中添加额外的功能，我们可以使用它来提交 Ajax 请求，而不是强迫用户加载一个全新的页面。随着我们向该页面添加更多组件，您将会看到对所有这些交互式部分使用 React 是多么有用。

在我们继续创建实际的 React 代码之前，让我们快速浏览一下将驱动新线程创建的后端代码。

## 后端功能

为了实际创建线程，我们需要进行数据库迁移。目前，我们只需要一个`author_id`列来引用谁创建了这个线程，以及一个标题和内容列。将来我们会添加更多的专栏，但这已经足够让我们开始了。你可以在 GitHub 上查看[的完整迁移。如果您正在跟进，请确保创建该迁移(我建议只运行`php artisan make:migration create_threads_table --create=threads`并编辑它以匹配我的文件)并刷新您的数据库。](https://github.com/NathanHeffley/react-forum/blob/251939322e9d0ad9558721907f8809ac11bd442e/database/migrations/2018_01_11_193047_create_threads_table.php)

因为线程将是我们将要与之交互的实际模型，所以我们也需要一个模型。从简单开始，我放入模型的唯一东西是一个`$fillable`属性，这样我们可以避免大量赋值异常和访问线程作者的雄辩关系(当然我也在用户类中添加了匹配关系，你可以[查看这里的不同](https://github.com/NathanHeffley/react-forum/commit/1bde7f36b7d0bc809c6ee85279766dbbf53d99cb#diff-36f1184e0453df972dab51127e08458f))。[这里是线程类](https://github.com/NathanHeffley/react-forum/blob/251939322e9d0ad9558721907f8809ac11bd442e/app/Thread.php)。

最后，如果我们没有一个控制器来处理传入的请求，所有这些都没有用。你可以用`php artisan make:controller ThreadsController`创建一个空白的控制器，然后添加这几个函数。

这个非常简单，只显示一个线程。

```
<?php

public function show(Thread $thread)
{
    return view('threads.show', compact('thread'));
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们实际存储新线程的地方。首先，我们验证标题和内容数据值是否存在。然后，我们实际上用这些值创建线程，并将`author_id`设置为当前已验证用户的 id。

```
<?php

public function store(Request $request)
{
    $request->validate([
        'title' => 'required',
        'content' => 'required',
    ]);

    $thread = Thread::create([
        'author_id' => \Auth::user()->id,
        'title' => $request->title,
        'content' => $request->content
    ]);

    return response($thread, 201);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在你已经看到了两个有用的函数，回到控制器的顶部，添加一个构造函数。这只是为了确保只有经过身份验证的用户才能发出创建线程的 POST 请求。因为我们将来会增加更多的功能，所以我决定最好只要求对所有东西进行认证，除了简单地查看一个线程。

```
<?php

public function __construct()
{
    $this->middleware('auth')->except('show');
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们只需要一些创建和查看线程以及调用这些控制器函数的路径。将这两条路线放到您的`route/web.php`文件中来处理。

```
<?php

Route::get('/threads/{thread}', 'ThreadsController@show');
Route::post('/threads', 'ThreadsController@store'); 
```

Enter fullscreen mode Exit fullscreen mode

## 用 React 创建线程

既然后端已经设置完毕，可以开始工作了，让我们创建表单组件吧！

首先，进入你的`resources/assets/js`文件夹，打开`app.js`。在那里，您将看到一条引入示例组件的线。继续修改它，这样它会拉入一个更具描述性的文件。

```
require('./components/CreateThread'); 
```

Enter fullscreen mode Exit fullscreen mode

进入 components 文件夹，将 Example.js 文件重命名为 CreateThread.js。

默认情况下，您应该会看到文件导入反应和 ReactDOM。因为我们要从这个组件发出 Ajax 请求，所以我们还需要 Axios(在每个 Laravel 项目中默认配置)。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';
import axios from 'axios'; 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将更新文件中默认的内容，然后添加我们自己的函数来获得我们需要的功能。确保将 Example 中的类名定义更新为 CreateThread，并在最后对 ReactDOM.render 进行同样的操作。我们需要检查页面是否有`create-thread` id 元素，我们将在任何想要加载该组件的地方使用该元素。因为一个页面只会有其中一种形式，这个简单的检查就可以了，并且是我们第一个
的完美起点

```
export default class CreateThread extends Component {
    // Omitted for brevity right now.
}

if (document.getElementById('create-thread')) {
    ReactDOM.render(<CreateThread />, document.getElementById('create-thread'));
} 
```

Enter fullscreen mode Exit fullscreen mode

既然基本的设置工作已经完成，我们可以开始重写渲染方法了。我们将让组件总是把自己作为一个独立的面板输出。这段代码的大部分只是引导 div 和样式。需要注意的重要事情是 input 和 textarea 元素上的`onChange`属性，以及按钮上的`onClick`属性。

```
render() {
    return (
        <div className="panel panel-default">
            <div className="panel-heading">Create a New Thread</div>

            <div className="panel-body">
                <form>
                    <div className="form-group">
                        <label htmlFor="new-thread-title">Title</label>
                        <input id="new-thread-title" className="form-control" onChange={this.changeTitle}></input>
                    </div>
                    <div className="form-group">
                        <label htmlFor="new-thread-content">Title</label>
                        <textarea id="new-thread-content" className="form-control" onChange={this.changeContent} rows="8"></textarea>
                    </div>
                </form>
            </div>

            <div className="panel-footer">
                <button className="btn btn-primary" onClick={this.postThread}>
                    Publish
                </button>
            </div>
        </div>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经处理好了渲染函数，我们需要创建那些`changeTitle()`、`changeContent()`和`postThread()`函数。

标题和内容函数是最简单的，因为它们只是更新组件状态。由于如何处理`onChange`事件属性，React 会自动将`event`参数传递给函数。对于`event`对象，我们只需选择`target`，它将是 input 或 textarea 元素，然后从中选择值。

```
changeTitle(event) {
    this.setState({title: event.target.value});
}

changeContent(event) {
    this.setState({content: event.target.value});
} 
```

Enter fullscreen mode Exit fullscreen mode

`postThread()`函数稍微复杂一点，但是我们将从最基本的 Axios 调用开始，我们现在可以离开这个调用。当我们添加一些新的特性和选项来发布帖子时，我们将在将来回到这个函数。

```
postThread() {
    axios.post('/threads', {
        title: this.state.title,
        content: this.state.content
    })
    .then(response => {
        this.setState({title: '', content: ''});
        document.getElementById('new-thread-title').value = '';
        document.getElementById('new-thread-content').value = '';
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你以前用过 Axios，那么这个功能会非常简单。即使你们以前没用过，我相信你们都很聪明，一定能弄明白。我们只是使用 Axios 向我们站点上的`/threads`路径发出一个 POST 请求，我们在前面设置了这个路径来接受 POST 请求并将它们发送给 ThreadsController。在那个 POST 请求中，我们传递存储在组件状态中的标题和内容数据(由`changeTitle`和`changeContent`函数放在那里)。Axios 使用[承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)工作，以便在请求完成并收到响应后，调用`then`函数。在其中，我们将组件状态重置为空值，并清除标题和内容输入。

我们需要的这个控制器的最后一点是一个构造函数。我们需要设置组件的初始状态，并将`this`绑定到我们创建的每个函数。在我关于[开始使用 React](https://www.nathanheffley.com/blog/getting-started-with-react-in-laravel-5-5) 的文章中，我解释了为什么我们需要做这个绑定。

```
constructor(props) {
    super(props);
    this.state = {
        title: '',
        content: '',
    };

    this.postThread = this.postThread.bind(this);
    this.changeTitle = this.changeTitle.bind(this);
    this.changeContent = this.changeContent.bind(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看完整的文件，[在 GitHub](https://github.com/NathanHeffley/react-forum/blob/251939322e9d0ad9558721907f8809ac11bd442e/resources/assets/js/components/CreateThread.js) 上查看。

就是这样！我们现在有一个组件可以使用 React 和 Axios 在我们的论坛上创建线程。访问你的项目(或者 克隆它)并转到`/home`路径查看表单。如果你尝试提交它，你可能看不到太多变化，但是如果你检查你的浏览器开发工具，你应该能看到一个 POST 请求发送到`/threads`。提交一个帖子后，尝试访问`/threads/1`，你应该能看到你创建的第一个帖子。

这种缺乏反馈的情况对用户来说不是很友好，所以在下一课中，我们将创建某种警告组件来告诉用户他们的线程已经成功创建，并给他们一个链接来查看它。