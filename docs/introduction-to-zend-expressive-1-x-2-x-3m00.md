# Zend Expressive 简介(1.x/2.x)

> 原文：<https://dev.to/robopuff/introduction-to-zend-expressive-1-x-2-x-3m00>

```
This article is outdated, it is a direct copy from medium 
```

Enter fullscreen mode Exit fullscreen mode

# 什么是 Zend 表现力，如何使用

<figure>[![](img/8fcc99e4768f2b2402ae02441f49c1d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gVj-m_8d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AxntErSQmfSj1OAbOviqS0g.jpeg) 

<figcaption>图片来自[https://www.rawpixel.com/](https://www.rawpixel.com/)</figcaption>

</figure>

## TL；DR；

*Zend Expressive* 是一个易于使用的、 *PSR-15* 、 *PSR-11* 、 *PSR* -7 兼容中间件原子微框架，可以很好地作为 Zend MVC 的入口，一个较小的全功能 web 应用程序或 API 服务器。

继续观看[*James Titcumb @ PHP Uk ' 17*:**以 Zend 富有表现力和教义 ORM** 开球 T5】](https://www.youtube.com/watch?v=fhUG7JexO7U)

Zend Expressive 是一个 [PSR-7](https://github.com/php-fig/http-message) ，基于中间件的*微型*框架，它是围绕自由选择而构建的，可以很容易地介绍 *Zend 框架*或其组件的世界。它不会把你锁在它的生态系统里；相反，它让您能够使用让您感觉最舒适的库来构建应用程序。

骨架安装程序会询问您想要使用哪个*路由器/DI/模板*引擎。

## 自由选择

你必须使用一个支持的*路由器*和*模板引擎*，但是 *DI* 的实现是开放的，因为*表达式*使用了 [PSR-11](https://github.com/php-fig/container) 容器。

中间件和标准化*请求/响应*的使用也给了我们比以往更容易混合不同库的独特机会；只需简单地为它创建一个*可调用的*，将它添加到 *DI* (如果需要)就可以了！