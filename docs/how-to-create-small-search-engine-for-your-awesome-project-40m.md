# 如何为你的牛逼项目创建小型搜索引擎？

> 原文：<https://dev.to/gaserd/how-to-create-small-search-engine-for-your-awesome-project-40m>

## 如何为自己牛逼的项目打造小型搜索引擎？

你怎么看待 Google 或者 DuckDuckGo？是的，我也爱它，就像数百万人一样。

但是，如果您创建新项目，您将需要强大和非常智能的搜索引擎？不，你将需要非常小和快速的搜索引擎！

好吧，让我们谷歌一下，我们会得到什么？最后你会看到 ElasticSearch，这是一个很棒的全文搜索引擎。

假设您在 Nodejs + PostgreSQL 上创建项目，并且您的数据库有 10，000 个条目。弹性搜索来应对这项任务。

但是，你需要复制你的数据库，因为 ElasticSearch 应该生成它的“搜索图”,而我们有 2 个“数据库”,我不喜欢它，因为我们需要在我的硬盘上有更多的空闲空间。

PostgreSQL 能帮助我们创建自定义搜索引擎吗？是的，他可以。

好了，帮你理解一下，我有编程语言 JavaScript，因为我喜欢他，但是 2017 年 PostgreSQL 主数据库。很多人在大大小小的项目中使用 NodeJS 和 PostgreSQL。

## 也许开始编程？

是的，开始并为我们的项目创建新文件夹。

命名: **sm-fs-search-engine** (小型快速搜索引擎)。

我们需要初始化 npm 并安装这个包。

```
{
  "name": "sm-fs-search-engine",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT",
  "dependencies": {
    "body-parser": "^1.18.2",
    "express": "^4.16.2",
    "pg": "^7.4.0"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们创建文件`server.js`并编写这个:

```
const express       = require('express');
const app       = express();
const bodyParser    = require('body-parser');

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

const port = process.env.PORT || 3000;

app.get('/search', function(req, res) {
    console.log('SERVER UP!');
})

app.listen(port);
console.log(new Date() + ' => start server :' + port); 
```

Enter fullscreen mode Exit fullscreen mode

而且去查这个，`node server.js`，hmmm 干得非常漂亮！

好的，接下来我们需要在 PostgreSQL 中设置设置，打开`pgsql`并创建数据库。

```
CREATE DATABASE universities; 
```

Enter fullscreen mode Exit fullscreen mode

很好，我们有数据库了。接下来，创建表格`university`。

```
CREATE TABLE university (id SERIAL PRIMARY KEY, name VARCHAR, description VARCHAR); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要大学。我加上了大学本身。

现在最重要的，再加一栏，她的名字 fts。

```
ALTER TABLE university ADD COLUMN fts tsvector; 
```

Enter fullscreen mode Exit fullscreen mode

更新这个。

```
UPDATE university SET fts=to_tsvector('english', coalesce(name,'') || ' ' || coalesce(description,'')); 
```

Enter fullscreen mode Exit fullscreen mode

完成数据库的步骤，这将创建搜索索引。

```
CREATE INDEX textsearch_idx ON university USING GIN (fts); 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在，我们可以开始为搜索引擎编写 REST API 了。

## 为搜索引擎创建 API

如果你注意到我们设置了包`pg`，特别为他我们需要创建文件夹`lib`和创建文件 db.js，这个文件当前连接到我们的数据库。

```
const pg = require('pg');

var config = {
  user: 'postgres',  
  database: 'universities',  
  password: 'qwe123', 
  host: 'localhost', 
  port: 5432, 
  max: 100, // max number of clients in the pool 
  idleTimeoutMillis: 30000, // how long a client is allowed to remain idle before being closed 
};

const pool = new pg.Pool(config);

pool.on('error', function (err, client) {
  console.error('idle client error', err.message, err.stack);
});

module.exports.query = function (text, values, callback) {
  console.log('query:', text, values);
  return pool.query(text, values, callback);
};

