# 视觉相似性和自定义元数据如何增强您的搜索

> 原文：<https://dev.to/clarifai/how-visual-similarity-and-custom-metadata-can-enhance-your-search-bno>

数据可以简单地认为是我们想要记住以备将来使用的东西。元数据有助于描述我们试图记住的数据。因此，当我们把它放在视觉相似性的环境中时，我们是在添加元数据来描述视觉上可能无法区分的输入(数据)。这就是搜索图像的意义！

当我们在 Clarifai 说元数据时，它描述了与特定图像相关的一些属性，这些属性可能是可见的，也可能不是可见的。可以把它想象成我们可以存储所有信息的地方，这些信息是我们希望图像保留的，对我们的项目或业务有价值。这里有几个例子:

*   我们可以让用户在目录中搜索，我们希望记录他们查看的产品。我们将能够引用产品 ID 的元数据，而不必回调我们的数据库。
*   我们可以在输入中添加元数据，这样当用户出现并希望找到附近的商品时，我们可以根据邮政编码或地区进行过滤。

我们可以制作定制的元数据来满足任何需求。

让我们深入看一个例子。我们将看看一个鞋店的案例，以及我们如何使用图像和元数据进行搜索。我们将能够找到与我们想要的东西在视觉上相似的商品，并根据它们的销售情况来筛选商品。如果你想看所有已经写好的代码，你可以[看看这个 GitHub repo](https://github.com/maxcell/custom-metadata) 和它的 README。

### **要求**

如果你还没有，请务必[在 Clarifai](https://clarifai.com/developer/account/signup) 上注册一个免费账户，然后[创建一个应用](https://clarifai.com/developer/account/applications)。我们还需要确保安装[节点](https://nodejs.org/en/download)。

对于我们的小鞋店，我们有一个相当短的数据列表。这是一个 CSV，对于我们想要表示的每个数据点有几列:产品 ID、类型、颜色、品牌、价格、销售、库存、图像来源。让我们使用预构建的数据[这里的](https://raw.githubusercontent.com/maxcell/custom-metadata/master/shoe-data.csv)作为我们的例子，并将其保存到我们的项目文件夹作为`shoe-data.csv`。

### 转换 CSV 并上传至 Clarifai

[Adaltas](http://www.adaltas.com/en/home/) 的团队决定与[分享他们的代码，帮助人们解析 Node 中的电子表格](http://csv.adaltas.com/)中的数据。它允许我们的数据的灵活性，所以给他们一些赞扬。我们将在终端中安装`npm install --save csv-parse`。在这些数据可用之前，我们需要将每一行转换成一个 JSON 对象。打开一个新文件`upload.js`，我们将在这里写下我们所有的动作:

```
/* upload.js */
const parse = require('csv-parse')
const fs = require('fs')

// ~Hidden magic we will come back to~

fs.readFile(__dirname+'/shoe-data.csv', 'utf8', (err, data) => {
  if(err) { return console.log(err) }
  parse(data, { columns: true }, (err, output) => { 
    if(err) { return err; }
    shoeData = output.map((shoe) => { return convertData(shoe) })
    uploadInputs(shoeData);
  });
}) 
```

我们仍然有隐藏的魔法来解释我们的`convertData()`和`uploadInputs()`！我们必须从添加允许我们使用 Clarifai 的代码开始。要安装 Clarifai JavaScript 客户端，请进入终端并编写`npm install clarifai --save`。将带有`const Clarifai = require('clarifai')`的客户端包含在其他模块的正下方。请务必为应用程序放置我们的 API 密钥:

```
/* upload.js */
const parse = require('csv-parse')
const fs = require('fs')
const clarifai = require('clarifai')

const app = new Clarifai.App({ apiKey: 'YOUR_API_KEY' })

// ... 
```

现在我们要确保写出`convertData()`和`uploadInputs()`函数。`convertData()`将从 CSV 读取我们的结果，然后转换成元数据输入数据。`uploadInputs()`然后将数据发送给 Clarifai 进行存储。

```
/* upload.js */
const parse = require('csv-parse')
const fs = require('fs')
const Clarifai = require('clarifai')

const app = new Clarifai.App({ apiKey: 'YOUR_API_KEY' })

const uploadInputs = (inputs) => {
    app.inputs.create(inputs).then(
      // Success
      (response) => { console.log('Successful Upload!') },
      // Error
      (error) => { console.error(error) }
    )
}

const convertData = (data) => {
  return {
    url: data.imageSrc,
    metadata: data
  }
}
// ... 
```

随着我们运行`node upload.js`,获取 CSV 并上传到 Clarifai 的一切都已处理完毕。

### 在 Clarifai 上搜索

让我们编写另一个小脚本来执行我们的搜索，我们将巧妙地将其命名为`search.js`。我们将使用这个[图像](https://farm4.staticflickr.com/3370/3344620504_b547190891_o_d.jpg)，然后将销售商品的元数据应用为`TRUE`。我们的图像将过滤用户想要的项目。这就是我们如何将视觉相似性融入我们的搜索。然而，在图像中没有单独的视觉线索来找出哪些商品在销售。这是我们需要应用元数据的一个重要原因。

```
/* search.js */
const Clarifai = require('clarifai')

let app = new Clarifai.App({ apiKey: 'YOUR_API_KEY' })

// Searching by visual similarity
app.inputs.search([
    {
      input: {
        url: 'https://farm4.staticflickr.com/3370/3344620504_b547190891_o_d.jpg'
      }
    },
    {
      input: {
        metadata: {
          sale: 'TRUE'
        }
      }
    }])
.then((response) => {
  response.hits.map(
    (hit) => {
      console.log(`Price: $${hit.input.data.metadata.price}USD; URL: ${hit.input.data.image.url}`)
  })
}) 
```

我们可以在终端中用`node search.js`运行它。我们得到一个列表，上面列有我们所有的价格和与商品相关的网址。一个重要的注意事项是，我们的元数据字段对数据的搜索方式很敏感。如果我们的任何键或值中有大写字母，我们需要确保它们在搜索时与**和**完全匹配。否则，他们会认为你放进去的对象和你想找的对象是不同的东西。

### 结论

我们只是尝试了一种使用元数据的方法。记住，我们在输入中添加了一堆不同的标签。一些元数据可能比其他的更有价值，但是它完全是自由形式的。如果你对更多的搜索功能感到好奇，比如通过[地理位置](https://clarifai.com/developer/guide/searches#by-geo-location)、[公共概念](https://clarifai.com/developer/guide/searches#by-public-concepts)或其他任何东西，请在我们的[指南](https://clarifai.com/developer/guide/searches#searches)中阅读更多相关信息。如果您需要任何帮助或者您如何在[hackers@clarifai.com](mailto:hackers@clarifai.com)使用自定义元数据，请告诉我们！

帖子[视觉相似性和自定义元数据如何增强你的搜索](https://blog.clarifai.com/how-visual-similarity-and-custom-metadata-can-enhance-your-search/)最先出现在 [Clarifai 博客|人工智能在行动](https://blog.clarifai.com)上。