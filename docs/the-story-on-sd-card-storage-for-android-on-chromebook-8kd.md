# Chromebook 上 Android 卡存储的故事

> 原文：<https://dev.to/codeledger/the-story-on-sd-card-storage-for-android-on-chromebook-8kd>

由于 Android 应用程序现在可以通过 arc++(Chrome 第二版的 Android 运行时)在 Chromebooks 上使用，来自传统文件存储背景的开发人员将必须确保他们的应用程序符合如何访问 SD 卡的优势。

Chromium 团队已经在 ChromeOS 62(Chromium-61？)或更高。这意味着，那些希望自己的应用能够像手机电池一样使用 SD 卡的开发者将会失望。这是因为 Android 框架不认为 Chromebook 上的那种“热插拔”SD 卡是可适应的存储设备。

此外，如果你想使用 MediaStore APIs 来访问它，你还是运气不好，因为 Android 框架不支持访问可移动硬件。Chromium 团队了解并正在考虑以下选项:

[https://bugs.chromium.org/p/chromium/issues/detail?id=789045 & desc=3](https://bugs.chromium.org/p/chromium/issues/detail?id=789045&desc=3)

那怎么办呢？

使用: [Android 存储访问框架](https://developer.android.com/guide/topics/providers/document-provider.html)

更多详情请见此 Commonware 在[可移动存储](https://commonsware.com/blog/2017/11/15/storage-situation-removable-storage.html)上的帖子。

原来是 StackOverflow 回答:[https://stackoverflow.com/a/44123748/295004](https://stackoverflow.com/a/44123748/295004)