# 在您的 Vue 项目中集成 TypeScript

> 原文：<https://dev.to/alexjoverm/integrate-typescript-in-your-vue-project>

你有一个 [Vue](https://vuejs.org/) 项目，你听说过[打字稿](https://www.typescriptlang.org/)可以帮你做的所有事情。您决定开始使用 TypeScript。在这里，您将看到如何以一种平易近人和务实的方式做到这一点。

在本文中，我们将假设您使用的是 SFC(单个文件组件),但是即使您将它们拆分成多个文件，它也可以正常工作。因此...我们开始吧！

## 整合打字稿

我们将从 [Vue CLI Webpack 的模板](https://github.com/vuejs-templates/webpack)开始，但它也可以与令人惊叹的 [PWA 模板](https://github.com/vuejs-templates/pwa)一起工作。当你被要求时，不要选择使用 ESLint:

```
vue init webpack my-app # or: vue init pwa my-app
cd my-app 
```

Enter fullscreen mode Exit fullscreen mode

我们必须做 4 个步骤:

### 1。创建一个`tsconfig.json`文件

让我们从非常简单的东西开始，稍后我们将回到 TypeScript 配置。

```
{  "compilerOptions":  {  "lib":  ["dom",  "es5",  "es2015"],  "target":  "es5",  "module":  "es2015",  "moduleResolution":  "node",  "sourceMap":  true,  "allowSyntheticDefaultImports":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

最重要的部分是`allowSyntheticDefaultImports`的设定。由于 Vue 类型不使用 ES2015 默认导出，此设置必须设定为绕过该导出。你可以在[这个 VSCode 文档页面](https://code.visualstudio.com/docs/languages/javascript#_common-questions)中看到更多信息。

设置`"module": "es2015"`将通过产生 ESM (EcmaScript 模块)使代码[成为可树摇动的](https://alexjoverm.github.io/2017/03/06/Tree-shaking-with-Webpack-2-TypeScript-and-Babel/)。

### 2。添加`ts-loader`和 webpack 调整

用 npm 安装`typescript`和`ts-loader`:

```
npm i -D typescript ts-loader 
```

Enter fullscreen mode Exit fullscreen mode

然后打开`build/webpack.base.conf.js`，将下面的代码放在`module.rules`的开头的**处，比`vue-loader`靠前一点:** 

```
module: {
    rules: [
      {
        test: /\.ts$/,
        exclude: /node_modules|vue\/src/,
        loader: "ts-loader",
        options: {
          appendTsSuffixTo: [/\.vue$/]
        }
      },
    ... 
```

Enter fullscreen mode Exit fullscreen mode

在那里，将条目重命名为`.ts`，并将其添加到扩展名:

```
...
entry: {
  app: './src/main.ts'
},
...
resolve: {
    extensions: ['.ts', '.js', '.vue', '.json'],
... 
```

Enter fullscreen mode Exit fullscreen mode

### 3。将`es-module: true`添加到`build/vue-loader.conf.js`

这将告诉 vue-loader 使用 ES 而不是 CJS (CommonJS)模块，如 [vue-loader 文档](https://github.com/vuejs/vue-loader/blob/master/docs/en/options.md#esmodule) :
中所述

```
module.exports = {
  loaders: utils.cssLoaders({
    sourceMap: isProduction
      ? config.build.productionSourceMap
      : config.dev.cssSourceMap,
    extract: isProduction
  }),
  esModule: true
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。在文件中使用类型脚本

所以你必须做两件事:

*   在`src`文件夹内将`.js`重命名为`.ts`扩展名
*   在 Vue 文件的`script`标签上使用`lang="ts"`。例如在`App.vue`:

```
<script lang="ts">
export default {
  name: 'app'
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 故障排除

如果你的编辑对着`main.js`文件中的`import App from './App'`行大喊找不到 App 模块，你可以给你的项目添加一个`vue-shim.d.ts`文件，内容如下:

```
declare module "*.vue" {
  import Vue from 'vue'
  export default Vue
} 
```

Enter fullscreen mode Exit fullscreen mode

我用的是 VSCode 1.13.1，看不到，但是以前见过。

## TSLint，更漂亮...女神！

我在 Egghead 上录制了一个 2 分钟的视频，我在视频中解释了如何在没有任何冲突的情况下设置 TSLint。去看看！

## 好的，我可以用打字稿...那么现在下一步是什么？

在这一点上，TypeScript 已经可以通过使用内置类型和第三方类型为您指出一些您以前没有注意到的错误，并通过使用类型推断为您提供更好的开发体验，正如 Slack 的[TypeScript](https://slack.engineering/typescript-at-slack-a81307fa288d)中所解释的，这篇文章讲述了 Slack 如何将他们的代码库迁移到 TypeScript。

不过，你必须通过使用接口、类型、枚举、类和任何你需要的东西来添加你自己的类型。这样，您将添加更多类型覆盖率，TypeScript 将使用这些覆盖率来应用静态类型，从而确保类型安全。

理想情况下，你应该在你的`tsconfig.json`中使用 TypeScript 2.3 `strict`编译器选项，因为它会给你带来最大的类型安全性。[马里乌斯·舒尔茨](https://twitter.com/mariusschulz)有一篇[很好地解释了这一点的文章](https://blog.mariusschulz.com/2017/06/09/typescript-2-3-the-strict-compiler-option)。在 TypeScript 2.3 中，严格选项是一组 4 个选项，但在未来的版本中，它可以添加更多选项:

*   `strictNullChecks`
*   `noImplicitAny`
*   `noImplicitThis`
*   `alwaysStrict`

然而，如果你有一个中型/大型的代码库，使用严格选项将会使你花费大量的精力和时间来解决所有类型短缺的问题。

对于这种情况，集成 TypeScript 的一个好方法是从最灵活的配置开始，当您向代码库添加更多的类型覆盖时，开始单独启用上面提到的标志，直到您可以应用`strict`选项，这样您就可以用一种实用的方式来处理它。

## 结论

TypeScript 从一开始就为您的代码库带来了类型安全，但是您定义的类型越多，就越容易检测到 bug 和错误，并使代码可维护。版本 2 的 Vue 附带了类型，所以我们可以使用它们。当你在 Vue 中使用 OOP(面向对象编程)时，它们会变得更强大，但会在另一篇文章中看到。

喜欢就去分享吧！你可以在这个博客或 twitter 上关注我，账号是 [@alexjoverm](https://twitter.com/alexjoverm) 。有什么问题吗？开枪！

* * *

*最初发布于[alexjoverm . github . io](https://alexjoverm.github.io/2017/06/28/Integrate-TypeScript-in-your-Vue-project?utm_source=devto)2017 年 6 月 28 日*