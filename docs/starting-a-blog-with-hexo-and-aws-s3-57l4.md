# 与 hexo 和 AWS S3 一起开博客

> 原文：<https://dev.to/vkuznecovas/starting-a-blog-with-hexo-and-aws-s3-57l4>

> 这是我博客上的一篇交叉文章，原文可以在这里找到。

所以我和我的女朋友一直在考虑开一个博客。放假一天，我决定好好利用它，开始创造一天。作为一名开发人员，在选择博客框架时，我有一些顾虑:

*   我是个控制狂，所以我更喜欢自己主持。博客平台是不可能的。
*   我喜欢 markdown -博客必须支持它。
*   必须是轻量级的。
*   它必须易于部署到 s3 静态网站托管。
*   我应该不需要使用 s3 以外的任何东西来托管。只需一个文本编辑器和一个命令行界面就能获得良好的博客体验。

经过一番研究和朋友的建议，我登陆了 Hexo.io。它似乎勾选了我所有的选项。插件支持似乎也很出色。基于节点，这意味着我可以找到我想要的任何东西。如果没有-我可以自己编码。

## 开始工作吧！

> 一个简短的声明——如果你想跟进，你需要一个 AWS 账户(也许？)一些编码知识。

#### 安装 hexo

最重要的事情第一-安装 hexo。嗯——这就像遵循他们的[文件](https://hexo.io/docs/)的最初几个步骤一样简单。运行`npm install -g hexo-cli && hexo init blog`后，我用`hexo generate && hexo server`检查它。看起来很好，但它只包含 hello world 帖子，我想在上面有图像的东西，看看那些会是什么样子。

#### 创建帖子

让我们创造一个，好吗？`hexo new post test`在`source/_posts/`下创建一个新的`test.md`文件。我用一些随机的单词填充它，并添加一个图像。请注意，添加资产的方式如下:

```
{% asset_img test_image_thumb.jpg Alt text. %} 
```

Enter fullscreen mode Exit fullscreen mode

完成这一步并验证它在 localhost 上工作后，我立即意识到我不喜欢 hexo 默认使用的文件结构。如果所有的邮政资产都被添加到`source/_posts/`中，这将很快变得一团糟。快速搜索一下谷歌就会发现，改变`_config.yml`中的一个标志会让 hexo 为每个新帖子在`source/_posts/`下创建一个子目录。简单地切换一下`_config.yml`的`post_asset_folder`旗，我就变成了一只快乐的熊猫。我重新创建了这篇文章，图片现在舒适地躺在文章的资产文件夹中。

#### 木桶

然后我将注意力转移到尽快让博客托管在 [AWS S3 静态网站托管](http://docs.aws.amazon.com/AmazonS3/latest/dev/WebsiteHosting.html)上。为什么是 S3？主要是因为我习惯了 AWS，他们有很好的关于如何在那里托管静态网站的[文档](http://docs.aws.amazon.com/AmazonS3/latest/dev/HostingWebsiteOnS3Setup.html)。创建 bucket 时，需要做的就是设置名称，稍后我会更改设置。配置本身并不难。我需要在属性选项卡下将静态网站托管设置为启用。在权限选项卡上，打开存储桶策略并将其粘贴到:

```
{  "Version":  "2008-10-17",  "Statement":  [  {  "Sid":  "AllowPublicRead",  "Effect":  "Allow",  "Principal":  {  "AWS":  "*"  },  "Action":  "s3:GetObject",  "Resource":  "arn:aws:s3:::exampleBucket/*"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

如果您继续学习，请确保将 exampleBucket 更改为您的 Bucket 名称。这项政策允许公众访问我的桶，我希望如此。有了这些，是时候部署我的博客了。

#### 部署到 S3

然而，hexo 的问题是，它不支持 S3 部署开箱即用。是时候看看插件库有多好了。谷歌搜索显示了一个候选人:

*   [hexo-deployer-aws-s3](https://www.npmjs.com/package/hexo-deployer-aws-s3)

快速`npm i -S hexo-deployer-aws-s3`安装依赖项。这是从 AWS 获取钥匙的运行时间。我需要这些能上传到 S3。AWS 为我们提供了关于如何做的文档。一旦我下载了密钥，我就在我的终端中将它们导出为 env 变量:

```
export AWS_SECRET_ACCESS_KEY=yourkeyhere
export AWS_ACCESS_KEY_ID=youridhere 
```

Enter fullscreen mode Exit fullscreen mode

剩下要做的就是在我们的`_config.yml`中添加以下三行代码来添加一个部分:

```
deploy:
  type: aws-s3
  region: yourregion # eu-west-1 < this is mine
  bucket: yourbucketname 
```

Enter fullscreen mode Exit fullscreen mode

嘭，我们准备好部署了。我首先运行`hexo generate`来生成静态内容。将其部署到 s3 - `hexo deploy`。随着内容的部署，我通过访问 url `http://yourbucketname.s3-website-yourregion.amazonaws.com/`来检查是否一切正常。一切都很有魅力，但现在还不是写博客的时候。

#### 云锋

AWS 的伟大之处在于它允许无缝使用它的许多工具。由于我们有一个位于特定区域的 bucket，如果我们的 bucket 位于亚洲，而客户从欧洲打开网站，我们可能会遇到加载缓慢的情况。为了避免这种情况，我利用了 [AWS CloudFront CDN](https://aws.amazon.com/cloudfront/) 。它会将博客分发到其他区域的边缘节点，从而为那些将从不同区域访问博客的人带来更快的加载时间。它还提供了许多开箱即用的强大功能，如缓存、http - > https 重定向、压缩等等。我为 cloudfront 创建了一个 web 发行版，选择我的 bucket 作为源域名，其他设置基本上保持默认。我过会儿会和他们一起玩。创建分发后，将需要 10-30 分钟的时间部署到边缘位置，完成后，您将看到状态更改为“已部署”。该发行版将带有一个难看的域名，这是 cloudfront 提供的。要找到它，打开你的 CloudFront 发行版，它会在`Domain Name`下面。一旦发行版部署完毕，打开 url 并检查一切是否正常。不过，我现在需要修复域名。

#### [DNA](#dns)

幸运的是，AWS 拥有一切所需的工具。[亚马逊 Route 53](https://aws.amazon.com/route53/) 是他们的云 DNS 服务。我还没有注册域名，所以我可以向 AWS 注册一个。这会让事情变得简单。如果你确实拥有一个域名，但是在其他地方注册了，把它转移到 Route 53 可能是个好主意。要做到这一点，请按照亚马逊提供的指示。一旦域得到验证(如果从另一个注册商那里转移，可能需要几个步骤)，就该把它指向我们的 cloudfront 发行版了。为此，首先打开 cloudfront 发行版，然后编辑配置。需要改变的是备用域名。填写您希望您的博客可以访问的所有域名。dizzy.zone 设置包含以下条目:

```
dizzy.zone
www.dizzy.zone 
```

Enter fullscreen mode Exit fullscreen mode

保存发行版，前往 53 号公路。在那里，需要做的是添加几个 dns 条目。是时候创造一个新的记录了。选择的类型应该是 IPv4，别名设置为 Yes。单击别名目标将显示一个下拉列表，在那里我单击 cloudfront 发行版。创建后，记录集需要一些时间来传播，所以您可能一开始看不到变化，但是给它一些时间，它就会工作。我现在可以通过 http://dizzy.zone/访问我的博客。很好。

#### SSL

2017 年，你的网站没有理由不使用 ssl。它提高了搜索引擎优化评分，让你的网站看起来更专业。用 [AWS 证书管理器](https://aws.amazon.com/certificate-manager/)做起来超级简单。我转到我的 cloudfront 发行版，再次编辑它。在那里，我切换到`Custom SSL Certificate`并单击`Request or Import a Certificate with ACM`(请注意，证书应该总是在 us-east-1 region 下)。我添加以下域名:

```
*.dizzy.zone
dizzy.zone 
```

Enter fullscreen mode Exit fullscreen mode

因为我的域名托管在 Route 53 下，所以我选择了 DNS 验证。ACM 使得通过验证屏幕上的按钮来验证域变得容易。这需要几分钟的时间，但是一旦证书发出，我就可以将它应用到我的 cloudfront 发行版中。完成之后，这个博客现在可以通过 [https://dizzy.zone](https://dizzy.zone) 访问了。为了确保用户使用 https 而不是 http，在云前端分发的配置下，我通过将`Viewer Protocol Policy`更改为`Redirect HTTP to HTTPS`来创建一个行为。现在，如果您通过 http 打开网站，这可以确保您被重定向到 https。

### 结论

对于技术型的简单博客来说，Hexo 似乎是个完美的选择。易于进入和超级容易部署，因为它所做的只是烘焙静态网页。有了 AWS S3，部署一个静态网站，让它在所有地区都可用，并设置域名和 SSL 都很简单。做完这些，是时候开始写博客了，对吧？良好的..相反，我会首先针对 PageSpeed Insights 对它进行优化...