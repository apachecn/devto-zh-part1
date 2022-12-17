# Spectre & Meltdown:这对移动应用有什么影响？

> 原文：<https://dev.to/nektro/spectre--meltdown-how-does-this-affect-mobile-apps-k9c>

老实说，我生活在一个网络泡沫中。虽然我也涉足其他语言，但 JavaScript 是我目前花费大部分时间研究的内容。通过它，我了解到由于 Spectre 和 Meltdown，web 浏览器(Chrome、Firefox、Edge 和 Safari)中的某些功能都禁用了 SharedArrayBuffers(同时允许在网页中的线程之间共享内存),并使性能指标的读数暂时变得不那么具体。

除此之外，Windows、Linux 和 macOS 同时都在开发操作系统级别的补丁。

所以我想我应该问一下 Android 或 iOS，因为我看到 ARM 也受到了影响。