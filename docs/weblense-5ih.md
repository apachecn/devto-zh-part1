# websense

> 原文：<https://dev.to/wkmn/weblense-5ih>

有一系列 URL 到图像的 API。大多数都很昂贵，过于复杂，不开源，而且你不能控制图像文件。WebLense 会处理好这件事。WebLense 是一个自托管的 Url-To-Image API。

## 如何使用 WebLense

*2018 年 1 月 30 日更新*

使用 WebLense 非常简单。

[!["Remix](img/13f34132a14421ff97aa78d14fb3bcbb.png)T2】](https://glitch.com/edit/#!/import/github/clarkhacks/WebLense)

或者

[![](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)T2】](https://heroku.com/deploy?template=https://github.com/clarkhacks/WebLense)

或者

```
git clone https://github.com/clarkhacks/WebLense.git
cd WebLense
npm install && npm start 
```

Enter fullscreen mode Exit fullscreen mode

## API

使用`Lense`端点进行 API 调用。

| `GET` | 需要 | 功能 |
| --- | --- | --- |
| 全球资源定位器(Uniform Resource Locator) | 是 | 要捕获的 URL |
| 宽度 | 不 | 末端宽度 |
| 高度 | 不 | 末端高度 |
| 耽搁 | 不 | 延迟截图 |
| json | 不 | 返回 Json |

## 例子

**网**

```
https://projectid.herokuapp.com/lense?url=https://clarkhacks.com/blog&width=1920&height=720 
```

Enter fullscreen mode Exit fullscreen mode

**jQuery**

```
$.get('https://projectid.glitch.me/lense',
{ url: 'https://clarkhacks.com' },
function(data) {
    ... 
```

Enter fullscreen mode Exit fullscreen mode

## 空间不足？

故障项目允许 128mb 的空间。WebLense 应用程序占用了大约 40mb 的空间，其余空间由拍摄的图像占用。

**选项**

*   清除`sites`文件夹
*   在别处主持 WebLense。

* * *

[通过电子邮件回复](//mailto:arpitbatra123@gmail.com?subject=Reply:%20WebLense)