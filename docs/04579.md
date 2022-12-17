# Django + webpack + Vue.js:建立一个易于开发和部署的新项目(第 1 部分)

> 原文：<https://dev.to/ariera/django--webpack--vuejs-setting-up-a-new-project-thats-easy-to-develop-and-deploy-part-1-4o2>

# T2】

**更新:我最近在[ariera/django-vue-template](https://github.com/ariera/django-vue-template)创建了一个工作项目，使用了 vue-webpack 模板的最新版本，并将这篇文章中强调的所有更改捆绑在一起提交 [8a5c552](https://github.com/ariera/django-vue-template/commit/8a5c552a20bb9ec7e5751adef3b5d2e26addbcf4)** 。您可能也有兴趣查看 CharlesAracil 从这个条目中获得灵感而创建的一个 [vue 项目模板。](https://github.com/CharlesAracil/webpack-vue-django)

我最近开始了一个基于 Django 和 Vuejs 的新 web 项目。它花了大量阅读半相关和/或过时的帖子，大量的文档，以及大量的试验和错误来得到它的权利。

在这篇文章中，我们将讨论关于**建立一个好的、可靠的开发环境的关键点，这个开发环境与生产一致，因此易于部署**。如何部署、最终配置和(自动化)的细节将在以后的文章中讨论。

这是我找到的解决方案，可能有更好的替代方案(我很乐意阅读),它最终是为了帮助他人和未来的自己而写的:)

### 在 [ariera.github.io](https://ariera.github.io/2017/09/26/django-webpack-vue-js-setting-up-a-new-project-that-s-easy-to-develop-and-deploy-part-1.html) 继续阅读