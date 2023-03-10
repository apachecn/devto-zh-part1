# 实现 CLI 和库的融合

> 原文：<https://dev.to/mogery/making-a-cli--library-fusion-lln>

假设你想创建一个命令行界面。太好了！你用 yargs 做的，还是直接`process.argv`。什么都行。

但是现在你突然也想把它变成一个包裹。创建一个单独的包？没必要。

例如，下面是我们的 CLI 代码[1]:

```
var fs = require('fs');
var fileName = process.argv[2]; // get 1st argument (0 is node, 1 is script filename) 

var fileStats = fs.statSync(fileName); // get file stats
var fileSize = fileStats.size; // filesize in bytes

console.log(fileSize); // print fileSize 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的代码获取参数中提供的文件的文件大小。

现在，如果你想创建一个 CLI，你可以把它命名为`index.js`，把它作为主文件，放在`"bin"`中。但是如果你正在做一个 fusion[2]，你应该把它命名为`cli.js` [3]，并把它放入`"bin"`，但是不要让它成为 main。我们将创建一个新的主文件。

```
var fs = require('fs');

function getFileSize(fileName) {
    var fileStats = fs.statSync(fileName); // get file stats
    var fileSize = fileStats.size; // filesize in bytes

    return fileSize;
}

module.exports = getFileSize; 
```

Enter fullscreen mode Exit fullscreen mode

这与 CLI 代码有些类似。你仍然首先需要`fs`，但是我们创建了一个函数，带有一个`fileName`参数。你不需要从`argv`获取文件名，因为它在参数中提供了，所以我们只需获取`fileStats`和`fileSize`，然后返回它。最后，我们导出函数。

人们现在可以在我们自己的节点脚本中使用主文件。

```
var cliApi = require('yourPackageName');

var fileSize = cliApi('example.txt'); //getFileSize(fileName) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以不去管 CLI 代码，只是发布它，但是当您更新软件包时，API 和 CLI 代码之间可能会发生不一致。因此，您需要重写 CLI 代码，以使用 CLI API 代码[1]。

```
var cliApi = require('./index.js'); // require the CLI API file

var fileName = process.argv[2]; // get 1st argument (0 is node, 1 is script filename)

var fileSize = cliApi(fileName); //getFileSize(fileName)

console.log(fileSize); 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们得到 CLI API。让我们稍微讨论一下这个 require 语句。`./`开头是需要的。这意味着 node 应该不需要 node_modules 中的文件(可以在`./`之后的路径中找到),而是需要脚本(正在执行的)所在的实际文件夹。

接下来，我们需要从参数中获取文件名，然后从 CLI API 中获取文件大小，并将其打印到控制台。就是这样！

## 备注

[1]:不包括 she bang(`npm link`所需)。
【2】:非技术术语。
【3】:你叫什么都无所谓。只保留`index.js`给主文件。

此外，请确保在自述文件中说明该软件包既是软件包又是 CLI。

## 示例文件

index.js

```
var fs = require('fs');

function getFileSize(fileName) {
    var fileStats = fs.statSync(fileName); // get file stats
    var fileSize = fileStats.size; // filesize in bytes

    return fileSize;
}

module.exports = getFileSize; 
```

Enter fullscreen mode Exit fullscreen mode

cli.js

```
#!/usr/bin/env node 
var cliApi = require('./index.js'); // require the CLI API file

var fileName = process.argv[2]; // get 1st argument (0 is node, 1 is script filename)

var fileSize = cliApi(fileName); //getFileSize(fileName)

console.log(fileSize); 
```

Enter fullscreen mode Exit fullscreen mode

package.json

```
{  "name":  "getfilesize",  "version":  "1.0.0",  "description":  "A CLI and package for getting file sizes.",  "main":  "index.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "keywords":  [  "fs"  ],  "bin":  {  "filesize":  "cli.js"  },  "author":  "skiilaa",  "license":  "MIT"  } 
```

Enter fullscreen mode Exit fullscreen mode