# 将故事书与 Vuejs 一起使用

> 原文：<https://dev.to/reoring/use-storybook-with-vuejs-b2o>

[![Screen Shot 2017-08-21 at 17.08.26.png](img/5645267d41a2f37ab3896041afbedeaa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SxiFGxji--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/7565/3baf1c3e-520a-9c3c-0d14-b4b46518b7f1.png)

[故事书](https://storybook.js.org/)从 3.2 开始，增加了对 Vuejs 的支持，我马上试用一下。

Storybook 是一个工具，它可以轻松地创建组件目录，对项目中的自制组件以及如何使用它进行编目。

[![Screen Shot 2017-08-21 at 16.54.49.png](img/8898b6f9de4ce93e3321912cdb22d870.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6y8UJRGR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.amazonaws.com/0/7565/899f8ef8-1dd4-5e4c-8dfb-56e431a37b6c.png)

## 故事书/ cli 的安装

```
npm i - g @ storybook / cli 
```

Enter fullscreen mode Exit fullscreen mode

由现有 Vuejs 项目准备

```
cd Directory where vuejs project is located 
```

Enter fullscreen mode Exit fullscreen mode

安装故事书

```
getstorybook 
```

Enter fullscreen mode Exit fullscreen mode

## 启动故事书服务器

```
yarn run storybook 
```

Enter fullscreen mode Exit fullscreen mode

在这种状态下，打开 [http://localhost:6006/](http://localhost:6006/) 打开默认设置画面。

## 添加组件

要添加一个组件到故事书，添加一个定义到用`getstorybook`创建的`stories`目录中的`index.js`。

您可以通过编辑`.storybook / config.js`来更改此`stories`目录。

## 参考材料

*   介绍:Vue 的故事书🎉–故事书–中型
*   [发布故事书 3.2-故事书-媒介](https://medium.com/storybookjs/announcing-storybook-3-2-e00918a1764c)
*   [story book/migration . MD at master story books/story book GitHub](https://github.com/storybooks/storybook/blob/master/MIGRATION.md#from-version-31x-to-32x)