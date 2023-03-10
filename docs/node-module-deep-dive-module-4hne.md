# 节点模块深入研究:模块

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-module-4hne>

哦，你好！

好久不见。

我从这个博客系列中抽出一点时间来享受假期，并编写一个新版本的 Zarf。但是我回来了，并准备好继续这个博客系列。

我想从查看`module`模块开始(试着快速说三遍！).NodeJS 文档的[模块页面](https://nodejs.org/api/modules.html)包含了大量关于 Node 中模块系统的信息。在深入研究`module`的代码库之前，我快速浏览了一遍。我将避免在这里总结该文档页面的内容，因为我将重点放在代码上，但如果您在阅读这篇博客文章的其余部分之前浏览一下它，可能会有所帮助。

像往常一样，我通过阅读 [`module.js`](https://github.com/nodejs/node/blob/5dbd77eb83244fbad731f660ebe5d9829a523821/lib/module.js) 文件中的最后几行开始我的代码阅读。

```
Module._initPaths();

// backwards compatibility
Module.Module = Module; 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来在底部有一个变量映射，并有一个注释说明这是为了“向后兼容”通过查看这个文件的提交历史，我对此做了进一步的研究。看起来[这个变化出现在 2011 年初](https://github.com/nodejs/node/commit/5a49f96505e48d2a1aa3a21233976f41fe1257ce)(当时 Node 还是个小婴儿)，是 CommonJS 模块系统某种重构的一部分。重构包括移动一些文件，所以重新映射实际上是在 2011 年初。我开始进入一个考古虫洞，所以我会在这里停下来，回到代码。

在文件开始时调用`_initPaths`函数。如果非要我猜的话，我会说它会负责初始化一些路径——呵呵呵。据我所知，`__initPaths`函数的前几行非常标准，在代码库的 JavaScript 部分很常见。逻辑很简单:如果我们在 Windows 上，就这样做；否则，就这样做。在这种特殊情况下，条件逻辑负责为用户主文件夹的目录设置变量。

```
Module._initPaths = function() {
  const isWindows = process.platform === 'win32';

  var homeDir;
  if (isWindows) {
    homeDir = process.env.USERPROFILE;
  } else {
    homeDir = process.env.HOME;
  } 
```

Enter fullscreen mode Exit fullscreen mode

下一段代码也利用类似的条件逻辑来确定 Node.js 在相关机器上的安装位置。

```
 // $PREFIX/lib/node, where $PREFIX is the root of the Node.js installation.
  var prefixDir;
  // process.execPath is $PREFIX/bin/node except on Windows where it is
  // $PREFIX\node.exe.
  if (isWindows) {
    prefixDir = path.resolve(process.execPath, '..');
  } else {
    prefixDir = path.resolve(process.execPath, '..', '..');
  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，该函数创建一个`paths`数组，存储 Node.js 安装和用户主目录中节点模块的潜在位置。

下一段代码获取环境变量`NODE_PATH`的值。我对这个环境变量名有点好奇，因为我以前没有遇到过它。对[模块文档](https://nodejs.org/api/modules.html#modules_loading_from_the_global_folders)的更彻底的重新阅读揭示了这是一点点“遗留”变量，来自没有关于模块如何在节点中加载的约定的时代。

旁注:是否有人对访问器是以`process.env['NODE_PATH']`形式而不是`process.env.NODE_PATH`形式编写的感到有点恼火？没有吗？只有我吗？好的。

不管怎样？接下来的代码有点意思。`nodePath`是一个用分号(或者冒号，如果你在 Windows 上)分隔的路径字符串。下一段代码在冒号或分号上分割字符串，遍历列表中的每个元素(路径),检查它们是否是真值，然后将它们添加到现有的`paths`列表中。所以现在我们的路径列表包含了 Node.js 安装中的模块集合，我们的主目录，以及在`NODE_PATH`环境变量中引用的目录。太棒了。

```
 var nodePath = process.env['NODE_PATH'];
  if (nodePath) {
    paths = nodePath.split(path.delimiter).filter(function(path) {
      return !!path;
    }).concat(paths);
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，该函数将`Module.globalPaths`的值设置为我们刚刚创建的`paths`列表的副本。

我认为下一个有趣的函数是`require`函数。

```
Module.prototype.require = function(path) {
  assert(path, 'missing path');
  assert(typeof path === 'string', 'path must be a string');
  return Module._load(path, this, /* isMain */ false);
}; 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来 require 做了一些基本的断言来确认传递的`path`的有效性，然后调用`_load`函数。`_load`函数的第一部分非常有趣。

```
if (isMain && experimentalModules) {
  (async () => {
    // loader setup
    if (!ESMLoader) {
      ESMLoader = new Loader();
      const userLoader = process.binding('config').userLoader;
      if (userLoader) {
        const hooks = await ESMLoader.import(userLoader);
        ESMLoader = new Loader();
        ESMLoader.hook(hooks);
      }
    }
    Loader.registerImportDynamicallyCallback(ESMLoader);
    await ESMLoader.import(getURLFromFilePath(request).pathname);
  })() 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来我们正在检查`isMain`变量和`experimentalModules`变量是否为真。`isMain`是指(我假设)请求是否来自主模块，`experimentaModules`是指节点是否配置为支持 [ES 模块](https://nodejs.org/api/esm.html)。看起来如果是这种情况，该函数调用 es 模块加载器。我将在另一篇博客文章中更深入地研究 ES 模块加载器，所以现在我将放弃深入研究它。相反，我浏览了其余的`_load`函数。

所以看起来`_load`做的第一件事是获取与我们试图导入的模块相关的文件名。

```
var filename = Module._resolveFilename(request, parent, isMain); 
```

Enter fullscreen mode Exit fullscreen mode

这是通过调用`_resolveFilename`来实现的，它根据下面的优先级搜索具有该模块名称的文件。

```
// require("a.<ext>")
// -> a.<ext>
//
// require("a")
// -> a
// -> a.<ext>
// -> a/index.<ext> 
```

Enter fullscreen mode Exit fullscreen mode

如果您阅读了链接到上面的模块文档，您会记得 Node 缓存了它需要的模块。这使得代码中接下来的几行易于理解。

```
var cachedModule = Module._cache[filename];
if (cachedModule) {
  updateChildren(parent, cachedModule, true);
  return cachedModule.exports;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以基本上，一旦它有了与模块相关联的文件名，它就检查该模块是否已经被加载并且在缓存中。如果是，它调用`updateChildren`函数，该函数(我假设)更新一些内部数据结构来存储缓存的模块已经被新模块需要的事实。然后，它返回由该模块导出的导出。

下一段代码检查所请求的模块是否是 NativeModule。如果是，它将 require 请求传递给本机模块加载器中的`require`函数。我将不得不在其他时间点读取代码。

旁注:现在是我把我的 Twitter 作为获取本系列新博文更新的好地方的好时机。:)

最后，如果模块还没有被请求，并且它不是一个本地模块，函数调用`Module`构造函数为我们试图加载的特定模块创建一个新的模块对象，并将其存储在缓存中。

```
var module = new Module(filename, parent);
...
Module._cache[filename] = module; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，它调用`module`上的`tryModuleLoad`函数。`tryModuleLoad`函数基本上是围绕`Module`对象中的`load`原型函数的一个小的 try-catch 包装器。`load`功能的作用是检查模块的`filename`上的文件扩展名，然后将其传递给不同的加载程序。例如，以`.js`结尾的文件名由一个加载器处理，以`.json`结尾的文件名由另一个加载器处理。快速浏览一下，看起来我们可以导入以`.js`、`.json` m `.node`和`.mjs`(新 es 模块系统的文件扩展名)结尾的文件。有道理。每个扩展加载器最终负责填充作为 require 结果返回的`module`对象中的`exports`字段。

```
return module.exports; 
```

Enter fullscreen mode Exit fullscreen mode

我将在这里停止我的代码通读，以防止 blogpost 运行太长时间，但本质上，模块加载器负责管理我们系统上模块(可能是不同类型的)的缓存和查找。如果您对上述内容有任何问题或意见，请随时在 Twitter 上联系我。