# 创建 VueJS 货币过滤器

> 原文：<https://dev.to/thinknimble/create-a-vuejs-currency-filter-3711>

显示格式化的货币金额是 web 应用程序的一个常见需求，但是 VueJS 没有提供任何现成的过滤器。因此，如果您需要在 Vue 项目中添加货币过滤器，您可以这么做。

首先，您需要了解 VueJS 滤波器及其工作原理。在这里阅读更多:[https://vuejs.org/v2/guide/filters.html](https://vuejs.org/v2/guide/filters.html)

## 使用货币格式化程序 NPM 包

如果您使用 webpack 或类似的构建工具，并且可以访问 node.js 包，那么您可以从 npm 安装 currency-formatter 包。顺便说一下，我强烈推荐您在项目中使用 [Vue CLI](https://cli.vuejs.org/) 。

```
npm install currency-formatter --save 
```

然后，您可以在您的应用程序代码中创建一个 VueJS 过滤器，如下所示:

```
import Vue from 'vue'
import currencyFormatter from 'currency-formatter'

Vue.filter('currency', formatNumberAsUSD)

function formatNumberAsUSD (value) {
  if (!value) return ''
  value = Number(value)
  return currencyFormatter.format(value, { code: 'USD' })
} 
```

`Vue.filter`注册一个新的名为“currency”的全局 Vue 过滤器，由`formatNumberAsUSD`函数完成。formatting 函数做三件事，它(a)确保它有一个真值，如果没有，则返回一个空字符串，(b)尝试将该值转换为一个`Number`，以及(c)使用`currencyFormatter.format`将数字格式化为 USD。

## 使用 number . prototype . tolocalestring()

如果您没有使用能够访问 node.js 包的构建工具，或者您不信任外部依赖项，您可以尝试使用下面的 Number.toLocalString()方法或[查看 Github](https://github.com/smirzaei/currency-formatter/blob/master/index.js) 上的 currency-formatter 源代码并借鉴它。请注意，虽然这在现代浏览器中受支持，但在您可能需要瞄准的旧浏览器中可能不可用。

```
import Vue from 'vue'
import currencyFormatter from 'currency-formatter'

Vue.filter('currency', formatNumberAsUSD)

function formatNumberAsUSD (value) {
  if (!value) return ''
  return Number(value).toLocaleString('en', {
    style: 'currency', currency: 'USD'
  })
} 
```

除了美元，货币格式化程序包也可以处理其他货币。[查看货币格式化程序 npm 页面](https://www.npmjs.com/package/currency-formatter)了解更多详情。

## 总结

无论您选择哪种方法，您现在都已经创建了一个“货币”过滤器，可以在 VueJS 模板中使用，如下所示:

```
<template>
  <span>{{ 12 | currency }}</span>
</template> 
```

输出应该是这样的:

```
$12.00 
```

太棒了。