module.exports.connect = function (callback) {
  return pool.connect(callback);
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用数据库查询，我们包括这个文件。

好了，创建文件夹`functions`这个文件夹有我们需要的所有功能。

```
const pool = require('../lib/db');

function search(search_string, func) {
     pool.query(
        "SELECT * FROM university WHERE fts @@ to_tsquery('english',$1)", 
        [search_string],
        function(err, result) {  
            if(err) {
                func([])
            } else {
                func(result.rows)
            }
        }
    );
}

module.exports = search 
```

Enter fullscreen mode Exit fullscreen mode

并更新 server.js

```
const express       = require('express');
const app           = express();
const bodyParser    = require('body-parser');

const search        = require('./functions/search.js');

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

const port = process.env.PORT || 3000;

app.get('/search', function(req, res) {
    if (typeof req.query.text !== 'undefined') {
        search(req.query.text, function(data_items) {
            res.send({
                response : {
                    items : data_items
                }
            })
        })
    } else {
        res.send({error : '[100] Not search params text in query.'})
    }
})

app.listen(port);
console.log(new Date() + ' => start server :' + port); 
```

Enter fullscreen mode Exit fullscreen mode

去检查这个，打开你的浏览器，写下:

**[http://localhost:3000/search？](http://localhost:3000/search?text=Institute)正文=研究所**

示例响应:

```
 {
   "response":{
      "items":[
         {
            "id":1,
            "name":"Massachusetts Institute of Technology (MIT)",
            "description":"Founded in 1861, Massachusetts Institute of Technology (M.I.T.) is the best university in the world according to the QS World University Rankings 2016-2017, a title it has regularly claimed over the last few years. Rated 5+ QS stars, it’s achieved the maximum score across all categories, including research, employability, teaching, facilities, internationalization, innovation, specialist criteria and inclusiveness. MIT is also the best university in the world for a number of subjects including, but not limited to, architecture, linguistics, computer science and information systems, engineering and technology, chemistry, mathematics, economics and econometrics. Based in Cambridge, MIT’s labs and classroom continue to deliver pioneering research into the world’s most pressing issues, such as cancer therapy, identity politics, global engagement, energy, public service and industry.",
            "fts":"'-2017':29 '1861':8 '2016':28 '5':42 'accord':21 'achiev':47 'across':51 'also':67 'architectur':84 'base':99 'best':16,69 'cambridg':101 'cancer':121 'categori':53 'chemistri':94 'claim':35 'classroom':106 'comput':86 'continu':107 'criteria':62 'deliv':109 'econom':96 'econometr':98 'employ':56 'energi':127 'engag':126 'engin':91 'facil':58 'found':6 'global':125 'ident':123 'includ':54,79 'inclus':64 'industri':131 'inform':89 'innov':60 'institut':2,10 'internation':59 'issu':118 'lab':104 'last':38 'limit':82 'linguist':85 'm.i.t':13 'massachusett':1,9 'mathemat':95 'maximum':49 'mit':5,65,102 'number':76 'pioneer':110 'polit':124 'press':117 'public':128 'qs':24,43 'rank':27 'rate':41 'regular':34 'research':55,111 'scienc':87 'score':50 'servic':129 'specialist':61 'star':44 'subject':78 'system':90 'teach':57 'technolog':4,12,93 'therapi':122 'titl':31 'univers':17,26,70 'world':20,25,73,114 'year':40"
         }
            ...
      ]
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

它工作了

我们创造了小而快的引擎！

[完整代码](https://github.com/Gaserd/sm-fs-earch-engine)

如何让它变得更好？

我们可以为大学写支持响应页面，像这样**[http://localhost:3000/search？text = Institute&page = 2](http://localhost:3000/search?text=Institute&page=2)T3，这样会提高结果的上传速度。**

如果你有困难，给我写信[推特](https://twitter.com/gaserd123)或者邮件(【gaserd123@gmail.com】T2)。

感谢阅读，愿原力与你同在！