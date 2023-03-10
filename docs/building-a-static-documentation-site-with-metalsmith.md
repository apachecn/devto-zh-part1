# 用 Metalsmith 构建静态文档站点

> 原文：<https://dev.to/gregoryleeds/building-a-static-documentation-site-with-metalsmith>

在工作中，我公司的产品团队已经使用 GitHub wiki 多年，用于我们所有面向文档的使用。随着他们从一个小型开源项目成长为一个拥有更全功能的企业产品的更大的团队，他们已经不能使用 GitHub wiki 了。我们出去寻找一套工具来构建我们自己的自托管文档网站，并满足以下要求:

*   为文档作者提供了一个易于使用的工作流，不需要程序员或设计人员编写
*   版本化我们的文档的能力
*   快速部署
*   我们的开发人员了解并能够支持的技术堆栈
*   无服务器部署

goto for documentation，GitHub 站点的默认设置是我们首先看到的 [Jekyll](https://jekyllrb.com/) 。虽然 Jekyll 有一个很好的社区，并且可能是阻力最小的途径，但是我们团队中没有人有任何 Ruby 经验的事实让我们寻找更多的选择。我们的核心产品是用 Java 编写的，但我们已经有了一些用 NodeJS 编写的支持基础设施，所以当我们开始寻找工具时，发现 [Metalsmith](http://www.metalsmith.io/) 是最受欢迎的选择。虽然 Metalsmith 的插件已经有好几天了，但它更像是一盒乐高积木，而不是一个完全组装的系统。

幸运的是，我找到并大量抄袭了神奇的[粒子微控制器板](https://docs.particle.io/guide/getting-started/intro/photon/)的开源文档。在 [GitHub](https://github.com/spark/docs) 上查看它们。他们的 Metalsmith 工作示例给了我足够的参考资料来开始。

# 项目结构

我们最初的项目结构是这样的:

```
docs
â”œâ”€â”€ public
â”‚Â Â  â””â”€â”€ components - Bower working directory
â”œâ”€â”€ scripts - All of the actual Metalsmith code
â”œâ”€â”€ src - Source of all content
â”‚Â Â  â”œâ”€â”€ assets 
â”‚Â Â  â”‚Â Â  â”œâ”€â”€ doc-media - Images used in docs
â”‚Â Â  â”‚Â Â  â””â”€â”€ images - Images used for all pages
â”‚Â Â  â”œâ”€â”€ css
â”‚Â Â  â””â”€â”€ markdown - The actual docs, subdirectories correspond to topnav
â”‚Â Â      â”œâ”€â”€ api
â”‚Â Â      â”œâ”€â”€ development
â”‚Â Â      â”œâ”€â”€ guide
â”‚Â Â      â”œâ”€â”€ index.md
â”‚Â Â      â””â”€â”€ install
â””â”€â”€ templates - The Bootstrap layouts for all pages 
```

Enter fullscreen mode Exit fullscreen mode

# 设置 Metalsmith 管道

Metalsmith 作为一个过滤器链工作，将输入目录(在我们的例子中，是一堆在`/src/markdown`中的降价)转换成输出目录。没有说 Metalsmith 的输入必须降价，也没有说输出必须是静态的 HTML 站点，但重要的是要记住，在其核心，Metalsmith 正在转换源文件，因此试图迫使它处理源文件之外的另一组数据可能很困难。有一次，我们试图让 Metalsmith 在构建的同时批量调整我们在文档中使用的截图的大小，结果证明是有问题的。

在 [`/scripts/metalsmith.js`](https://github.com/gleeds/metalsmith-docs-example/blob/master/scripts/metalsmith.js) 中，我们编写出核心渲染流程如下:

```
var ms = Metalsmith(__dirname)
  .source('../src/markdown')
  .destination('../build')
  .use(paths())
  .use(helpers({
    directory: './hbs-helpers'
  }))
  .use(collections({
      home: {
        pattern: 'index.md',
        metadata: {
          name: "Home"
        }
      },
      installation: {
        pattern: 'install/*.md',
        sortBy: 'order',
        metadata: {
          name: "Installation"
        }
      },
      guide: {
        pattern: 'guide/*.md',
        sortBy: 'order',
        metadata: {
          name: "Guide"
        }
      },
      development: {
        pattern: 'development/*.md',
        sortBy: 'order',
        metadata: {
          name: "Development"
        }
      },
      api: {
        pattern: 'api/*.md',
        sortBy: 'order',
        metadata: {
          name: "API"
        }
      }
    }))
  .use(markdown())
  .use(layouts({
    engine: 'handlebars',
    directory: '../templates',
    default: 'template.hbs'
  }))
  .use(assets({
    src: '../src/assets',
    dest: '../build/assets'
  }))
  .use(assets({
    src: '../src/css',
    dest: '../build/assets/css'
  }))
  .use(assets({
    src: '../public/components/bootstrap/dist',
    dest: '../build/assets/bootstrap'
  }))
  .use(assets({
    src: '../public/components/jquery/dist',
    dest: '../build/assets/jquery'
  }))
  .use(permalinks({
    relative: false
  })) 
```

Enter fullscreen mode Exit fullscreen mode

概括地说，下面是我们的渲染管道正在做的事情:

1.  配置源和目标目录
2.  将每个源文件的文件路径信息添加到 Metalsmith 元数据集合，这有助于我们建立链接和目录。
3.  允许 Handlebars 模板调用在`/scripts/hbs-helpers`中导出的 javascript 助手。我们用它来做一些简单的事情，比如在顶部导航中突出显示当前收藏。
4.  根据匹配模式将源文件分成集合。这些用于顶部导航和侧边栏导航，以及每个单独页面呈现的目录。
5.  将 Markdown 呈现为 HTML
6.  将渲染的 HTML 注入到手柄模板中
7.  强制将“源”目录之外的静态资产复制到适当的输出目录中。
8.  将所有未命名为`index.html`的 html 文件移动到同名的子目录中，并在该目录中将它们重命名为`index.html`。这为我们的静态站点提供了漂亮的 URL。

然后导出管道，这样我们就可以在没有单独的构建脚本的情况下使用它。

# 构建脚本

我们构建的 Metalsmith 管道会在调用时将整个静态站点编译到`/build`目录中，但这通常不是我们想要做的。我们在主管道上构建了一系列脚本，让我们可以做一些有趣的事情，比如:

*   只是渲染整个事情，然后退出
*   呈现站点并启动 web 服务器来托管内容，观察任何更改并重建站点。对于我们的文档作者来说，这是一个很好的工作流程，因为所有需要做的就是保存他们的 Markdown 文件，然后在浏览器中点击 F5 来查看他们的工作效果。
*   渲染站点，然后部署它。

所有这些脚本都是通过类似于`npm run www`的操作从`package.json`开始运行的。

向这些脚本添加额外的过滤器非常简单，比如这个[开发服务器脚本](https://github.com/gleeds/metalsmith-docs-example/blob/master/scripts/www.js) :

```
ms
  .use(watch({
        paths: {
          "${source}/**/*": true,
          "../templates/**/*": true,
        },
        livereload: true,
      })
    )
  .use(serve({
    port:3000
  }))
  .build(function(){}); 
```

Enter fullscreen mode Exit fullscreen mode

# 版本化

最终，我们希望托管与应用程序不同版本相对应的不同版本的文档。目前，我们只是标记托管我们内容的 git repo。

# 部署

静态网站的伟大之处在于它们非常容易托管。在我们的例子中，我们将网站复制到 AWS S3 桶中，并在前面放置一个 CloudFront CDN。

虽然 Metalsmith 有一个 S3 插件，但我发现使用[节点 S3 库](https://github.com/andrewrk/node-s3-client)更容易滚动我自己的插件，它甚至可以对你的所有文件运行校验和，因此它只需几秒钟就可以上传我们的整个网站。上传完成后，脚本会向 CloudFront 发送一个缓存失效请求。

下面是[部署脚本](https://github.com/gleeds/metalsmith-docs-example/blob/master/scripts/deploy.js) :
的细节

```
ms
    .build(function(err){
        if(err) {
            return fatal(err.message);
        }
        else {
            var client = s3.createClient({
                s3Options: {
                    region:'us-west-2'
                }
            });

            var params = {
                localDir: __dirname + '/../build',
                deleteRemove: true,
                s3Params: {
                    Bucket:'docs-site'
                }
            };

            var uploader = client.uploadDir(params);
            uploader.on('error', function(err) {
                console.error("unable to sync:", err.stack);
            });
            uploader.on('progress', function() {
                console.log("progress", uploader.progressAmount, uploader.progressTotal);
            });
            uploader.on('end', function() {
                console.log("done uploading");
            });
        }
    }); 
```

Enter fullscreen mode Exit fullscreen mode

如果您还没有从 AWS CLI 工具进行设置，那么您需要用您的 AWS 凭证创建一个`~/.aws/credentials`文件，以使部署工作。

# 结论

最后，我们基于 Metalsmith 的文档网站可能需要更多的工作来设置，但现在已经完成了，我们对结果非常满意。文档作者已经享受了自动更新服务器的快速反馈外观。使用 git 为我们提供了一个很好的方法，通过拉请求来查看文档更新，并对文档进行版本控制。部署速度如此之快，几乎让人觉得好像出了什么问题。

完整的工作示例，请查看这个 [GitHub repo](https://github.com/gleeds/metalsmith-docs-example) 。