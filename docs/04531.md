# React 16 中的 requestAnimationFrame 和 polyfill

> 原文：<https://dev.to/letsbsocial1/requestanimationframe--polyfill-in-react-16-2ce>

如果你看了我之前的帖子， [***“我第一次使用 React 16.0”***](https://dev.to/letsbsocial1/my-first-time-using-react-160-ehf)，你就知道我在**终端**输入`npm run test`时得到了如下警告

```
> react-universal-blog-app@1.0.0 test /Users/mariacam/Development/react-universal-blog-app
> jest

PASS  src/sum.test.js
PASS  src/App.test.js
  â— Console console.error node_modules/fbjs/lib/warning.js:33
      Warning: React depends on requestAnimationFrame. Make sure that you load a polyfill in older browsers. http://fb.me/react-polyfills Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        1.669s
Ran all test suites. 
```

Enter fullscreen mode Exit fullscreen mode

注意到什么不同了吗？

```
console.error node_modules/fbjs/lib/warning.js:33
  Warning: React depends on requestAnimationFrame. Make sure that you load a polyfill in older browsers. http://fb.me/react-polyfills 
```

Enter fullscreen mode Exit fullscreen mode

[React Polyfills](http://fb.me/react-polyfills) 链接把我带到了 **Github** 上标题为
***React 16 JavaScript environment . MD***的**要点**。在那里，我了解到 React 16 依赖于集合类型[映射](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)和[集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)。 ***和*** ，说明如果开发商

> 正在支持旧的浏览器和设备，这些浏览器和设备可能还没有提供这些功能(例如，IE < 11), (they should) consider including a global polyfill in (their) bundled application, such as [core-js](core-js(https://github.com/zloirock/core-js) 或 [babel-polyfill](https://babeljs.io/docs/usage/polyfill/) )。

由于他们的例子使用了`core-js`，我决定走那条路线。

接下来，**终端**的警告也让我知道**的反应**取决于`requestAnimationFrame`、**、T6】甚至、测试环境**。而 **React 团队**，具体来说就是***@ gae Aron(Dan abra mov)***声明:

> **React** 也依赖于**requestAnimationFrame**(即使在测试环境下)。对于 ***测试环境*** 来说，一个简单的垫片应该是:` ` markdown global . requestanimationframe = function(callback){ setTimeout(callback，0)；};

所以我先用`npm` :
安装了`core-js`

```
npm install core-js --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`index.js`中，我在顶部添加了以下内容:

```
import React, { Component } from 'react';
import { render } from 'react-dom';
import 'core-js/es6/map';
import 'core-js/es6/set';
import img/favicon.ico'; 
```

Enter fullscreen mode Exit fullscreen mode

而在`App.test.js`里，我的 ***进口*** 的正下方，我加了:

```
global.requestAnimationFrme = function(callback) {
    setTimeout(callback, 0);
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来我安装了`raf` :

```
npm install raf --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

之后，我在我的`Jest configuration`内的`package.json`中添加了以下内容:

```
"jest": {
    "setupFiles": [
      "raf/polyfill"
    ],
    "moduleNameMapper": {
      "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js",
      "\\.(css|less)$": "identity-obj-proxy"
    }
  }, 
```

Enter fullscreen mode Exit fullscreen mode

最后，我决定安装`identity-obj-proxy` **npm 包**，这样我就可以在`Jest Snapshot Testing`中为我的`className lookups`安装`mock a proxy`，因为我使用了`CSS Modules` :

```
npm install identity-obj-proxy --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

来了[推荐](https://facebook.github.io/jest/docs/en/webpack.html)。

然后我再次输入`npm run test`看看会发生什么。这是在**终端** :
返回的内容

```
PASS  src/App.test.js
 PASS  src/sum.test.js

Test Suites: 2 passed, 2 total
Tests:       2 passed, 2 total
Snapshots:   0 total
Time:        1.274s 
```

Enter fullscreen mode Exit fullscreen mode

成功！我添加了 **React 16** ( ***至今*** )所需的所有新特性，我所有的警告和错误都没有了。

我认为 **React 团队**正在意识到越来越多的开发者要么定制 **create-react-app** 要么开发他们自己的**工作流**。无论如何，我很感谢所有这些令人敬畏的文件。感谢 **React** ，特别感谢 **@gaearon (Dan Abramov)** ，是他做出了这个**的主旨**！

**相关文章:**

[React v16.0](https://facebook.github.io/react/blog/2017/09/26/react-v16.0.html) 、**T3】Facebook . github . ioT5】**

[使用 requestAnimationFrame](https://css-tricks.com/using-requestanimationframe/) 、**和 *CSS 技巧*和**

[window . requestanimationframe()](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame)，**T3】MDNT5】**

[用 requestAnimationFrame 制作动画](https://www.kirupa.com/html5/animating_with_requestAnimationFrame.htm)、**T3、基鲁帕**

[智能动画制作的 request animation frame](https://www.paulirish.com/2011/requestanimationframe-for-smart-animating/)，**T3【保罗爱尔兰 T5】， **requestAnimationFrame 的创建者****