# Express 和 MongoDB 的基本路由、HTTP 请求和 CRUD 操作

> 原文：<https://dev.to/ichtrojan/basic-routing-http-requests-and-crud-operation-with-express-and-mongodb-od2>

首次发布于 [JavaScript 一月](https://www.javascriptjanuary.com/blog/basic-routing-http-requests-and-crud-operations-with-express-and-mongodb)

你好，

再说路线。路线只是您的应用程序的入口点。所以我们将构建路由来用 ExpressJS 执行一些 HTTP 请求。

## 入门

首先，我们需要做一些设置:

*   创建一个新文件夹，并随意命名:`mkdir express-routes`
*   将您的终端目录更改到该文件夹:`cd express routes`
*   运行`npm init`(所有提示都可以按回车键)。
*   安装快捷:`npm install express --save`
*   在该文件夹中创建一个新文件:`touch index.js`
*   现在在你最喜欢的文本编辑器中打开`index.js`，在我的例子中是 [Atom](http://atom.io)

然后我们需要将 Express 导入到我们的应用程序中:

```
const express = require('express');
const app = express(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们必须创建一个服务器，让浏览器和 API 调用可以连接。我们可以用 Express' `listen`方法:
来实现

```
app.listen(3000, function() {
  console.log('listening on 3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过运行`node index.js`来测试您的服务器。您应该看到这个:

[![test](img/939586d21592117406b4835296d4a7d8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8B2OaJpi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515319847/Screen_Shot_2018-01-07_at_11.07.56_AM_iopepq.png)

## 添加我们的第一条航线

现在，让我们创建一条可以在浏览器中访问的路线。浏览器只为使用`GET`请求的页面提供服务，所以我们需要使用 Express' `get`方法创建一个路由，如下所示:

```
app.get('/', function(req, res) {
  res.send("Yep it's working");
}); 
```

Enter fullscreen mode Exit fullscreen mode

您需要重新启动节点服务器才能看到此更新。您可以通过键入`CTRL + C`来停止您的服务器，然后运行`node index.js`来实现这一点。如果你在网络浏览器上访问 [http://localhost:3000](http://localhost:3000) ，你应该会看到这个:

[![test1](img/25c35920a5acaedcec9459ad1430cbdc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NTz-o9RM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515320585/Screen_Shot_2018-01-07_at_11.22.23_AM_d4i0nl.png)

我们再加一条路线:

```
app.get('/love', (req, res) => {
  res.send('Hi Love');
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果你访问[http://localhost:3000/love](http://localhost:3000/love)，你应该会看到这个:

[![test2](img/33f182746e0abf7414d7755fc62c81c9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QsHKv_FY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515321130/Screen_Shot_2018-01-07_at_11.29.33_AM_g3ab4j.png)

你可能已经注意到我现在正在使用一个箭头功能，这是 ES6 的一个特性。你可以在这里了解更多关于 ES6 规范[的信息。](//es6-features.org)

现在我们已经学习了如何在 Express 中创建一个基本的路由，让我们进一步学习使用 MongoDB 的 CRUD 操作。但在我们前进之前，你不厌倦每次做出改变都要重启服务器吗？您可以通过使用一个名为 Nodemon 的包来解决这个问题。

## 添加 Nodemon

可以通过运行`npm install nodemon --save-dev`来安装 Nodemon。然后这样编辑你的`package.json`:

[![test3](img/2af1d04b24a0810703487b8c64e3f6d4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HEel8iAx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515323965/Screen_Shot_2018-01-07_at_12.18.17_PM_xm8ljq.png)

现在在您的终端中运行`npm run dev`，您应该会看到:

[![test4](img/cc92d0f9f337bd09b6d53995254888fb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KkLG8jkR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515324090/Screen_Shot_2018-01-07_at_12.21.02_PM_lej62p.png)

现在有了 Nodemon 的设置，无论何时做任何更改，都不必重新启动服务器。但是，您需要刷新浏览器才能看到这些更改。

## 积垢操作

[CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (创建、读取、更新和删除)操作是四个基本的数据库功能。对于本教程，我们将使用一个名为 mLab 的远程 MongoDB 服务。我们将构建一个简单的应用程序来存储和检索数据库中的`first_name`和`last_name`值。

## 设置 MongoDB 和 mLab

要创建一个新帐户并建立一个数据库，您需要到[这里](https://mlab.com/)。我提供了下面的图片作为建立数据库的参考指南。

[![step1](img/3b2e5350a13f31bf2534b5e901632229.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XaHOnUNW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325863/Screen_Shot_2018-01-07_at_12.36.07_PM_jncg0f.png)
T0】

[![step2](img/e04a23c13defaa6759975dd7739e12f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FXDN05O---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325864/Screen_Shot_2018-01-07_at_12.36.26_PM_sfuy6f.png)
T0】

[![step3](img/6176e45717af7b560f76e7d11d74cb28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WsmWO6jW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325863/Screen_Shot_2018-01-07_at_12.36.45_PM_jxew89.png)
T0】

[![step4](img/c6a4d86071e11f0b4d154d5e5afe8710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p8pPgE2H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325863/Screen_Shot_2018-01-07_at_12.36.56_PM_aqrabn.png)
T0】

[![step5](img/2da91a28d9b44b5fec1266d1e66b5384.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HTECVzus--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325862/Screen_Shot_2018-01-07_at_12.37.18_PM_r9f5nj.png)
T0】

[![step6](img/91672ea3a8cf3c9534d4d33384ae61d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C5jy3ExM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325864/Screen_Shot_2018-01-07_at_12.38.31_PM_fjetbt.png)
T0】

[![step7](img/a5e752a4ffeef572a467f3158c66311e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T5gGToRK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515325864/Screen_Shot_2018-01-07_at_12.39.24_PM_zf8h0p.png)
T0】

mLab 应该给你你的证书，可以在顶部找到

[![credentails](img/5d0ed3dd46ab75ed34611a3cff812556.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GIXiJKLO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515524950/Screen_Shot_2018-01-09_at_8.05.08_PM_xkdvpa.png)

用您刚刚创建的用户的凭证替换`<dbuser>`和`<dbpassword>`

设置好 remmote 数据库后，我们可以通过运行:
在应用程序中安装 MongoDB

```
npm install mongodb --save 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将 MongoDB 导入到我们的应用程序中:

```
const MongoClient = require('mongodb').MongoClient
var ObjectID = require('mongodb').ObjectID; // we will use this later

MongoClient.connect('link-to-mongodb', (err, database) => {
  // ... start the server
}) 
```

Enter fullscreen mode Exit fullscreen mode

记住用 mLab 给你的 URL 替换`link-to-mongodb`，在我的例子中:

`mongodb://<dbuser>:<dbpassword>@ds247027.mlab.com:47027/crud`

还要确保用您创建的凭证替换`<dbuser>`和`<dbpassword>`。

现在将`listen`方法包装在`MongoClient`回调中，如下所示:

```
MongoClient.connect('mongodb://trojan:00000000@ds247027.mlab.com:47027/crud', (err, db) => {
  var dbase = db.db("crud");
  if (err) return console.log(err)
  app.listen(3000, () => {
    console.log('app working on 3000')
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

记住这些

```
HTTP Verb     Operation
GET           Read
POST          Create
PUT           Update
DELETE        Delete 
```

Enter fullscreen mode Exit fullscreen mode

前面我们了解到浏览器通过`GET`请求获取页面，但是要向数据库添加数据，我们需要使用`POST`请求。我们可以使用名为[邮递员](https://www.getpostman.com/)的服务来发送这些请求。

为了让我们的应用程序接收`post`请求，我们需要一个名为`body-parser`的包，我们可以通过运行`npm install body-parser --save`来安装它。然后我们可以像这样把它导入到我们的应用程序中:

```
const express = require('express');
const MongoClient = require('mongodb').MongoClient
var ObjectID = require('mongodb').ObjectID;
const bodyParser= require('body-parser')
const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true})); 
```

Enter fullscreen mode Exit fullscreen mode

### 创建条目

我们将使用 POST HTTP 动词在数据库中创建一个新条目。让我们创建我们的发布路线:

```
app.post('/name/add', (req, res, next) => {

    var name = {
      first_name: req.body.first_name,
      last_name: req.body.last_name
    };

    dbase.collection("name").save(name, (err, result) => {
      if(err) {
        console.log(err);
      }

      res.send('name added successfully');
    });
  }); 
```

Enter fullscreen mode Exit fullscreen mode

要测试此应用程序:

*   开放邮递员
*   勾选`x-www-form-urlencoded`
*   向[http://localhost:3000/name/add](http://localhost:3000/name/add)发出 post 请求，关键字为`first_name`和`last_name`，如下所示:

[![postman](img/9e3c6cb6971f1c65109beca860d6e5db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CH_0QVWq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515345219/Screen_Shot_2018-01-07_at_6.02.45_PM_p5vdyf.png)

如果你看到了`name added successfully`，你可以查看你的 [mLab 仪表盘](http://mlab.com)，找到一个名为`name`的新收藏。

[![mlab](img/5e669e50021921f28fe90027dafc4985.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XcS-lTnD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515345515/Screen_Shot_2018-01-07_at_6.16.27_PM_kb9ose.png)

如果您点击`name`，您将看到您在`post`请求中发送的数据:

[![mlabs1](img/934845c308168b6fedef6bc7857ae9e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CI628aZe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515346000/Screen_Shot_2018-01-07_at_6.19.24_PM_lqmch9.png)

### 读取所有条目

现在让我们添加一个函数来处理对 Express 数据库的 GET 请求。

```
app.get('/name', (req, res) => {
    dbase.collection('name').find().toArray( (err, results) => {
      res.send(results)
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过向[http://localhost:300/name](http://localhost:300/name)发送 GET 请求来测试 Postman，如下所示:

[![postman2](img/7b1c320ca8ab86fecf5eddafa5f38df6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IoppsJFg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515348132/Screen_Shot_2018-01-07_at_7.01.15_PM_oaqwfr.png)

### 按 ID 读取

我们还可以通过自动分配给数据库中条目的`id`发出 GET 请求。我们可以通过 URL 将那个`id`添加到我们的请求中。

[![details](img/92ee27213c7c8fc7916779b20027b9fd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nqz-LNcS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515351170/Screen_Shot_2018-01-07_at_7.52.21_PM_jqapvi.png)

例如，上图中的数据 id 为`5a5252b2d60a9f4a4254bade`。所以当我们像这样在 URL 中传递它的 id 时:

[![url](img/d976579f1ed4ae5c416d3b7e6b9e71d1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XRngixQT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515351306/Screen_Shot_2018-01-07_at_7.54.19_PM_scvhnm.png)

它将返回与该 id 相关联的条目。我们可以像这样为这种类型的请求添加一个快速处理程序:

```
app.get('/name/:id', (req, res, next) => {
    if(err) {
      throw err;
    }

    let id = ObjectID(req.params.id);
    dbase.collection('name').find(id).toArray( (err, result) => {
      if(err) {
        throw err;
      }

      res.send(result);
    });
 }); 
```

Enter fullscreen mode Exit fullscreen mode

### 按 ID 更新

我们学习了如何使用 POST 请求添加数据，以及如何使用 GET 请求检索数据。现在我们将添加一个处理程序，使用一个 PUT 请求来更新现有的 id 记录:

```
app.put('/name/update/:id', (req, res, next) => {
    let id = {
      _id: ObjectID(req.params.id)
    };

    dbase.collection("name").update({_id: id}, {$set:{'first_name': req.body.first_name, 'last_name': req.body.last_name}}, (err, result) => {
      if(err) {
        throw err;
      }
      res.send('user updated sucessfully');
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用 Postman 来测试这一点。我添加了更多数据，如下图所示:

[![getall](img/8bc1d339b1052f70ba27be0c8c205347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mUnxfeP3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515372776/Screen_Shot_2018-01-08_at_1.51.51_AM_c7xf7h.png)T3】

```
[  {  "_id":  "5a52a2a314622d0c85d8da3f",  "first_name":  "Thomas",  "last_name":  "Okoh"  },  {  "_id":  "5a52b1dc8635184cf9f7fd32",  "first_name":  "Kun",  "last_name":  "Aguero"  },  {  "_id":  "5a52b1e48635184cf9f7fd33",  "first_name":  "Ed",  "last_name":  "Sheeran"  },  {  "_id":  "5a52b1ee8635184cf9f7fd34",  "first_name":  "Christiano",  "last_name":  "Ronaldo"  },  {  "_id":  "5a52b2008635184cf9f7fd35",  "first_name":  "Adekunle",  "last_name":  "Gold"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着编辑这个条目:

```
{  "_id":  "5a52b1ee8635184cf9f7fd34",  "first_name":  "Christiano",  "last_name":  "Ronaldo"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将在 Postman 中使用一个 PUT 方法，使用`id`:T7 将`last_name`从`Ronaldo`更新到`Messi`，将`first_name`从`Christiano`更新到`Lionel`。

[![PM](img/94cc6845665a951722cd2842241e89f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ihz22LwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515375064/Screen_Shot_2018-01-08_at_2.29.06_AM_a5cgdw.png)

当我们检查数据库中的条目时，我们看到它们已经被更新。

```
[  {  "_id":  "5a52a2a314622d0c85d8da3f",  "first_name":  "Thomas",  "last_name":  "Okoh"  },  {  "_id":  "5a52b1dc8635184cf9f7fd32",  "first_name":  "Kun",  "last_name":  "Aguero"  },  {  "_id":  "5a52b1e48635184cf9f7fd33",  "first_name":  "Ed",  "last_name":  "Sheeran"  },  {  "_id":  "5a52b1ee8635184cf9f7fd34",  "first_name":  "Messi",  "last_name":  "Lionel"  },  {  "_id":  "5a52b2008635184cf9f7fd35",  "first_name":  "Adekunle",  "last_name":  "Gold"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 按 ID 删除

最后，我们到达了这个应用程序的最后一个阶段:通过`id`删除条目。我们将通过使用删除请求来做到这一点。让我们在应用程序中创建一个处理函数。

```
app.delete('/name/delete/:id', (req, res, next) => {
    let id = ObjectID(req.params.id);

    dbase.collection('name').deleteOne(id, (err, result) => {
      if(err) {
        throw err;
      }

      res.send('user deleted');
    });
 }); 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下当前的条目:

```
 [  {  "_id":  "5a52a2a314622d0c85d8da3f",  "first_name":  "Thomas",  "last_name":  "Okoh"  },  {  "_id":  "5a52b1dc8635184cf9f7fd32",  "first_name":  "Kun",  "last_name":  "Aguero"  },  {  "_id":  "5a52b1e48635184cf9f7fd33",  "first_name":  "Ed",  "last_name":  "Sheeran"  },  {  "_id":  "5a52b1ee8635184cf9f7fd34",  "first_name":  "Messi",  "last_name":  "Lionel"  },  {  "_id":  "5a52b2008635184cf9f7fd35",  "first_name":  "Adekunle",  "last_name":  "Gold"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们将删除这个:

```
{  "_id":  "5a52b2008635184cf9f7fd35",  "first_name":  "Adekunle",  "last_name":  "Gold"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将通过邮递员发出删除请求:

[![firepost](img/549279f8b38a4cc92bd1911f64d5d138.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--byGNJ42l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ichtrojan/image/upload/v1515375600/Screen_Shot_2018-01-08_at_2.39.34_AM_fglrgu.png)

成功！！！

```
[  {  "_id":  "5a52a2a314622d0c85d8da3f",  "first_name":  "Thomas",  "last_name":  "Okoh"  },  {  "_id":  "5a52b1dc8635184cf9f7fd32",  "first_name":  "Kun",  "last_name":  "Aguero"  },  {  "_id":  "5a52b1e48635184cf9f7fd33",  "first_name":  "Ed",  "last_name":  "Sheeran"  },  {  "_id":  "5a52b1ee8635184cf9f7fd34",  "first_name":  "Messi",  "last_name":  "Lionel"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

我们的条目已被删除。

## 结论

此时，您的`index.js`应该是这样的:

```
const express = require('express');
const MongoClient = require('mongodb').MongoClient
const bodyParser= require('body-parser')
var ObjectID = require('mongodb').ObjectID;
const app = express();

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

MongoClient.connect('mongodb://trojan:00000000@ds247027.mlab.com:47027/crud', (err, db) => {
  if (err) return console.log(err)

  app.listen(3000, () => {
    console.log('app working on 3000')
  });

  let dbase = db.db("crud");

  app.post('/name/add', (req, res, next) => {

    let name = {
      first_name: req.body.first_name,
      last_name: req.body.last_name
    };

    dbase.collection("name").save(name, (err, result) => {
      if(err) {
        console.log(err);
      }

      res.send('name added successfully');
    });

  });

  app.get('/name', (req, res, next) => {
    dbase.collection('name').find().toArray( (err, results) => {
      res.send(results)
    });
  });

  app.get('/name/:id', (req, res, next) => {
    if(err) {
      throw err;
    }

    let id = ObjectID(req.params.id);
    dbase.collection('name').find(id).toArray( (err, result) => {
      if(err) {
        throw err;
      }

      res.send(result);
    });
  });

  app.put('/name/update/:id', (req, res, next) => {
    var id = {
      _id: new ObjectID(req.params.id)
    };

    dbase.collection("name").update(id, {$set:{first_name: req.body.first_name, last_name: req.body.last_name}}, (err, result) => {
      if(err) {
        throw err;
      }

      res.send('user updated sucessfully');
    });
  });

  app.delete('/name/delete/:id', (req, res, next) => {
    let id = ObjectID(req.params.id);

    dbase.collection('name').deleteOne({_id: id}, (err, result) => {
      if(err) {
        throw err;
      }

      res.send('user deleted');
    });
  });

}); 
```

Enter fullscreen mode Exit fullscreen mode

完成了。如果你跟随这篇文章到了这一步，你应该为自己感到非常自豪。向前迈进，为下一个十亿用户构建。

你可以随时在推特上联系我

再见