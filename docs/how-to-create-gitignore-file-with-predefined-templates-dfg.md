# 如何用预定义的模板创建 gitignore 文件？

> 原文：<https://dev.to/ycmjason/how-to-create-gitignore-file-with-predefined-templates-dfg>

对于经常创建 project 的人来说，创建 gitignore 文件是一件非常烦人的事情。同样的一组文件，但是为什么要重新写。即使从其他目录复制也是浪费精力。这就是我创建[giignorer](https://www.npmjs.com/package/gitignorer)的原因。

要安装这个程序，只需运行:

```
> npm i -g gitignorer 
```

Enter fullscreen mode Exit fullscreen mode

这个程序允许你用一组预定义的文件创建 gitignore 文件。例如:

```
> gitignore init node
# created by gitignorer
# profile used: node
*.sw*
.DS_Store
node_modules
npm-debug.log* 
```

Enter fullscreen mode Exit fullscreen mode

这不是很棒吗？

所有的轮廓都在`~/.gitignore.profiles.js`定义。定义简单直观。下面是其中一个例子:

```
/*** ~/.gitignore.profiles.js ***/
var common = [
  '*.sw*',
  '.DS_Store'
];

var node = [
  'node_modules',
];

var java = [
  'url: https://raw.githubusercontent.com/github/gitignore/master/Java.gitignore',
  '.object2'
];

module.exports = {
  default: common,
  node: [...common, ...node],
  java: [...common, ...java],
  awesome: [...commone, ...node, ...java],
}; 
```

Enter fullscreen mode Exit fullscreen mode