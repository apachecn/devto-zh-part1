# 在角度路线变更上滚动到顶部

> 原文：<https://dev.to/teroauralinna/scroll-to-the-top-on-angular-route-change-1gn5>

默认情况下，当您导航到另一个页面时，Angular 不会滚动到页面顶部。这里有一个如何实现滚动的快速提示。

## 实现滚动服务

首先创建一个名为`ScrollTopService`的新服务。在服务器端(Angular Universal ),我们不需要这个“黑客”,所以它仅限于浏览器。

```
import { Injectable, Inject, PLATFORM_ID } from '@angular/core';
import { Router, NavigationEnd } from '@angular/router';
import { isPlatformBrowser } from '@angular/common';

@Injectable()
export class ScrollTopService {

  constructor(
    @Inject(PLATFORM_ID) private platformId: Object,
    private router: Router
  ) {}

  setScrollTop() {
    if (isPlatformBrowser(this.platformId)) {
      this.router.events.subscribe((event: NavigationEnd) => {
        window.scroll(0, 0);
      });
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 将服务注入到组件中

将服务导入组件。

```
import { ScrollTopService } from '../your/path/scrolltop.service'; 
```

Enter fullscreen mode Exit fullscreen mode

然后调用组件中`ngInit`上的`setScrollTop`方法来初始化滚动。

```
ngOnInit() {
  this.scrollTopService.setScrollTop();
} 
```

Enter fullscreen mode Exit fullscreen mode