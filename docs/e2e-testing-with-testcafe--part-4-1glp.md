# 用 TestCafe |重构进行 E2E 测试

> 原文：<https://dev.to/chrisvasqm/e2e-testing-with-testcafe--part-4-1glp>

在第 3 部分中，我们不仅学习了如何并行运行多个浏览器，还学习了这些浏览器的多个实例。

那么我们能做些什么来改进我们的 TestCafe 项目呢？

如果你从一开始就遵循这个指南，你的`devto.js`应该是这样的:

```
import { Selector } from 'testcafe'

fixture('DEV Tests')
    .page('http://dev.to/');

test("Check founder's names", async(t) => {
    const aboutLink = Selector('a').withText('About');
    const firstFounder = Selector('b').withText('Ben Halpern');
    const secondFounder = Selector('b').withText('Jess Lee');
    const thirdFounder = Selector('b').withText('Peter Frank');

    await t
    .click(aboutLink)
    .expect(firstFounder.exists).ok()
    .expect(secondFounder.exists).ok()
    .expect(thirdFounder.exists).ok();
});

test("Search for posts about testing", async (t) => {
    const discussTag = Selector('span').withText('#discuss');
    const discussTitle = Selector('h1').withText('Discussion');

    await t
        .click(discussTag)
        .expect(discussTitle.exists).ok();
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以重构什么？

它工作得很好，对吗？

正确，它确实有用。但是你知道吗？我们可以更上一层楼。所以我们走吧！

# 重构

我们需要做的第一件事是找到一种更好的方法来使用我们的`Selector`。不是通过使用不同的方法或语法，而是实际上从我们的`test`函数中抽象出来。

我们的测试现在知道的太多了。

[![Smartass](img/87f643d2dd701a5353e72f6a7aa18331.png)T2】](https://i.giphy.com/media/l0Iy4M0jO48jGB3R6/giphy.gif)

那么，我们如何做到这一点呢？

### 介绍...页面对象模型

这种设计模式包括根据类的功能创建代表给定页面的类。

例如

#### DevSearchPage.js

该页面应该包含进行搜索所需的`Selector`。这意味着任何可能出现的过滤器和文章都应该是它的一部分。

这使得我们的代码从存在:

```
const aboutLink = Selector('a').withText('About'); 
```

Enter fullscreen mode Exit fullscreen mode

致:

```
import DevHomePage from '../page/devhomepage.js';

const devHomePage = new DevHomePage();

// ...

await t
    .click(devHomePage.aboutLink)
    //... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们在测试中使用哪个“关于链接”是毫无疑问的，同时也使我们的`Selector`可用于其他可能需要它的测试，也称为*可重用性*。

所以，我们的最终结果应该是这样的:

```
// project/page/devhomepage.js

import { Selector } from 'testcafe';

export default class DevHomePage {

    constructor() {
        this.aboutLink = Selector('a').withText('About');
        this.discussTag = Selector('span').withText('#discuss');
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

```
// project/page/devaboutpage.js

import { Selector } from 'testcafe';

export default class DevAboutPage {

    constructor() {
        this.firstFounder = Selector('b').withText('Ben Halpern');
        this.secondFounder = Selector('b').withText('Jess Lee');
        this.thirdFounder = Selector('b').withText('Peter Frank');
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

```
//project/page/devdiscusstagpage.js

import { Selector } from 'testcafe';

export default class DevDiscussTagPage {

    constructor() {
        this.discussTitle = Selector('h1').withText('Discussion');
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像这样在测试中使用我们所有的页面:

```
//project/tests/devto.js

import DevHomePage from "../page/devhomepage";
import DevAboutPage from "../page/devaboutpage";
import DevDiscussTagPage from "../page/devdiscusstagpage";

const devHomePage = new DevHomePage();
const devAboutPage = new DevAboutPage();
const devDiscussTagPage = new DevDiscussTagPage();

fixture('DEV Tests')
    .page('http://dev.to/');

test("Check founder's names", async(t) => {
    await t
        .click(devHomePage.aboutLink)
        .expect(devAboutPage.firstFounder.exists).ok()
        .expect(devAboutPage.secondFounder.exists).ok()
        .expect(devAboutPage.thirdFounder.exists).ok();
});

test("Filter articles by discuss tag", async (t) => {
    await t
        .click(devHomePage.discussTag)
        .expect(devDiscussTagPage.discussTitle.exists).ok();
}); 
```

Enter fullscreen mode Exit fullscreen mode

但是和往常一样，千万不要相信网上随便发的帖子，自己去试试吧！

如果看到:

```
Using locally installed version of TestCafe.
 Running tests in:
 - Chrome 63.0.3239 / Mac OS X 10.13.2

 DEV Tests
 ✓ Check founder's names
 ✓ Search for posts about testing

 2 passed (6s) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我在这里的工作完成了😎

没有我照顾你，你或许可以继续你的旅程。

所以去吃官方文件吧

[![Learn all the things](img/14ef48d36afd2d49f8b67ba48d2d4be8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hW24VY8f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/synotanhnck9ym5nypn1.jpg)