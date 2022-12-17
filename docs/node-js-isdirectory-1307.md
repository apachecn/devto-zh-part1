# Node.js 是目录

> 原文：<https://dev.to/adamkdean/node-js-isdirectory-1307>

下面是一个有用的代码片段，用于检查 Node.js 中的路径是否是一个目录，这显然需要(' fs ')。

```
fs.lstatSync(path_string).isDirectory() 
```

您还可以:

```
fs.lstatSync(path_string).isFile()
fs.lstatSync(path_string).isDirectory()
fs.lstatSync(path_string).isBlockDevice()
fs.lstatSync(path_string).isCharacterDevice()
fs.lstatSync(path_string).isSymbolicLink()
fs.lstatSync(path_string).isFIFO()
fs.lstatSync(path_string).isSocket() 
```