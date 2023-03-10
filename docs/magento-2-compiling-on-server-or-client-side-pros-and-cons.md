# Magento 2 在服务器端还是客户端编译？(赞成和反对)

> 原文：<https://dev.to/rafaelcg/magento-2-compiling-on-server-or-client-side-pros-and-cons>

服务器端和客户端哪种方式更好？我有这个问题很多次了，当其他开发者问我的时候，我都忘记了。所以我决定在下面这个简单的列表中列出所有内容，并添加本地编译。
声明，我曾经在管理面板上使用服务器端编译，在我的本地环境中使用本地编译。；)

你可以在**商店>配置>高级>开发者>前端开发工作流程**上找到这个配置。

## 服务器端编译

服务器端编译是**默认的**选择，用于安装后的首次编译和生产模式。

#### 缺点:

*   慢的
*   全局重新编译
*   每次运行前需要删除静态文件

#### 优点:

*   不需要额外设置
*   在服务器端工作

* * *

## 客户端编译

使用 Javascript 的客户端编译是可选的，用于快速预览变化和调试小问题。

#### 缺点:

*   样式编译前恼人的闪烁
*   全局重新编译

#### 优点:

*   不需要额外的设置，可以在管理区切换
*   快的

* * *

## 本地编译

使用 Node.js 和 Grunt task runner 进行本地编译是一种**高级模式**，推荐用于深度定制。

#### 缺点:

*   需要本地环境的初始设置

#### 优点:

*   快的
*   仅重新编译本地更改
*   易于调试
*   可以自动观看和实时重新加载

我希望这个解释对你的项目有所帮助，我想知道你更喜欢哪个，为什么？评论告诉我。