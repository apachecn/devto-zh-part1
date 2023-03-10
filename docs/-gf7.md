# 制作了只随机显示颜色的服务

> 原文：<https://dev.to/jgs/-gf7>

[![](img/be10b5cd96ddb651c5b87ba36da93dbb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rMuYl-ee--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kotbjrtjwxwx7y2ubo91.png)

[https://color.kbys.tk/](https://color.kbys.tk/)

因为在野外使用时需要一点随机的颜色，所以想快速地制作。

最近知道一个叫 unpkg.com 的 CDN，在这里指定 npm 中注册的包和版本后，文件就会被发布。 有趣的是加上`?module`后，就会被传递到与 ES modules 对应的形式上。

例えば[https://UNP kg . com/@ angular/router @ 4 . 3 . 5/@ angular/router . es5 . js？模块](https://unpkg.com/@angular/router@4.3.5/@angular/router.es5.js?module) だと

```
// ...
import { BehaviorSubject } from 'https://unpkg.com/rxjs@^5.0.1/BehaviorSubject?module';
import { Subject } from 'https://unpkg.com/rxjs@^5.0.1/Subject?module';
import { from } from 'https://unpkg.com/rxjs@^5.0.1/observable/from?module';
import { of } from 'https://unpkg.com/rxjs@^5.0.1/observable/of?module';
import { concatMap } from 'https://unpkg.com/rxjs@^5.0.1/operator/concatMap?module';
import { every } from 'https://unpkg.com/rxjs@^5.0.1/operator/every?module';
// ... 
```

Enter fullscreen mode Exit fullscreen mode

这样的感觉在`import`句之后也被重写为`unpkg.com`。 用这个，不在手边构建就试着做了 APP，但是好像在 CommonJS 上写了依存就无法解决，不能使用自己喜欢的极简库。 很遗憾。

于是，不知如何是好，结果在`preact-cli`中一下子做出了雏形，一下子部署在了 Netlify 上。

没有做什么特别夸张的事，坦率地只写 JS 和 CSS 的话就形成了形式，是个方便的时代。