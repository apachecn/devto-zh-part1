# 网络构建队列的工作原理

> 原文：<https://dev.to/saul/how-the-netlify-build-queue-works-4m1a>

我将 [Netlify](https://netlify.com) 与 [Contentful](https://contentful.com) 结合使用，并配置了一个[Contentful Outgoing web Hook](https://www.contentful.com/developers/docs/concepts/webhooks/)来在内容更新时触发一个 [Netlify 构建钩子](https://docs.netlify.com/configure-builds/build-hooks/)。这让我想知道 Netlify 是否操作一个构建队列，如果是，它是如何工作的。

(在撰写本文时)没有关于这个主题的正式文档，所以我跳到了 [Netlify Gitter 对话](https://gitter.im/netlify/community)上，看看我能找到什么。谢天谢地[马蒂亚斯·比尔曼](https://github.com/biilmann)(Netlify 的创始人)在旁边启发我。

原来他们确实使用了一个构建队列，这就是它的工作方式:

> 我们将开始第一个，然后一旦完成，我们的系统将所有的中间构建标记为“跳过”，然后运行最后一个

谢天谢地，这是我希望的逻辑🎉

在[内容丰富的内容预览 API](https://www.contentful.com/developers/docs/references/content-preview-api/) 的情况下，当内容正在被编辑时，webhooks 可以被相当频繁地触发(高达每分钟 3 次)。我担心这是在创造一种竞争条件，即*可能会*导致陈旧内容战胜新鲜内容。谢天谢地，这些担心是没有根据的。