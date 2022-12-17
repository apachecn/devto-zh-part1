# 在 React 中导入图像

> 原文：<https://dev.to/letsbsocial1/importing-images-in-react-84o>

[![Importing images in React](img/0f4d813a24cde8edb3f78545f3f2040e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OWvmB5nw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3kxgqotmyxpg61t1wep9.jpg)

正如你们中一些关注我的帖子的人可能已经知道的，我一直在做我的**前端开发者组合**的`React`版本。尽管我很喜欢我的`Jekyll`版本，但我想尝试新事物。我还想使用`React`继续我自己的项目，同时继续深入各种 **React 工作流**。

我将在其他文章中介绍我对开发工具包所做的与应用程序工作流程相关的各种更改。在这里，我只想谈谈为了让我使用的图片在我的应用程序`locally`和`remotely`中正常工作，我必须做些什么。

# 网络包

首先，我想谈谈你需要什么样的工具来给你的**反应**应用程序添加图片。不仅仅是你需要用`React`做什么。如果你的工作流来自`webpack`，你必须首先考虑`webpack`需求。

有两个加载图像的本地 webpack 加载器:`url-loader`和`file-loader`。`url-loader`有利于发展。它像`file-loader`一样工作，但是如果文件是< 10000 字节，它返回一个`DataURL`。

我在`webpack-dev.config.js`中的`url-loader`配置:

```
{
    test: /\.(pdf|jpg|png|gif|svg|ico)$/,
    use: [
        {
            loader: 'url-loader'
        },
    ]
}, 
```

例如，当我在 **Chrome DevTools** 中检查我的`footerTwitter.png`文件时，它以如下方式显示:

```
<li>
    <a href="https://twitter.com/letsbsocial1">
        <img class="footerTwiiter" src="data:image/png:base64, iVBOR....AZaPAJx1SWgAAAAASUVORK5CYII=" width="40" alt="twitter">
    </a>
</li> 
```

那是因为`footerTwitter.png` <有一万个字节。然而，我的`profileSmall.png`是> 10，000 字节，它在 Devtools 中显示如下:

```
<div class="Home-content">
    <div class="Home-profile">
        <img src="0688089....png" class="Profile-image" alt="Profile image">
    </div>
</div> 
```

我不介意我的图像显示为一个难看的、不确定的文件，但是我不喜欢在生产中出现这种情况。我想在名字后面加一个`[hash]`，但是我也想保留文件原来的名字，这样我就可以识别它了。这就是`file-loader`的用武之地。

`file-loader`有利于生产。这是我的`file-loader`配置在我的`webpack-prod.config.js`里的样子:

```
{
    test: /\.(jpg|png|gif|svg|pdf|ico)$/,
    use: [
        {
            loader: 'file-loader',
            options: {
                name: '[path][name]-[hash:8].[ext]'
            },
        },
    ]
}, 
```

如果我没有添加一个`options`对象，默认情况下，我的文件名将会是那些长而难看的散列加上它们的本地扩展名。但是通过添加`name`属性和定制，我能够改变`file-loader`的`behavior`并发出我自己的`custom filename`。

`[path]`指文件相对于条目的路径。我的`webpack-prod.config.js`文件中条目属性的值是:

```
entry: {
    bundle: './src/index.js',
}, 
```

由于应用程序所需的一切都包含在`index.js`中，而`index.js`在`src`目录中，`[path]`指的是相对于`src`的文件路径。由于我也使用了`[name]`，它指的是任何给定文件的名称，因此包含了`src`中的任何给定文件，所以`src`中的所有文件都被复制到相对于`src`的`dist`文件夹中。由于`images`目录在`src`之外，它被复制到`dist`中作为其原始的`images`目录，包括`dist`中的任何子目录。在它之前没有添加`src`目录。但是这个设置有一个小故障。在`src`的顶部有一个名为`favicon.ico`的小文件。我需要将`|ico`添加到我的`file-loader` `test`属性中，以便 webpack 知道将它加载到应用程序中。我还使用了`[path][name]`，最终将`src`中的所有文件复制到`dist`中。当我运行一个`production`构建时，webpack 在`dist`中创建一个包含`favicon-[hash].ico`的`src`目录。我还没有找到阻止`dist`中`src`被创建的方法，也不知道有没有。其他人也遇到了类似的问题，据我所知，还没有找到解决办法。或许是时候来个`feature request`了？

`[hash:8]`是指加在文件名后面的哈希，数字 8 是指哈希的长度。默认长度太长了！至于选择分离器，最佳实践似乎不是一个`.`就是一个`-`。我个人喜欢清楚地看到我的分离器，所以我用了`-`。

`.[ext]`指文件扩展名。通过使用`[ext]`而不仅仅是一个扩展名意味着任何已经在`file-loader` `test`属性中定义的文件扩展名都将被包括在内。

# 反应

结构是如此重要。任何为他们的`HTML5`、`JS`和`CSS3`应用创建了`Gulp`工作流的人都知道我在说什么。与结构相关的图像路径也非常重要。在你的`React App`和`webpack.config.js`中适当的结构也能确保 Webpack 将你的图片正确的移动到你的 dist 文件夹中，并提供正确的路径。

为了能够将图像导入到`React components`中，您必须确保图像在本地驻留在与组件相同的父目录中，或者图像从它们驻留的目录中导出，以便它们可以导入到您的任何组件中。在我的`Portfolio React`应用程序中，我的组件文件夹如下所示:

```
components/
    About.js
    Calendar.js
    Contact.js
    Footer.js
    Header.js
    Home.js
    Skills.js 
```

它位于`src`:

```
src/
    -components/
        About.js
        Calendar.js
        Contact.js
        Footer.js
        Header.js
        Home.js
        Skills.js
        Work.js 
```

这是我的`images`文件夹的样子:

```
images/
    -icons/
        footerGithub.png
        footerGoogle.png
        footerLinkedin.png
        footerTwitter.png
        github.svg
        googleplus.svg
        linkedin.svg
        twitter.svg
    -resume/
        mdcResume8217.pdf
    index.js
    profileSmall.png 
```

但是，我的`images`目录和我的组件`components`目录不在同一个目录中。它存放在`root`档案馆。

为什么？因为这是`webpack`精确复制我的`image`目录结构的唯一方式。这就是我的`webpack-prod.config.js`:

```
module: {
    rules: [
        {
            test: /\.(jpg|png|gif|svg|pdf|ico)$/,
            use: [
                {
                    loader: 'file-loader',
                    options: {
                        name: '[path][name]-[hash:8].[ext]'
                    },
                },
            ]
        },
    ],
}, 
```

`name`属性指的是一个`image`文件的`name`。`[path]`是指从`root`目录开始的图像文件的`path`。我不严格地使用这个术语，因为从技术上来说，应用程序的`root`目录是它的顶层目录。在我的例子中，应该是`portfolio-react`。然而，`src`目录，其中`webpack`提取它需要的数据来捆绑我们的应用程序运行所依赖的文件，然后文件路径的剩余部分是在`production`中的`destination`文件夹中复制的，即`dist`。因此，如果开发中的映像的路径是`simg/img.jpg`，它将以同样的方式复制到`dist`目录，但是将`dist`作为最顶层的目录:`dist/simg/img.jpg`。那就太怪异了，而且行不通！

通过将`images`目录放在`root`中的`src`之外，图像目录在`dist`中以如下方式复制:

```
dist/
        -images/
            profileSmall-0688089a.png
            -icons/
                footerGithub-8d086876.png
                footerGoogle-c7c39c36.png
                footerLinkedin-9a80860c.png
                footerTwitter-cf5ffa5b.png
                github-ff66eb8e.svg
                googleplus-603de14e.svg
                linkedin-bc8e55bb.svg
                twitter-93a9fd6a.svg
            -resume/
                mdcResume8217-17c81764.pdf
        -src/
            favicon-08080867.ico 
```

我在我的`package.json`中创建了一个小的`script`,以便在运行新的构建之后删除源文件夹:

```
"cleanSrc": "rimraf dist/src", 
```

我已经安装了`rimraf` npm 包，并将其用于我的`"clean": "rimraf dist"` `script`，所以创建另一个很容易。

为了在我的`Portfolio React`应用程序中正确加载我的图像，还需要最后一个关键步骤，因为组件和图像目录不在同一个目录中。我在 images 目录中创建了一个`index.js`文件。它包括导出`images`目录及其任何`sub-directories`目录中的所有文件:

```
export profileSmall from './profileSmall.png';

export mdcResume8217 from './resume/mdcResume8217.pdf';

export linkedin from './icons/linkedin.svg';
export googleplus from './icons/googleplus.svg';
export github from './icons/github.svg';
export twitter from './icons/twitter.svg';

export footerTwitter from './icons/footerTwitter.png';
export footerGithub from './icons/footerGithub.png';
export footerGoogle from './icons/footerGoogle.png';
export footerLinkedin from './icons/footerLinkedin.png'; 
```

如果我没有完成这最后一步，我的图像就不会出现！我也不能以下面的(正确的)方式将它们导入到我的组件中:

```
import React from 'react';
import Typist from 'react-typist';

import linkedin from '../img/icons/linkedin.svg';
import googleplus from '../img/icons/googleplus.svg';
import github from '../img/icons/github.svg';
import twitter from '../img/icons/twitter.svg';

export const Contact = () => (
    <div className="Contact-content">
        <div className="contact-social">
            <h2 className="title-social">Follow me</h2>
            <ul>
                <li>
                    <a href="https://twitter.com/letsbsocial1" target="_blank">
                        <img className="twitter" src={twitter} width="40" alt="twitter"/>
                    </a>
                </li>
                <li>
                    <a href="https://github.com/interglobalmedia" target="_blank">
                        <img className="github" src={github} width="40" alt="github"/>
                    </a>
                </li>
                <li>
                    <a href="https://plus.google.com/u/0/110861192597778984723" target="_blank">
                        <img className="google-plus" src={googleplus} width="40" alt="google plus"/>
                    </a>
                </li>
                <li>
                    <a href="https://www.linkedin.com/in/mariacampbell/" target="_blank">
                        <img className="linkedin" src={linkedin} width="40" alt="linkedin"/>
                    </a>
                </li>
            </ul>
            <div className="follow">
                <li>
                    <a href="https://medium.com/@letsbsocial1" target="_blank">Medium</a>
                </li>
                <br/>
                <li>
                    <a href="http://www.mariadcampbell.com/" target="_blank">Dev. Blog</a>
                </li>
                <li>
                    <a href="http://interglobalmedianetwork.com/" target="_blank">co. blog</a>
                </li>
            </div>
            <div className="email">
                <h2 className="title-social">Email</h2>
                <li>
                    <a href="mailto:interglobalmedia@gmail.com">interglobalmedia@gmail.com</a>
                </li>
            </div>
            <div className="contribute">
                <h2 className="title-social">Contributor to</h2>
                <li>
                    <a href="https://blog.hellojs.org/" trget="_blank">hello.js</a>
                </li>
                <br/>
                <li>
                    <a href="https://dev.to/letsbsocial1" target="_blank">The Practical Dev</a>
                </li>
            </div>
        </div>
    </div>
) 
```

转眼间。您已经以这样的方式构建了您的`React`应用程序，使得`React`能够正确地解释您的图像文件。这确保您可以将它们导入到您的组件中。您已经成功地配置了您的`webpack-dev.js`和`webpack-prod.config.js`，以便它将您的所有映像和映像`types`正确地加载到您的`React`应用程序的开发和生产构建中。

**相关资源:**

[Github 上的 Portfolio React 应用](https://github.com/interglobalmedia/portfolio-react)

[webpack url-loader](https://webpack.js.org/loaders/url-loader/)

[网页包档案载入器](https://webpack.js.org/loaders/file-loader/)