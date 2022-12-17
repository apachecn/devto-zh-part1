# 将 Github 页面用于生产就绪的应用程序是个坏主意吗？

> 原文：<https://dev.to/sabatesduran/is-a-bad-idea-to-use-github-pages-for-a-production-ready-app-8on>

我试着不花钱托管一个 react 应用程序，我发现了这个包:

*   [gh-pages](https://github.com/tschaub/gh-pages) :将文件发布到 GitHub 上的 gh-pages 分支(或任何其他地方的任何其他分支)

通过添加该脚本，您可以构建代码并将其直接推送到`gh-pages`，并在几秒钟内发布您的应用:

```
"scripts": {
  "deploy": "gh-pages -d build"
} 
```

Enter fullscreen mode Exit fullscreen mode

你认为用 Github 页面制作是个坏主意吗？