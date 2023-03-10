# 编写自己的 npm 模块

> 原文：<https://dev.to/dhruv/writing-your-own-npm-module>

### 什么是 npm？

npm 代表节点程序包管理器。npm 使得开发者共享代码变得容易。像所有其他包一样，npm 包也有一个声明文件，名为 **package.json** 。

### 安装 node.js 和 npm

下载 node.js 的最佳方式是使用 node.js [网站](https://nodejs.org/en/download/)上的节点安装程序。有 nvm(节点版本管理器)在节点的不同版本之间切换也是很好的。Node.js 附带了 npm，但是最好更新您的 npm。运行以下命令来更新您的 npm。

##### *npm 安装 npm@latest -g*

### 创建 package.json 文件

package.json 有

1.  您的包所依赖的依赖项列表。
2.  指定您正在使用的软件包的版本
3.  使重用您的包来运行 package.json 文件变得容易

##### *npm 初始化*

它会询问一些细节，如

**名称**:您的包的名称
**版本**:您的包的版本
**描述**:您的包做什么
**main** :包含您的主代码的文件(理想情况下应该是 index.js)
**脚本**:您想在之后运行的任何脚本，如 tests 或 bower
**作者**:该包属于谁
**许可证**最后，您的 package.json 会看起来像是

```
{
  "name": "my_package",
  "description": "",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "https://github.com/username/my_package.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/username/my_package/issues"
  },
  "homepage": "https://github.com/username/my_package"
  "dependencies": {
    "my_dep": "^1.0.0"
  }
} 
```

### 编写您的包

你在 package.json 中提到的 ***index.js*** 中编写你的脚本，如前所述你可以在你的包中使用已经发布的包。

```
var request = require('request');
exports.printMessage = function(){
  console.log("This is my npm package. Wohoo !");
} 
```

这里我使用[请求](https://www.npmjs.com/package/request)模块进行 HTTP 调用。
为了让你的函数被使用，你需要导出它们。这里我导出了我的 printMessage 函数。
当人们使用你的软件包时，他们会 **npm 安装我的新软件包**

```
var myPackage = require('myNewPackage');
myPackage.printMessage() // This is my npm package. Wohoo ! 
```

### 发布您的 npm 包

若要发布您的包，您必须拥有 npm 注册表帐户。要么在网站上创建一个帐户，要么运行

##### *npm adduser*

如果您已经有一个帐户运行

##### *npm 登录*

要检查您是否已登录，请运行

##### *npm whoami*

它应该会提示您的用户名。当你完成所有这些后，点击

##### *npm 发布*

然后你的 npm 包就出版了。

### 更新您的 npm 软件包

当你更新完你的包后，只需更改 package.json 中的版本并点击 npm publish，你的 npm 包就会被更新。

*最初发表于[媒介](https://medium.com/@nendhruv/writing-your-own-npm-module-8024d75ae159)T3。*