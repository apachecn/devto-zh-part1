# 如何在 Nuxt.js 中将字体 Awesome 5 设置为 VueComponent

> 原文：<https://dev.to/coolgoose/how-to-setup-font-awesome-5-as-vuecomponent-in-nuxtjs-54e4>

在 Nuxt.js 中有一些事情我需要理解，所以希望这也能让其他人更快地理解

1.  要在 Nuxt 中拥有一个全局组件，只需创建一个插件。即使文档在设置上不是很清楚，但是如果你看看 Github 的问题列表，它会工作得很好

2.  [Font Awesome 5 的 VueJS 集成工作良好](https://github.com/FortAwesome/vue-fontawesome)，但你需要记住安装图标类别。

3.  **更新:它在 1.1.3** ~~中得到修复，目前 [Fontawesome 5 SSR](https://github.com/FortAwesome/Font-Awesome/issues/11984) 有一个问题，听从 toadkicker 的建议，回到一个版本。~~

    # [![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)  SSR 问题与@ forawesome/font awesome 1 . 1 . 0# 11984](https://github.com/FortAwesome/Font-Awesome/issues/11984)

    [![juhaelee avatar](img/61bb408454c7d223f67728e031dcceb6.png)](https://github.com/juhaelee) **[juhaelee](https://github.com/juhaelee)** posted on [<time datetime="2017-12-22T07:10:45Z">Dec 22, 2017</time>](https://github.com/FortAwesome/Font-Awesome/issues/11984)

    我在尝试使用 fontawesome 进行 SSR 时收到以下错误:

    ```
    Cannot read property 'doScroll' of undefined
    as node_modules/@fortawesome/fontawesome/index.js:192:39 
    ```

    看起来像是在文档未定义的情况下执行该行:

    ```
    var loaded = (DOCUMENT.documentElement.doScroll ? /^loaded|^c/ : /^loaded|^i|^c/).test(DOCUMENT.readyState); 
    ```

    [View on GitHub](https://github.com/FortAwesome/Font-Awesome/issues/11984)

也就是说，让我们创建一个插件，我称之为`font-awesome.js`

```
import Vue from 'vue'
// the component
import FontAwesomeIcon from '@fortawesome/vue-fontawesome'
// the library
import fontawesome from '@fortawesome/fontawesome'
// add more icon categories as you want them, even works with pro packs
import brands from '@fortawesome/fontawesome-free-brands'

// asociate it to the library, if you need to add more you can separate them by a comma
fontawesome.library.add(brands)
Vue.component(FontAwesomeIcon.name, FontAwesomeIcon) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要在`nuxt.config.js`
中添加插件

```
module.exports = { 
  ...

  plugins: [
    ...
    { src: '~/plugins/font-awesome' }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们使用页面中的组件

```
<font-awesome-icon :icon="['fab', 'linkedin']" /> 
```

Enter fullscreen mode Exit fullscreen mode