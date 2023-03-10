# 采用 Laravel 和 Vue 的防弹布线(第 1 部分)

> 原文：<https://dev.to/mozartted/bullet-proofed-routing-with-laravel-and-vue-part-1-32l>

# 简介

Laravel 和 Vue 是我喜欢的两个非常酷的软件工具，坦白地说，我觉得没有比这更好的组合了。在本帖中，我们将看看我们可以实现的路由技巧，以充分利用这些家伙。

> 路由尤其是 Vue 路由是一个长期的业务，所以这将是一个两部分的职位，所以留下来。

## 路由问题

我知道，我们可能不想承认这一点，但与 Vue 和 laravel 运行一个节目作为一个水疗中心，我们通常有一个路由问题。

> 这到底是什么问题

#### 案件现场

因此，我们有一个具有以下路径的应用程序

/resource/
/posts/
/posts/{ slug }

让我们假设这些路由是通过 laravel (routes/web.php)中的 web 路由提供的，没什么大不了的，

但是，当我们使用 vue 路由器将魔法转移到 vue 时，它就变成了一个非常棘手的问题，对于 spa 来说，所有路由都必须加上前缀 hash bang，以便 vue 能够正确处理它们的转换。

因此
/resource/成为！#/resources
/posts/becoms！#/posts
/posts/{slug}变成了？？，你猜对了，嘭！#/posts/{slug}

对于像我们这样的酷开发者和我们希望拥有的令人惊奇的用户来说，这并不酷。

#### 关键时刻。

在这一点上，你可能真的很烦，只想看到解决方案，所以我们开始吧。

现在，vue 试图使用 Vue 路由器上的历史值
来解决这个问题

```
const router = new Router({
        routes: routes,
        base: '/',
        mode: 'history',
        history: true
    }); 
```

Enter fullscreen mode Exit fullscreen mode

使用路由器设置，我们可以将历史值设置为 true，并让它们在从 route link `router-link`组件单击时使用正常的路由值。

但这只是部分修复，因为当我们从主外部链接请求路由时，这会给我们一个错误，因为现在是 laravel 路由处理混合，而不再是 vue 路由运行的节目，所以我们如何确保这仍然是 vue 业务

嗯，这是一个神奇的东西

```
Route::get('/{vue_capture?}', function () {
   return view('inner');
})->where('vue_capture', '^(?!storage).*$'); 
```

Enter fullscreen mode Exit fullscreen mode

这种路由设置实际上意味着所有跟在/后面的路由都被立即发送回托管 vue 根元素和 main 实现的刀片文件，在这种情况下，刀片文件位于函数上的 regex 的“内部”,捕获除了希望访问存储链接的路由之外的所有路由。

## 结论

有了这个 vue 处理节目，所有的事情路由运行与 vue。在下一篇文章中，我们将看看使用 vuex 进行身份验证并自动重定向到适当路由的过程，当然还有如何以防弹的方式做到这一点。