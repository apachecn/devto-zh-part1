# 我在 Angular SPA 上进行二维码解码的最佳选择 npm 包

> 原文：<https://dev.to/j_sakamoto/the-npm-package-of-my-best-choice-for-qr-code-decoding-on-angular-spa-4747>

最近，我需要在我的 Angular Web 应用程序项目中实现使用摄像设备检测和读取二维码的功能。

所以我研究了一下哪个 npm 包最适合我实现解码二维码的功能。

我找到的 npm 包列表是这样的。

*   jsQR
*   情况
*   中兴打字稿
*   ngx-z 型
*   二维码阅读器

我的 Angular SPA 项目简介是这样的:

*   角度- v.5.0
*   node.js - v.8.9.3
*   npm - v.5.6.0
*   TypeScript - v.2.4.2
*   模块 bandler - webpack - v.2.5.1(我不使用 angular CLI)

# 我的最佳选择是“jsQR”

*   [https://www.npmjs.com/package/jsqr](https://www.npmjs.com/package/jsqr)([GitHub](https://github.com/cozmo/jsQR))
*   阿帕奇-2.0

这个包只提供解码二维码功能，管理相机设备，视频和画布 DOM 元素，不支持。

我应该实现从相机设备捕捉视频，预览视频流，从预览中抓取图像数据，等等。

但是这个包工作得很好，并且容易使用。

我应该做的只是手动创建一个 TypeScript 类型定义文件，就像这样。

```
// jsQR.d.ts
declare module 'jsQR' {
  export default function jsQR(imageData: Uint8ClampedArray, width: number, height: number): {
        data: string
  } | null;
} 
```

Enter fullscreen mode Exit fullscreen mode

将此包导入后，

```
import jsQR from "jsQR"; 
```

Enter fullscreen mode Exit fullscreen mode

可以调用“jsQR”函数解码二维码。

当你用二维码的图像数据调用“jsQR”函数时，你可以得到解码的结果作为返回值。

仅此而已。

这个 API 非常简单。不需要注册回电，或者使用“承诺”等。

# 不能用——“instascan”

*   [https://www.npmjs.com/package/instascan](https://www.npmjs.com/package/instascan)([GitHub](https://github.com/schmich/instascan))
*   用它

这个包在我的谷歌搜索结果中很受欢迎。

该软件包提供了实现 QR 码解码功能所需的一切。我要做的只是标记视频标签并绑定它。

听起来对我很好。

所以在我研究之初，这个包是最有影响力的候选。

但是，在将这个包安装到我的项目中之后，webpack 报告了构建错误，就像这样:

```
ERROR in ./node_modules/instascan/src/zxing.js
Module not found: Error: Can't resolve 'fs' in '/Users/enzo/Copy/projects/elevenyellow/coinfy/node_modules/instascan/src'
resolve 'fs' in '/Users/enzo/Copy/projects/elevenyellow/coinfy/node_modules/instascan/src' 
```

Enter fullscreen mode Exit fullscreen mode

我从谷歌搜索中找到了这个错误的解决方法。

[https://github . com/schmich/instascan/issues/121 # issue comment-357757911](https://github.com/schmich/instascan/issues/121#issuecomment-357757911)

我编辑了“webpack.config.js”以应用于变通办法，然后构建错误消失了。

但是，接下来，我在浏览器中遇到了这样的运行时错误:

```
Error: Zone.js has detected that ZoneAwarePromise `(window|global).Promise` has been overwritten.
Most likely cause is that a Promise polyfill has been loaded after Zone.js (Polyfilling Promise api is not necessary when zone.js is loaded. If you must load one, do so before loading zone.js.) 
```

Enter fullscreen mode Exit fullscreen mode

这个错误意味着这个包与“zone.js”冲突。

"zone.js "是项目对 Angular 的依赖。

这个错误让我决定不使用这个包。

“istascan”仍然是一个很棒的包，而且非常有用，我认为。

但与 Angular SPA 不兼容。

# 不能使用- "zxing-typescript "

*   [https://www.npmjs.com/package/zxing-typescript](https://www.npmjs.com/package/zxing-typescript)([GitHub](https://github.com/aleris/zxing-typescript))
*   阿帕奇-2.0

这个包在包名中有“TypeScript”这个词，所以我希望我可以在 Angular SPA 项目中轻松地使用这个包。

但是，在安装了这个包之后，webpack 报告了许多类似这样的构建 TS2322 TypeScript 错误行。

```
ERROR in [at-loader] ./node_modules/zxing-typescript/src/browser/BrowserCodeReader.ts:281:9
    TS2322: Type 'CanvasRenderingContext2D | null' is not assignable to type 'CanvasRenderingContext2D'.
  Type 'null' is not assignable to type 'CanvasRenderingContext2D'. 
```

Enter fullscreen mode Exit fullscreen mode

我猜，这个包是用老版本 TypeScript 实现的，包里不仅有 transpiled 后的 JavaScript 还有 TypeScript 源代码。

因此，在较新的 TypeScript 环境中，此包不能传输。

我认为，npm 包不应该包含 TypeScript 源代码。在 transpile 和类型定义文件之后，它应该只包含 JavaScript 文件。

无论如何，这个错误使我决定不使用这个包。

# 没试过——也许不能用——“ngx-zxing”

*   [https://www.npmjs.com/package/ngx-zxing](https://www.npmjs.com/package/ngx-zxing)([GitHub](https://github.com/werthdavid/ngx-zxing))
*   用它

该软件包提供了一个二维码解码功能，作为一个角度组件。

我的项目是一个有棱角的 SPA 项目，所以我觉得这个套餐可能是和我的项目最匹配的。

但是当我阅读这个包中的自述文件时，我发现了下面的描述:

> “这个库包装了 zxing-typescript...

这个描述让我害怕遇到很多 TS2322 类型的错误，就像我尝试使用“zxing-typescript”一样。

所以我决定不尝试就不使用这个包。

# 不确定，但也许可以用——“二维码阅读器”

*   [https://www.npmjs.com/package/qrcode-reader](https://www.npmjs.com/package/qrcode-reader)([GitHub](https://github.com/edi9999/jsqrcode))
*   阿帕奇-2.0

这个包只提供解码二维码的功能，就像“jsQR”一样。

像这样导入这个包后，你可以实例化 QR code 类，并使用 QrCode 对象解码一个 Qr Code。

```
import QrCode from 'qrcode-reader'; 
```

Enter fullscreen mode Exit fullscreen mode

请记住，您应该创建这样的类型定义文件:

```
// qrcode-reader.d.ts
declare module 'qrcode-reader' {
  export default class QrCode {
    // You should append this line with QrCode member's definiton.
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我成功地将这个包安装到我的项目中，导入它，构建项目，并运行它，没有任何错误。

但是我没有检查这个包的解码功能，因为我已经决定使用“jsQR”。

我没有得到证实，但我觉得这个包可能可以用在一个角温泉项目。

# 结论

*   **jsQR** - **我的最佳选择！**(但它只提供解码功能。)
*   instascan -不兼容 Angular。
*   zxing-typescript -与最新的 typescript 不兼容。
*   ngx-zxing -不确定，但可能不兼容最近的打字稿。
*   二维码-阅读器-不确定，但也许可以使用。

希望这篇文章对你有用。

快乐编码:)