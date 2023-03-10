# 从您的应用程序库中持续部署自定义 Heroku 错误和维护页面

> 原文：<https://dev.to/nholden/continuously-deploy-custom-heroku-error-and-maintenance-pages-from-your-apps-repository-13kn>

如果你不小心在 Heroku 上部署了一些糟糕的代码，你可能很熟悉 [Heroku 的应用错误页面](https://www.herokucdn.com/error-pages/application-error.html)。或者你可能需要让你的应用进入维护模式，你已经看到了 [Heroku 的维护模式页面](https://www.herokucdn.com/error-pages/maintenance-mode.html)。

这两个页面都可以通过配置变量定制。您所需要的是可以公开访问的、位于应用程序之外的静态 HTML 页面。Heroku 建议将它们放在亚马逊 S3 上，但是在那里上传页面，忘记它们，让它们与应用程序的其他部分不同步是很容易的。

避免丢失自定义错误和维护页面的一种方法是从应用程序的存储库中持续部署它们。有一种方法可以做到这一点。

## 选择一个目录来提供页面

这可能是存储库中的新目录，也可能是已经存在的目录。请注意，无论您选择哪个目录，它的所有文件都是可以公开访问的。

我的应用程序，[周一里程](https://github.com/nholden/monday-miles)，是一个 Rails 应用程序，这意味着`/public`目录已经可以公开访问了。对我来说，提供一个镜像似乎没什么大不了的，所以我选择将错误和维护页面存储在`/public`中。其他选项包括一个单独的`/pages`目录，或者一个以你计划提供这些页面的域名命名的目录。

## 将您的页面添加到目录中

页面应该是静态的 HTML 页面。虽然您不能从应用程序的其他地方访问资源(比如从 Rails 的资源管道)，但是您可以将图像和 CSS 上传到同一个目录，并使用相对路径引用它们。

我已经有了一个用于周一里程的 [`/public/500.html`](https://github.com/nholden/monday-miles/blob/master/public/500.html) 页面，所以我重用它作为我的错误页面。我调整了副本来创建我的 [`/public/maintenance.html`](https://github.com/nholden/monday-miles/blob/master/public/maintenance.html) 页面。

## 创建新的 Netlify 应用

Netlify 是一个托管静态网站的很棒的服务。如果你使用像 Jekyll 或 Middleman 这样的静态站点生成器，它可以做一些有趣的事情，但它也非常适合托管静态 HTML 文件的普通旧目录。与 GitHub Pages 相比，我最喜欢的 Netlify 的优点是，它通过“让我们加密”为自定义域提供了免费的一键 HTTPS。

注册后，点击“从 Git 新建网站”选择您的 Git 提供者(在撰写本文时，支持 GitHub、GitLab 和 Bitbucket)，选择您的存储库，并选择要部署的分支(可能是`master`)。为了确保您只公开所需的目录，您可以在“发布目录”字段中输入该目录，或者向您的存储库的根目录添加一个超级简单的 [netlify.toml](https://www.netlify.com/docs/continuous-deployment/#deploy-contexts) 配置文件([这里是我的](https://github.com/nholden/monday-miles/blob/master/netlify.toml)用于星期一英里)。因为您没有使用静态站点生成器，所以不需要设置构建命令。单击“部署站点”，并验证您的页面在所提供的*.netlify.com 域中是活动的。

## 设置您的自定义域(可选)

Heroku 将在 iFrame 中提供您的错误和维护页面，因此如果这些页面是从*.netlify.com 提供的，您的用户不太可能会注意到。但是，netlify 使您可以很容易地开始使用自定义域，我认为多花几分钟时间是值得的。

在您的新 Netlify 应用程序的概述页面上，单击“设置自定义域”，并按照说明添加自定义域(我选择了 public.mondaymiles.com)。如果您的域与提供 CDN(如 Cloudflare)的提供商合作，请确保为“仅 DNS”设置新的 CNAME 记录，因为 Netlify 有自己的 CDN。按照说明提供“让我们加密”证书并启用 HTTPS。

如果您希望任何对您的 Netlify 域中不存在的文件的请求将用户重定向到您的主应用程序，您需要将一个 [`_redirects`文件](https://www.netlify.com/docs/redirects/)添加到您的 Netlify 发布目录中。[这是](https://github.com/nholden/monday-miles/blob/master/public/_redirects)的矿井。

## 配置 Heroku

将`ERROR_PAGE_URL`和`MAINTENANCE_PAGE_URL`配置变量设置为指向 Netlify 上的错误和维护页面。你可以通过 [Heroku CLI](https://devcenter.heroku.com/articles/error-pages#configure-your-application) 或者通过你的应用程序在“设置”页面上的仪表板来完成。如果你使用 [Heroku Pipelines](https://devcenter.heroku.com/articles/pipelines) ，记得为你所有的应用程序做这件事。按照 Heroku 的说明测试你的错误和维护页面。

就是这样！任何时候你在你的应用库中更新这些页面，它们都会在 Netlify 上被更新。您的用户不会看到一般的 Heroku 错误和维护模式页面，而是会看到您创建的定制页面(希望不要太频繁！).