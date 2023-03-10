# 创建网站的过程(HTML & CSS)

> 原文：<https://dev.to/codetheweb/the-process-creating-a-website-html--css-2lo>

> 本文由[亚里沙·杜比克-威尔森](https://adubik.github.io/)建议——谢谢！如果你也想推荐一篇文章，你可以[联系我](https://codetheweb.blog/contact)或者在评论中告诉我。

## 只是一个音符…

这篇文章不是向你展示创建一个网站的实际技术部分，而是指导你创建一个网站的整个过程。只是让你知道…

## 最终产品

这是我今天要建的网站的最终产品。看一看它，并在我们浏览教程时记住它。

[![The final product!](img/575f628b4fcfc83abd9ced6fc86065e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--izpcYyKj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/final-product.gif)

这将是一个关于美洲驼 Boberick 的虚构网站(我有时会有奇怪的想法……)

## 1。规划你的布局

任何网站的第一步总是要知道你想在上面放什么，以及(模糊地)你想让它看起来怎么样。所以，第一步是画一个草图——在纸上或者在电脑上，取决于你觉得哪一个更容易。

记住，_ **不一定要好看。** _ 下面是我的: [![A sketch of what the website will look like](img/9768b2c40e0e5656d21ebe716864a9fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8de3wJs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/sketch.png)

如你所见，它非常粗糙。线不是直的，没有什么是均匀的，但我仍然可以看到网站将如何看，我需要有什么部分。

在这个布局中，我有一个标题(导航栏)，三个部分和一个页脚。

## 2。设置“样板代码”

现在，是时候获取任何网站开始时的基本代码了(这通常被称为*样板文件*)。

为此，请:

1.  在电脑上为网站创建新文件夹
2.  在里面新建空的`index.html`和`style.css`文件
3.  将[基本‘样板代码’](https://codetheweb.blog/2017/10/07/structure-of-an-html-document/)添加到您的`index.html`文件中:

```
<!DOCTYPE html>
<html>
    <head>
        Boberick the llama
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
        <h1>Just testing this works!</h1>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

最后，在网络浏览器中打开你的`index.html`，检查一切正常: [![The boilerplate code](img/0116f2ef88209efa2c7202c2dd35684b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3IY-xXI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/base-code.png)

> 这篇文章将更多地解释创建一个网站的过程，所以我不会详细解释实际的代码——但是如果你愿意，你仍然可以跟着做。
> 
> 如果是这样，请按照上面的步骤开始！

## 3。在布局中创建元素

现在是时候创建您在步骤 1 中计划的布局/部分元素了！

最好的方法是使用[语义元素](https://codetheweb.blog/newsletter) : `<header>`、`<main>`、`<section>`和`<footer>`。

下面是 HTML:

```
<!DOCTYPE html>
<html>
    <head>
        Boberick the llama
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
        <header>

        </header>
        <main>
            <section id="hero">

            </section>
            <section id="about">

            </section>
            <section id="contact">

            </section>
        </main>
        <footer>

        </footer>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们给出的是`<section>` s `id` s，所以我们以后可以参考它们。

如果你重新加载页面，你会看到什么都没有——这是因为我们只是创建页面的部分，而不是实际的内容。

## 4。填充 HTML 内容

一旦你有了页面的各个部分，就该把它们填满了！如果你知道你将要使用什么内容，把它放进去。如果没有，放入一些[虚拟文本](https://lipsum.com/feed/html)，稍后用实际内容替换它。

下面是填入一些内容后的 HTML:

```
<!DOCTYPE html>
<html>
    <head>
        Boberick the llama
        <link rel="stylesheet" href="style.css">
    </head>

    <body>
        <header>
            <img src="https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/llama.jpg" class="profile-img">
            <nav>
                <ul>
                    <li><a href="#hero">Home</a></li>
                    <li><a href="#about">About</a></li>
                    <li><a href="#contact">Contact</a></li>
                </ul>
            </nav>
        </header>
        <main>
            <section id="hero">
                <div class="section-inner">
                    <img src="https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/llama.jpg" class="profile-img">
                    <h1>Hi, I'm Boberick the llama.</h1>
                </div>
            </section>
            <section id="about">
                <div class="section-inner">
                    <h2>About me</h2>
                    <p>I'm a really awesome llama. Every day I wake up, munch on some grass, do some coding and then go back to sleep.</p>
                    <h3>Achievements</h3>
                    <ul>
                        <li>Bachelor of photogenic posing, 2010</li>
                        <li>Llamaness certification from the Llama Institute, 2014</li>
                        <li>I coded a website, 2017</li>
                    </ul>
                </div>
            </section>
            <section id="contact">
                <div class="section-inner">
                    <h2>Contact me</h2>
                    <p>You can find me on:</p>
                    <ul>
                        <li><a href="https://twitter.com/llama">Twitter</a></li>
                        <li><a href="https://www.reddit.com/user/llama">Reddit</a></li>
                        <li><a href="https://www.instagram.com/llamasporfavor/">Instagram</a></li>
                    </ul>
                    <p>Or, you can <a href="mailto:llama@codetheweb.blog">send me an email</a>.</p>
                </div>
            </section>
        </main>
        <footer>
            © Copyright Boberick The Llama, 2017
        </footer>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果你重新加载页面，你会看到我们现在有一些内容！[![The HTML content of our site (without CSS)](img/7f9eb0367e006c7c4ed867f4fcdb5f30.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Qx39JQu9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/html-content.png)

## 5。添加一些基本的布局 CSS

一旦我们完成了 HTML，是时候继续学习 CSS 了！首先要关注的也是最重要的部分是让它看起来像我们的布局——然后我们可以关注细节。

这意味着我们需要关注像`width`、`height`、`margin`、`padding`、`position`和`display`这样的属性。此外，我们需要确保图像的大小是正确的，这样他们就不会抹杀页面。

这是我们将添加到`style.css` :
中的 CSS

```
body {
    margin: 0;
    margin-top: 50px;
}

header {
    display: flex;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 50px;
    line-height: 50px;
    background-color: #eee;
}

header * {
    display: inline;
    height: 50px;
}

header ul {
    padding: 0;
}

header li {
    margin-left: 20px;
}

section {
    height: 100vh;
    border: 1px solid black;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
}

#hero .profile-img {
    width: 300px;
}

footer {
    text-align: center;
    padding: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们只是添加样式来使我们的整体布局看起来相似，而不是单独的内容。我们确保将这些部分设置为 [100%的视窗高度](https://codetheweb.blog/2017/12/04/css-units/#vw--vh)，使标题具有一个[固定位置](https://codetheweb.blog/2017/11/21/css-position-property/#position-fixed)，将项目[放置在标题](https://codetheweb.blog/newsletter)中，等等。我们还使用[flexbox](https://codetheweb.blog/2017/12/05/css-flexboxes/)将内容放在各个部分的中央。

这是结果: [![Now our page looks similar to the sketch!](img/0462d68f13893a09ad3f3c7c613dcd87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7bHJbGqC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/layout-css.gif)

## 6。添加更多特定的样式

一旦网站的基本框架完成，我们可以添加更多的具体风格。

现在我们可以让我们的网站看起来更好了！

下面是我们的 CSS:

```
body {
    margin: 0;
    margin-top: 50px;
    font-family: sans-serif; /* Add this line */
}

header {
    display: flex;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 50px;
    line-height: 50px;
    background-color: #eee;
}

header * {
    display: inline;
    height: 50px;
}

header ul {
    padding: 0;
}

header li {
    margin-left: 20px;
}

section {
    height: 100vh;
    border: 1px solid black;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
}

#hero .profile-img {
    width: 300px;
    border-radius: 50%; /* Add this line */
}

footer {
    text-align: center;
    padding: 50px;
}

/* Add everything below here */

#hero h1 {
    font-size: 3em;
}

section h2 {
    font-size: 2.5em;
}

section h3 {
    font-size: 1.5em;
}

header a {
    text-decoration: none;
    color: black;
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们把标题变大了，把图像变圆了(使用`border-radius`)，并且改变了字体。我们还从标题链接中移除了一些默认样式。

下面是结果: [![Our website after adding more specific styles](img/a5499347533ebdded7e6682278f4f28d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hg0NmJb_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/specific-styles.gif)

## 7。添加颜色和背景

耶，我们现在在最后冲刺了！是时候给我们的网站添加点睛之笔了——颜色和背景！

这将使我们的网站看起来真的很棒。

下面是 CSS:

```
body {
    margin: 0;
    margin-top: 50px;
    font-family: sans-serif;
}

header {
    display: flex;
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    height: 50px;
    line-height: 50px;
    background-color: #eee;
}

header * {
    display: inline;
    height: 50px;
}

header ul {
    padding: 0;
}

header li {
    margin-left: 20px;
}

section {
    height: 100vh;
    border: 1px solid black;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;

    background-size: cover; /* Add this line */
    background-position: center center; /* Add this line */
    background-repeat: no-repeat; /* Add this line */
    background-attachment: fixed; /* Add this line */
}

#hero .profile-img {
    width: 300px;
    border-radius: 50%;
}

footer {
    text-align: center;
    padding: 50px;
}

#hero h1 {
    font-size: 3em;
}

section h2 {
    font-size: 2.5em;
}

section h3 {
    font-size: 1.5em;
}

header a {
    text-decoration: none;
    color: black;
}

/* Add everything below here */

#hero {
    background-image: linear-gradient(rgba(255,255,255,0.75),rgba(255,255,255,0.75)), url('https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/field.jpg');
}

#about {
    background-image: linear-gradient(rgba(255,255,255,0.75),rgba(255,255,255,0.75)), url('https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/beach.jpg');
}

#contact {
    background-image: linear-gradient(rgba(255,255,255,0.75),rgba(255,255,255,0.75)), url('https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/canyon.jpg');
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们已经为`section`元素添加了一些通用的[背景样式](https://codetheweb.blog/2017/11/29/advanced-background-images/)，并且为每个部分单独添加了一个`background-image`。

在`url('image.jpg')`之前放置`linear-gradient(rgba(255,255,255,0.75),rgba(255,255,255,0.75)),`的原因是因为否则文本难以阅读——所以我们在顶部添加了半透明的白色覆盖层。我在这里写了更多关于 T2 的内容。

[![Yay! We're done!](img/575f628b4fcfc83abd9ced6fc86065e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--izpcYyKj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codetheweb.blog/assets/img/posts/steps-to-creating-a-website/final-product.gif)

## 8。庆祝一下！🎉

呜！你终于完成了你的网站！现在，向你的朋友、家人和整个互联网炫耀吧😉

## 进一步阅读

如果您想了解更多关于我今天展示的特定部分的信息，请查看:

1.  [如何打造整版英雄形象](https://codetheweb.blog/2017/12/07/fullscreen-image-hero/)
2.  [如何使用 CSS 设计导航条的样式](https://codetheweb.blog/2017/12/12/style-a-navigation-bar-css/)

## 结论

所以，我希望你喜欢这篇文章，并且希望你在这个过程中学到了一些东西！

今天做了一件和平时有点不一样的事，说说你的想法吧。记住，这篇文章不是向你展示创建网站的实际技术部分，而是引导你完成创建网站的整个过程。

再次感谢[亚里沙·杜比克-威尔森](https://adubik.github.io/)对这篇文章的建议，别忘了[你也可以！](https://codetheweb.blog/contact)

如果你喜欢这篇文章，请做一个了不起的人，分享或者[注册时事通讯](https://codetheweb.blog/newsletter)，我会给你一个免费的玉米卷🌮(不尽然😜)

玩得开心，继续编码，我们下次再见，我将讨论如何使用 CSS 设计导航栏(标题)的样式——带你浏览我们今天创建的导航栏，但会更详细。到时候见！

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)