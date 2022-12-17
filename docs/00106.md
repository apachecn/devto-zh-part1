# LowDB 博客

> 原文：<https://dev.to/wkmn/lowdb-blog-577h>

LowDB 博客是 LowDB(丫不说？)和 jQuery powered blog，提供了完整的私有和公共 API。这个库经常更新。[![Remix](img/098a93546163c1d1bd480b4e3054d673.png)T2】](https://glitch.com/edit/#!/remix/lowdb-blog)

或者

在 Github 上克隆

```
$ git clone https://github.com/clarkhacks/LowDB-Blog.git
$ cd LowDB-Blog 
```

Enter fullscreen mode Exit fullscreen mode

## 设置

1.  运行`npm install`
2.  在`secret.example.json`中设置一个秘密。让它变得复杂，像一个硬密码，*(例如:48zu-QdF5a-dS)* 与网址友好的字符。
3.  将`secret.example.json`重命名为“secret.json”。(在生产中使用环境变量)
4.  通过 API 或`db.json`设置元数据并发布(在`.data/db.json`的生产存储数据中)
5.  运行`node server.js`

## API

该 API 理解和使用起来相对简单。它基于帖子请求和您之前设置的`.env`秘密。jQuery 中显示了一些例子。

### 新帖子

*终点*终点`/posts/new`

*参数*`title``date``body``token`。

*例* `$.post("/posts/new", { title: "Title", date: "Month Day, body: "Lorem ipsum...", token: "TOKENHERE"});`

所有查询都是必需的。

### Meta Data

*终点*终点`/meta/:ID`

*id*`user``bio``link``photo``photo``title`。

*参数*、`value`、`text`、`token`。

*例* `$.post("/meta/bio", { value: "Lorem ipsum...", token: "TOKENHERE"});`

*例二* `$.post("/meta/link", { text: "Google", value: "google.com", token: "TOKENHERE"});`

所有查询都需要表达式文本。文本仅用于`link` ID。

### 公共 API

公共 API 是 LowDB json 文件的只读版本。

*终点*终点`/api/v1`

* * *

[通过电子邮件回复](//mailto:arpitbatra123@gmail.com?subject=Reply:%20LowDB%20Blog)