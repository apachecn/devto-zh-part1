# 本地包的 Node.js 编码

> 原文：<https://dev.to/lirantal/nodejs-yarning-for-localpackages>

这并不是对使用 Yarn 的 npm 包管理的又一个赞扬，而是一个使用本地开发的包的简明方法。

当您在本地开发机器上初始化 Npm 模块时，它们就开始了它们的生命，但是当您想要测试它们或者简单地将它们与您拥有的其他 Node.js 项目一起使用时，就会出现这种情况。

使用 npm 客户端，我们将在文件系统中创建一个链接，但是使用 Yarn，您可以真正管理它们的版本，并像使用任何其他依赖关系一样使用它们，所有规则都应用于依赖关系。

## 文件系统上的模块:yarn add file://

使用以下命令从文件系统添加软件包:

```
yarn add file:/Users/lirantal/code/my-npm-module 
```

这样做时，请记住:

*   因为 Yarn 将此视为真正的依赖项，这意味着它会将它真正安装到您的本地 node_modules/目录中，所以如果您对 npm 模块进行了更改，它们将不会反映在 Node.js 项目中已安装的版本上。
*   Yarn 喜欢缓存，它也缓存那些本地 npm 模块，您可以通过执行以下操作强制重新安装:

```
// Remove the package and clean local cache
yarn remove my-npm-mdule
yarn cache clean
// Re-install it
yarn add file:/... 
```

## Github / Git 上的  模块

Yarn 带来的另一个便利是，您可以将 npm 模块推送到 Git 存储库，并告诉 Yarn 使用它。如果您不想将 npm 包提交给 npm 存储库，这很有用。

这不是纱线所特有的。npm 客户端也可以这样做。如果你在 GitHub 上有一个包，你可以告诉 Yarn 使用:

```
yarn add git+ssh://git@github.com/lirantal/my-npm-module.git 
```

这里应用了与文件系统安装相同的缓存和版本控制。