# 在 headless Chrome 中运行角度测试

> 原文：<https://dev.to/cvuorinen/running-angular-tests-in-headless-chrome-9h9>

Angular 有一些很棒的工具来运行测试，即 Karma 和量角器。默认情况下(至少在使用 Angular CLI 时)，它们使用 Chrome 运行。因此，当您从命令行执行测试时，它会弹出一个浏览器窗口来执行测试。这已经足够好了，但是有时您不希望看到浏览器窗口弹出，或者您正在没有图形环境的环境中运行测试(例如在 CI 服务器或 Docker 容器上)。

在没有浏览器窗口的情况下运行 Karma 测试并不是什么新鲜事，你已经可以通过安装`karma-phantomjs-launcher`用 PhantomJS 来做了。对于这个问题，PhantomJS 已经是足够好的解决方案了，但是您可能会不时地遇到一些问题，并且需要添加一些额外的 polyfills 等。但是 Chrome 现在从第 59 版开始可以在[无头模式下运行，所以你可以用它来运行测试，而不需要安装任何额外的包，并且有一个更标准的环境。](https://www.chromestatus.com/feature/5678767817097216)

## 传达你的因缘

无论您有一个用 Angular CLI 生成的 Karma 配置还是手动创建的配置，您都可以使用一个名为**custom launcher**的配置选项，通过为它定义额外的标志来基于现有的启动器创建一个新的启动器。这同样适用于较旧的 AngularJS v1.x 项目以及较新的 Angular v2.x-4.x 项目。要在无头模式下使用 Chrome，您需要在您的`karma.conf.js`
中添加以下部分

```
customLaunchers: {
  ChromeHeadless: {
    base: 'Chrome',
    flags: [
      '--headless',
      '--disable-gpu',
      // Without a remote debugging port, Google Chrome exits immediately.
      '--remote-debugging-port=9222',
    ],
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，根据你的 Chrome 版本，可能不需要`--disable-gpu`标志。

然后你可以替换掉`browsers`部分的任何内容(Chrome 或者 PhantomJS 等等。)带`ChromeHeadless`。就这样，之后你就可以享受运行测试而不会弹出任何浏览器窗口了。

## E2E 用量角器测试

在无头模式下运行 E2E 测试有点困难，因为它不能很好地处理幻想曲。基本上你唯一的选择是在 Xvfb 中运行 Chrome(如果你想知道的话，那就是 X 虚拟帧缓冲区)。但是现在只需[在`protractor.conf.js`](https://github.com/angular/protractor/blob/master/docs/browser-setup.md#using-headless-chrome) 中添加几行代码，就可以在无头模式下运行 E2E 测试。您需要在`capabilities`键下添加以下选项(这里您应该已经有了`browserName: 'chrome'` ):

```
chromeOptions: {
  args: ["--headless", "--disable-gpu", "--window-size=800x600"]
} 
```

Enter fullscreen mode Exit fullscreen mode

关于`karma.conf.js`和`protractor.conf.js`的完整示例，请参见[本要点](https://gist.github.com/cvuorinen/543c6f72f8ec917ebfd596802d387aa3)(两个文件均由 Angular CLI 生成，仅做了上述更改)。

## 前进，测试所有的东西

现在，您可以高枕无忧，享受运行所有测试的乐趣，没有任何令人分心的浏览器窗口，或者更重要的是，您可以在您的持续集成服务器上运行它们，如 Travis CI 或 Jenkins 等。

有关更多信息，请参见谷歌开发者网站上的[无头浏览器](https://developers.google.com/web/updates/2017/04/headless-chrome)入门。