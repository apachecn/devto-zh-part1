# Rails 上的第一个渐进式 Web 应用程序

> 原文：<https://dev.to/rossta/your-first-progressive-web-app-on-rails>

关于[渐进式网络应用](https://developers.google.com/web/progressive-web-apps/) (PWA)的讨论在 JavaScript 社区[引起了注意](https://medium.com/javascript-scene/native-apps-are-doomed-ac397148a2c0)，但是是在 Rails 开发者中...没有那么多。

渐进式网络应用程序技术仍然非常年轻，而且发展迅速；也许 Rails 团队更不愿意使用新的 JavaScript APIs，直到它们变得更加稳定。此外，Rails 社区到目前为止还没有一条集成 PWA 技术的清晰道路。在这篇文章中，我们将展示如何使用`serviceworker-rails` gem 将你的 Rails 应用变成一个渐进式 Web 应用。

什么是渐进式网络应用？简单地说，它们是提供类似移动应用程序的体验的 web 应用程序。例如，开放网络技术现在使得基于浏览器的网络应用成为可能:

*   **可安装** -轻松将网络应用添加到移动设备的主屏幕上
*   **更可靠** -在设备离线或网络响应能力下降时提供用户体验；当网络功能恢复时，在后台同步用户请求
*   **更吸引人** -即使用户不使用网站，也能通知他们活动

听起来不错吧？让我们开始吧。

## Rails 上的第一个渐进式 Web 应用

Eric Elliot 最近发布了[一篇关于渐进式 Web 应用需求的全面概述](https://medium.com/javascript-scene/native-apps-are-doomed-ac397148a2c0)，值得一读。以下是所需内容的摘要:

*   HTTPS——任何使用渐进式网络应用技术的页面都需要通过 SSL/TLS 提供服务，因此推荐“HTTPS 无处不在”
*   [Web 应用清单](https://developer.mozilla.org/en-US/docs/Web/Manifest) -一个包含应用元数据的文本文件，支持主屏幕安装
*   服务工作者(Service Worker)——一个客户端 JavaScript 工作者，可以拦截网络请求、修改响应、与本地缓存交互、在后台同步数据以及启用推送通知

### 成立 HTTPS

为了在我们的网站上启用 HTTPS，我们需要根据我们的主机提供商和部署需求来决定如何设置我们的 web 服务器。设置 Heroku 和 T2 数字海洋的教程可能是一个好的开始。

我们还想在远程环境的 Rails 应用程序配置中强制使用 SSL 设置，例如`production`。

```
# config/environments/production.rb

Rails.application.configure do
  # ...

  # Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.
  config.force_ssl = true

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

在投入生产之前，我们应该确保在辅助远程环境中测试这种行为，因为我们希望了解应用程序中混合内容警告和硬编码非 HTTPS URL 等问题。

### 添加一个货单和服务人员

对于下一步，我们将假设使用 Rails 资产管道。这很有帮助，但也带来了一个问题:

Rails 资产管道做出了许多关于什么最适合部署 JavaScript 的假设，包括资产摘要指纹和长期缓存头——主要是为了增加“可缓存性”。Rails 还假设了一个父目录/public/assets，以便更容易地查找给定资产的文件路径。

服务工作者和清单资产必须遵循不同的规则。服务工作者只能在他们被服务的范围内活动。因此，如果您尝试从 Rails 资产管道路径(如`/assets/serviceworker-abcd1234.js`)注册一个服务工作者，它将只能与`/assets/**`内的请求和响应进行交互。这不是我们想要的。

为了解决这个问题，我创建了`serviceworker-rails` gem ( [source](https://github.com/rossta/serviceworker-rails) )。这个 Rails 插件使得设置您的应用程序更容易，以便在规范的 URL 上提供服务工作者脚本和 web 应用程序清单，同时利用资产管道提供的编译和插值功能。

为了开始使用`serviceworker-rails`，我们将把它与我们的 Rails 应用捆绑在一起。

将宝石添加到`Gemfile` :

```
# Gemfile

gem "serviceworker-rails" 
```

Enter fullscreen mode Exit fullscreen mode

捆绑 app:

```
$ bundle 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用 service worker rails 的内置生成器向我们的项目和适当的配置添加一些 starter JavaScript 文件。

```
$ rails g serviceworker:install 
```

Enter fullscreen mode Exit fullscreen mode

生成器将创建以下文件:

*   `config/initializers/serviceworker.rb` -用于配置您的 Rails 应用程序
*   `app/assets/javascripts/serviceworker.js.erb` -包含一些示例策略的空白服务人员脚本
*   `app/assets/javascripts/serviceworker-companion.js` -在浏览器中注册服务人员所需的 JavaScript 代码片段
*   `app/assets/javascripts/manifest.json.erb` -一个指向 gem 提供的一些默认应用程序图标的启动 web 应用程序清单
*   `public/offline.html` -开始离线页面

它还将对现有文件进行以下修改:

*   向`application.js`添加链轮指令，要求`serviceworker-companion.js`
*   将`serviceworker.js`和`manifest.json`添加到`config/initializers/assets.rb`中的编译资产列表中
*   将标签注入到`app/views/layouts/application.html.erb`的`head`中，用于链接到 web 应用清单

当然，我们可以手动设置，但是第一次尝试运行自动安装可能会有所帮助。如果要手动操作，请查阅`serviceworker-rails` [自述文件](https://github.com/rossta/serviceworker-rails)和我之前关于配置 gem 的[博文，以获得更多设置帮助。](https://rossta.net/blog/service-worker-on-rails.html)

至此，我们已经在 Rails 应用程序中准备好了所有的样板文件，可以开始添加渐进式 Web 应用程序功能了。最棒的是，我们可以挑选想要添加的功能。

这里有一些你可以尝试的事情:

*   [为你的 Rails 应用添加离线页面](https://rossta.net/blog/offline-page-for-your-rails-application.html)—[@ rossta](https://dev.to/rossta)
*   [从 Rails 发送网页推送通知](https://rossta.net/blog/web-push-notifications-from-rails.html)—[@ rossta](https://dev.to/rossta)
*   [使用服务工作者应用外壳架构即时加载 Web 应用](https://addyosmani.com/blog/application-shell/)-Addy Osmani *

## 更进一步

关于掌握服务人员基础知识和开发网络离线解决方案的更多信息，我强烈推荐这个免费的 Udacity 课程:

*   谷歌的离线网络应用

您会想要了解服务人员的生命周期，Jake Archibald 对此进行了详细的论述:

*   [服务人员生命周期](https://developers.google.com/web/fundamentals/instant-and-offline/service-worker/lifecycle)

对于实现服务工作者获取和缓存策略的一些开源抽象，请查看来自谷歌 Chrome 团队的 [SW-Toolbox](https://github.com/GoogleChrome/sw-toolbox) 和 [SW-Precache](https://github.com/GoogleChrome/sw-precache) 。

当然，我们只是介绍了渐进式 Web 应用程序之旅的入门部分。我省略了很多有趣的部分，比如决定如何实现缓存策略或发送推送通知。这里有一些资源可以让你的 PWA 技能更上一层楼:

*   来自 Mozilla 的一组服务人员食谱和演示
*   离线食谱——杰克·阿奇博尔德(Jake Archibald)撰写的服务人员片段的概要，包括许多缓存策略
*   [Rails 沙盒上的服务人员](https://github.com/rossta/serviceworker-rails-sandbox)——一个使用 [@rossta](https://github.com/rossta) 的`serviceworker-rails` gem 的 Rails 上的渐进式 Web 应用程序演示
*   [令人敬畏的进步网络应用](https://github.com/TalAter/awesome-progressive-web-apps)-PWA 资源的厨房水槽
*   [牛逼的服务人员](https://github.com/TalAter/awesome-service-workers) -服务人员资源的厨房水槽

## [t1【轨道】PWA](#rails-%C3%B0%C3%BF%C5%93-pwa)

Rails 与先进的 Web 应用技术没有不一致的地方，所以我们没有技术上的理由不能从今天开始引入这些特性。值得注意的是，选择过渡到 PWA 与您可能已经在处理的任何 JavaScript MVC 框架/模块捆绑器/turbolinks 决策是完全正交的。

在未来几年，我相信随着越来越多的网站访问将转移到移动网络，采用 PWA 功能来满足需求将变得越来越重要。

*本文原载于[rossta.net](https://rossta.net/blog/make-your-rails-app-a-progressive-web-app.html)T3】*