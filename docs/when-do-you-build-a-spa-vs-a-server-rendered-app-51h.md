# 什么时候构建 SPA，什么时候构建服务器渲染的应用？

> 原文：<https://dev.to/_bigblind/when-do-you-build-a-spa-vs-a-server-rendered-app-51h>

在我正在开发的一个 web 应用中，我们计划将前端移至 ReactJS。我在考虑是将其构建为 SPA(单页应用程序)更好，在这种情况下，服务器只公开一个 RESTful API 供前端使用，还是构建为多页应用程序更好，在这种情况下，服务器呈现每个页面中不需要交互的部分，而我们使用 React 来处理交互部分。

你会考虑哪些因素来做出这样的决定？