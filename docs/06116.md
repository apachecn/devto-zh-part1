# 考虑使用 PreactJs 进行开发

> 原文：<https://dev.to/ben/thinking-about-going-with-preactjs-for-devto>

[dev.to](https://dev.to/) web 应用程序基于“普通”JavaScript 构建，以实现特定于其架构/权衡的性能目的，因此我们没有大量使用库。但是它正在成长为一个有时复杂的应用程序，一些客户端代码变得有点像意大利面条。

我们仍将从服务器端呈现核心体验，但网站上在客户端处理的交互组件，如 follow 按钮，可能应该由状态驱动的框架来处理，如 React、Angular、Vue 等。

[PreactJS](https://preactjs.com/) 是一个 3kb 的视图库，旨在成为一个*方式更经济的*反应替代方案，我认为这对我们来说是正确的选择。我们在团队中有使用 React 的经验，这似乎是一种非常干净的方式来处理我们的问题。

有没有人对此有什么想法，或者知道我们应该查看的任何其他图书馆或博客帖子可以帮助我们做出这个选择？