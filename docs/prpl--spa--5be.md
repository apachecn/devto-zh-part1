# 从 PRPL 模式考虑非 SPA 性能时的笔记

> 原文：<https://dev.to/aggre/prpl--spa--5be>

我们以[PRPL](https://developers.google.com/web/fundamentals/performance/prpl-pattern/) 模式为构思起点，考虑了为了在非 SPA 结构的网络 APP 中发挥效果的措施。

# Motivation

通过 JavaScript 进行基于组件的开发使前端的实现变得高效了。

但是，在 toC 的服务中，为了 Bot 友好的政策成为了负担。 虽然也有只在服务器端制作`<meta>`标签的对策，但是如果包括 Pocket 和未知服务的话，仍然很多情况下需要*正文*。 我个人在发行几个 SPA 的过程中，也感受到了明明是作为文档的 HTML 却不运行脚本就无法成为文档的违和感。

因此，考虑了通过在服务器端只生成为了作为文档成立的最小限度的要素，来避免 Bot 措施。

为了在保持 SPA 的状态下处理作为*文档成立的 HTML* ，判断出会出现紧密结合的结构或 SSR 的必要条件，从而决定废除 SPA。

即使不是 SPA，除面向文档的要素以外，作为 Web Components 也可以继续开发。

在此基础上探讨了提高绩效的措施。

# Policy

以 PRPL 模式为起点考虑的政策如下。

## 推送

HTTP/2 的推送目前很难考虑浏览器的缓存，所以暂缓应对。

### 终点

例如，在对`/page/1`的请求中，返回以下 HTML。

```
<!DOCTYPE html>
<html lang="en-US" dir="ltr">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1,user-scalable=yes">
    Page 1

    <!-- Preload CSS -->
    <link rel="preload" as="style" href="/style.css" onload="this.rel='stylesheet'">

    <!-- Preload critical Custom Element -->
    <link rel="preload" as="script" href="/x-header.js" onload="var s = document.createElement('script');
    s.src = this.href;
    document.body.appendChild(s);">

    <!-- Preload service worker -->
    <link rel="preload" as="script" href="/service-worker.js">

    <!-- Preload lazy load script -->
    <link rel="preload" as="script" href="/lazy-load.js" onload="var s = document.createElement('script');
    s.src = this.href;
    document.body.appendChild(s);">

    <!-- Prefetch next page -->
    <link rel="prefetch" href="/page/2">

    <!-- Prefetch home page -->
    <link rel="prefetch" href="/">

    <link rel="manifest" href="/manifest.json">
</head>
<body>
    <!-- Custom Element for header -->
    <x-header></x-header>

    <main>
        <article>
            <section>
                <h1>Title</h1>
                <p>The content</p>
                <p>

                    <!-- Custom Element for article -->
                    <x-content-gallery lazy-load="/x-content-gallery.js"></x-content-gallery>

                </p>
                <p><a href="/page/2">Next</a></p>
            </section>
        <article>
    </main>

    <!-- Custom Element for footer -->
    <x-footer lazy-load="/x-footer.js"></x-footer>

    <script async defer src="/polyfill-of-preload.js"></script>
    <script>
        // Add a service worker
        'serviceWorker'in navigator&&window.addEventListener('load',function(){navigator.serviceWorker.register('/service-worker.js')});
    </script>
</body>
</html> 
```

进行关键资源的`preload`和有可能转移的页面的`prefetch`。

`lazy-load`被追加了属性的要素在该要素出现在视点内的时候进行依存解决。 由于该属性名称是临时的，而且在视口中出现的时候也有损害 UX 的风险，所以需要下功夫。

## 渲染

渲染 HTML。

因为不是 SPA，所以不需要针对不同路线的处理。

## 预缓存

因为它不是 SPA，所以不会由服务工作器进行预缓存。

Service Worker 仅限于通信发生时的缓存和通知等用途。

## 懒人加载

由于不是 SPA，所以与 PRPL 图案中的 Lazy-load 的趣味略有不同。

我们理解，PRPL 模式将采取在不同的路线上延迟读取资源，以及在视口中出现图像等资源时延迟读取的方式。

由于这次不是 SPA，所以不考虑不同路线的延迟读取。

# 体系结构

确保可以通过 REST API 检索数据源。

在此基础上，考虑到尽量使本地和服务器接近的结构，HTML 的发送想让用 Node.js 等建立的服务器就这样在云中运行。

在这种情况下，REST API 可以无服务器构建，但 HTML 很难无服务器构建。

但是，如果数据源是 REST API，则 HTML 可以无状态返回，因此 CDN 缓存应该可以有效地工作。

* * *

我想在早期阶段将这个结构投入实战。