# 我对 Webpacker/Yarn 和已编译的 Rails 资产的罪恶

> 原文：<https://dev.to/dstull/my-sins-against-webpackeryarn-and-compiled-rails-assets>

## 我做了什么...

*   使用了 Rails 5.x 中的 [webpacker gem](https://github.com/rails/webpacker) ，然后将 yarn 安装的 webpacker/js 包全部检入 git。
*   编译 Rails 资产，同时仍然在 dev 中，并将它们签入 git。

## 我为什么要这么做？

*   对于 Webpacker/Yarn，即使它有一个 yarn.lock 文件之类的，我也找不到不这样做的理由。尽管你可以为此“错误”找出 100 个理由。此外，在我们的 MAC 电脑之外，访问外部世界、安装 yarn 等几乎是不可能的(不是不可能，只是如果我们有其他方法，可能不值得)。因此，如果我们要在苹果电脑之外的任何地方开发，我们就会遇到问题...当然，如果不在我们的 MAC 电脑上，我们无论如何也不能下载新的软件包...嗯
*   对于 Rails 资产，由于 [yarn install](https://github.com/rails/rails/commit/b1c08d8d9b921fdcf3813b5c20a0c3fab96eccca) 和 [webpacker:compile](https://github.com/rails/webpacker/commit/d2a6537e6ab0896a9bf14b6eddc5c0961db2c802) 现在已经“嵌入”到 rake assets:precompile 命令中，我们陷入了困境。在我们的开发环境中安装 yarn/node 是一项艰巨的任务。

### 如何在 git 中预编译资产的情况下实现这一点，并在未编译资产的情况下正常开发。

*   webpacker 的安装和实际使用可以在 webpacker github [页面](https://github.com/rails/webpacker)找到。

#### 强制开发和测试仍然正常使用未编译的资产。

*   在 config/environments/development . Rb 和 test.rb 中添加 config.asssets.prefix 指令以覆盖默认值:

```
 # this allows us to precompile for non dev and commit, yet still use uncompiled for dev
  config.assets.prefix = '/dev-assets' # can be anything really 
```

Enter fullscreen mode Exit fullscreen mode

*   工作原理:由于我们将编译到通常的资产路径，开发和测试将不会找到编译的资产，并将默认回到未编译的资产。

#### 如果资产发生变化，现在您需要在签入 git 之前执行额外的步骤:

*   注意:在运行测试后进行此操作**...这一部分很重要，因为我已经看到 manifest.json 被覆盖并指向端口 8080 上的 webpacker 本地服务器。**
*   在您的终端中:

```
RAILS_ENV=production rake assets:precompile 
```

Enter fullscreen mode Exit fullscreen mode

*   像往常一样将文件提交到 git。由于生产环境是在安装 webpacker 时默认设置的，因此这是用于此目的的最佳环境，即使您有多种类型的“生产”环境。当然，有些人会说 rails 中应该只有 3 个环境，并使用 ENV 变量来控制其他一切...取决于你的设置/视角，但是我跑题了...

## 结论/想法:

到目前为止，这似乎是有效的，尽管有些人会说我打破了纱线/资产/等的一些基本规则，有些人可能会说“是的，我也这么做了”。我所知道的是，我们需要一个针对我们的应用所处的封闭环境的解决方案，这似乎是一个可行的解决方案。我并不是自己想出了这个解决方案，而是一堆谷歌搜索、阅读 rails 文档，然后把它们都放在这里...我希望这能激发一些富有成效的对话，也许是一个比这个更优雅的解决方案，用于与外界隔绝/很难在开发之外安装任何东西/使用你自己的基础设施而不是 AWS/Heroku world。

还有一件事要提一下...我相信这将有助于我们顺利过渡到 docker。我看过一些 docker 编译和维护资产的策略，看起来都很乱。我相信这一个将保持 docker 图像不变，并为我们提供更少的环境之间的摩擦。