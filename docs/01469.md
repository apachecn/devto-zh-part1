# Fs-tivus 为我们其余的人

> 原文：<https://dev.to/warrend/fs-tivus-for-the-rest-of-us-551m>

还有什么比看看 Node 的文件系统(fs)模块更好的方式来迎接假期呢！

从高层次来说，Node fs 允许我们在后端使用 JavaScript 从我们的计算机中读取、写入、创建、更新和删除文件，类似于您使用 Ruby 或 Python 的方式。

为了使用文件系统，我导入了模块:

```
const fs = require(‘fs’) 
```

Enter fullscreen mode Exit fullscreen mode

对于这个例子，假设我们有一个名为 data.json 的 JSON 文件，其中有一个圣诞电影列表。我们希望在文件中添加一个新电影，并将其保存到一个名为 updated.json 的新文件中，该文件尚不存在。

我们的 data.json 文件看起来像这样:

```
{
  "movies": [
    {
      "name": "Christmas Vacation",
      "director": "Jeremiah Chechik",
      "year": "1989"
    },
    {
      "name": "Die Hard",
      "director": "John McTiernan",
      "year": "1988"
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想要添加到 JSON 的对象是这样的:

```
let christmasStory = {
  "title": "A Christmas Story", 
  "director": "Bob Clark",
  "year": "1983"
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我读取 data.json 文件的内容，并将其保存到一个变量:

```
fs.readFile('data.json', 'utf8', (err, data) => {
  if (err) throw err
  let obj = JSON.parse(data)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我使用 fs 来访问文件系统模块。readFile()的第一个参数是我们要读取的文件，后面是我们添加的“utf8 ”,否则我们会得到一堆没有转换成字符的奇怪数字。由于 readFile()是异步的，所以我添加了一个回调函数，一旦它完成了数据获取，就会触发这个函数。我添加了一些最小的错误检查，并接入了保存 JSON 文件数据的数据。最后，由于从文件中读取的数据是一个字符串，所以我使用 JSON.parse()将其转换为 JSON。

接下来，我将新电影《圣诞故事》放入 JSON 中的 movies 数组。

```
fs.readFile('data.json', 'utf8', (err, data) => {
  if (err) throw err
  let obj = JSON.parse(data)
  obj.movies.push(christmasStory)
}) 
```

Enter fullscreen mode Exit fullscreen mode

当把 JSON 写到一个文件的时候，我们必须把它变成一个大的字符串，就像它来到我们这里一样，否则文件不知道如何处理数据。为此，我使用 JSON.stringify()并将其保存到一个名为 updated:
的变量中

```
fs.readFile('data.json', 'utf8', (err, data) => {
  if (err) throw err
  let obj = JSON.parse(data)
  obj.movies.push(christmasStory)
  let updated = JSON.stringify(obj, null, 2)
}) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我使用了 fs.writeFile()，它接收要写入的文件，然后接收要写入的数据。如果文件不存在，WriteFile()将创建该文件。

```
fs.readFile('data.json', 'utf8', (err, data) => {
  if (err) throw err
  let obj = JSON.parse(data)
  obj.movies.push(christmasStory)
  let updated = JSON.stringify(obj, null, 2)
  fs.writeFile('updated.json', updated)
}) 
```

Enter fullscreen mode Exit fullscreen mode

完成的代码如下所示。要运行代码，请转到文件夹的根目录并运行 node yourFileName。一旦运行，您将看到新的 updated.json 文件出现在项目的根目录:

```
const fs = require('fs')

let christmasStory = {
  "title": "A Christmas Story", 
  "director": "Bob Clark",
  "year": "1983"
}

fs.readFile('data.json', 'utf8', (err, data) => {
  if (err) throw err;
  let obj = JSON.parse(data)
  obj.movies.push(christmasStory)
  let updated = JSON.stringify(obj, null, 2)
  fs.writeFile('updated.json', updated)
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们只是触及了 fs 的表面，但是正如你所看到的，这个模块为我们在应用程序中创建和添加数据文件提供了无限的可能性。