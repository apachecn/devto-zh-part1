# ReactJS 和 Laravel -运行基本设置-第 1 部分

> 原文：<https://dev.to/devtalhaakbar/reactjs-and-laravel---running-through-a-basic-setup---part-1-ei8>

在新创建的 Laravel 5.5 项目中键入以下命令:

```
php artisan preset react 
```

Enter fullscreen mode Exit fullscreen mode

做完了吗？看到你的`package.json`更新了吗？太好了，不要关！我们在那里有些工作要做。在你的`devDependencies`；添加以下内容:

```
"bootstrap": "^4.0.0-beta",
"popper.js": "^1.12.6",
"react-redux": "^5.0.6",
"react-router-dom": "^4.2.2",
"reactstrap": "^5.0.0-alpha.3",
"redux": "^3.7.2",
"redux-form": "^7.1.1" 
```

Enter fullscreen mode Exit fullscreen mode

一旦你完成了上面的工作，注意到我在那里添加了 Bootstrap 4 Beta 包了吗？是的，留着吧。也就是说，从`devDependencies` :
中删除`bootstrap-sass`和`jquery`声明

```
"bootstrap-sass": "^3.3.7",
...
"jquery": "^3.1.1", 
```

Enter fullscreen mode Exit fullscreen mode

现在，一旦你做到了以上几点。快速启动您的终端并发出以下命令:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

希望你没有网络问题。如果你足够幸运地安装了这些包，那么转到`/resources/assets/sass`目录。在您选择的编辑器中打开`variables.scss`文件，删除其中的所有内容。接下来，从同一个`sass`目录中打开`app.scss`，并将其更改为:

```
// Variables
@import "variables";

// Bootstrap
@import "~bootstrap/scss/bootstrap"; 
```

Enter fullscreen mode Exit fullscreen mode

现在，你已经在 CSS 端为 Bootstrap 4 做好了一切准备。

移动到`/resources/assets/js/`目录，打开`bootstrap.js`进行编辑。一旦你打开了那个文件，赶紧删除下面几行:

```
/**
 * We'll load jQuery and the Bootstrap jQuery plugin which provides support
 * for JavaScript based Bootstrap features such as modals and tabs. This
 * code may be modified to fit the specific needs of your application.
 */

try {
    window.$ = window.jQuery = require('jquery');

    require('bootstrap-sass');
} catch (e) {} 
```

Enter fullscreen mode Exit fullscreen mode

另外，打开`app.js`文件(在`bootstrap.js`旁边)，它实际上被配置为 JS 代码的入口点。一旦你打开它，看:

```
require('./components/Example'); 
```

Enter fullscreen mode Exit fullscreen mode

在这一行中，只需将`/Example`替换为`/App`。那更好。不用担心；只要遵循下面的步骤，你就会知道我为什么这么做(或者，你足够聪明，已经猜到了。)

Laravel 附带示例组件，可用于快速检查是否根据您选择的预设正确设置了所有内容。查看组件目录，您会看到两个文件:`ExampleComponent.vue`和`Example.js`。删除`ExampleComponent.vue`文件，因为我们不需要它。然后把`Example.js`改名为`App.js`，让自己准备好和 JSX 一起敲几下 ES6。如果你注意到了，我们在`app.js`中用`/App`替换了`/Example`，因为我们要在`components`目录中将`Example.js`重命名为`App.js`。

此时，您可以清除组件目录中从`App.js`开始的所有内容，或者保留示例代码。如果你决定选择后者，我建议你做一些基本的改变。

将组件类名从`Example`重命名为`App`，并更改以下行:

```
if (document.getElementById('example')) {
    ReactDOM.render(<Example />, document.getElementById('example'));
} 
```

Enter fullscreen mode Exit fullscreen mode

同台词:

```
if (document.getElementById('app')) {
    ReactDOM.render(<App />, document.getElementById('app'));
} 
```

Enter fullscreen mode Exit fullscreen mode

继续前进；快速打开与您的 Laravel 项目相关的`/routes`目录中的`web.php`文件。去掉你唯一看到的路线，换成:

```
Route::group(['prefix' => 'ajax'], function() {
    // all routes that don't need to go to react-router
});

Route::get('/{path?}', function () {
    return view('web');
}); 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们有一个特殊的路由组，它只匹配那些 URL 带有前缀`ajax/`的路由。这个路由组将帮助我们建立这样一个`react-router`设置，它可以在不使用基于`#`的 URL 的情况下呈现适当的组件。然而，AJAX 路由组之外的路由声明匹配每一个 URL，不管它是什么，并返回`web`视图作为响应。

接下来，移动到`/resources/views`目录，重命名`welcome.blade.php`，并将其更改为`web.blade.php`或任何你喜欢的名字。只需确保视图名称与我们在`routes/web.php`中指定的名称相匹配。(我假设你把它改名为`web.blade.php`)

打开`web.blade.php`，用类似 Bootstrap 4 HTML Starter 的内容替换它的内容。无论你做什么，确保以下三件事:

*   有一个`<link>`标签引用了`/app.css`文件。
*   有一个`<script>`标签，其 src 设置为`/app.js`文件。
*   在 id 为`app`的`<body>`标签中有一个`<div>`。

此时，如果运行以下命令:

```
npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

并等待其完成。然后，通过下面的 artisan 命令运行 PHP 的小服务器:

```
php artisan serve --port=80 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以导航到`http://127.0.0.1`并看到所有活动。(我假设您没有接触过`Example`组件中的示例代码)。

在所有这些忙乱之后，您将能够使用以下代码编写 React 组件:

*   反应路由器
*   Redux
*   冗余形式
*   反应阱

我们的 **ReactJS 和 Laravel 的第 1 部分到此结束——通过基本设置**运行。考虑到第 1 部分的回答，我将制作第 2 部分，在其中我将在 Laravel 和 React 中使用相同的设置来设置认证系统。

请注意，上面的 npm 包是在花费大量时间后选择的，所以，保留它们并查看它们的入门指南，以防止自己头疼。