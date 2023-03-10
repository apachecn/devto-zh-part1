# Postgres 中的 JSONB 入门

> 原文：<https://dev.to/craigkerstiens/getting-started-with-jsonb-in-postgres>

*[原载于我的博客](http://www.craigkerstiens.com/2017/03/12/getting-started-with-jsonb-in-postgres/)*

JSONB 是 Postgres 中一个非常棒的数据类型。这些天我发现自己每周都在使用它。通常在使用一些 API(比如 [clearbit](https://www.clearbit.com) )的时候，我会得到一个 JSON 响应，而不是把它解析成一个表结构，把它扔进一个 JSONB 然后查询它的各个部分真的很容易。

如果你不熟悉 JSONB，它是数据库中 JSON 的二进制表示。你可以在这里阅读更多关于它和 JSON [的内容。](https://www.citusdata.com/blog/2016/07/14/choosing-nosql-hstore-json-jsonb/)

在使用 JSONB 的过程中，有一些快速的技巧可以让你更快地使用它:

### 索引

在很大程度上，你不必考虑太多。使用 Postgres 强大的索引类型，您可以添加一个索引，并自动索引 JSON 文档中的所有内容，所有键和所有值。这里的关键是添加一个`GIN`索引。一旦这样做了，当你搜索某个值时，查询应该会快得多:

```
CREATE INDEX idx_data ON companies USING GIN (data); 
```

Enter fullscreen mode Exit fullscreen mode

### 查询

查询需要多做一点工作，但是一旦掌握了基础知识，就可以非常简单了。有几个新的操作符，您可能想快速使用，从那里开始查询就变得容易了。

对于最基本的部分，你现在有一个操作符，所以遍历不同的键。首先让我们了解一下 JSON 是什么样子的，这样我们就有东西可以使用了。这是我们从 Clearbit 得到的一组样本数据:

```
{
  "domain": "citusdata.com",
  "company": {
    "id": "b1ff2bdf-0d8d-4d6d-8bcc-313f6d45996a",
    "url": "http:\/\/citusdata.com",
    "logo": "https:\/\/logo.clearbit.com\/citusdata.com",
    "name": "Citus Data",
    "site": {
      "h1": null,
      "url": "http:\/\/citusdata.com",
      "title": "Citus Data",
    },
    "tags": [
      "SAAS",
      "Enterprise",
      "B2B",
      "Information Technology & Services",
      "Technology",
      "Software"
    ],
    "domain": "citusdata.com",
    "twitter": {
      "id": "304455171",
      "bio": "Builders of Citus, the extremely scalable PostgreSQL database.",
      "site": "https:\/\/t.co\/hKpZjIy7Ej",
      "avatar": "https:\/\/pbs.twimg.com\/profile_images\/630900468995108865\/GJFCCXrv_normal.png",
      "handle": "citusdata",
      "location": "San Francisco, CA",
      "followers": 3770,
      "following": 570
    },
    "category": {
      "sector": "Information Technology",
      "industry": "Internet Software & Services",
      "subIndustry": "Internet Software & Services",
      "industryGroup": "Software & Services"
    },
    "emailProvider": false
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

抱歉，这有点长，但它给了我们一个很好的例子。

### 基本查找

现在让我们查询一些相当基本的东西，领域:

```
# SELECT data->'domain' 
FROM companies 
WHERE domain='citusdata.com' 
LIMIT 1;

    ?column?
----------------------
 "citusdata.com" 
```

Enter fullscreen mode Exit fullscreen mode

`->`可能是您将在 JSONB 中使用的第一个操作符。遍历 JSON 很有帮助。尽管您希望获得文本形式的值，但实际上您会希望使用`->>`。不是给你一些引用的回复或者 JSON 对象，而是以文本的形式获取，这样会更简洁一些:

```
# SELECT data->>'domain' 
FROM companies 
WHERE domain='citusdata.com' 
LIMIT 1;

    ?column?
----------------------
 citusdata.com 
```

Enter fullscreen mode Exit fullscreen mode

### 对数值进行过滤

现在有了像 clearbit 这样的东西，你可能只想过滤掉某些类型的公司。我们可以在示例数据中看到有一堆标签。如果我们只想找到带有 B2B 标签的公司，那么一旦我们确定了 JSON 的这一部分，就可以使用`?`操作符。`?`操作符会告诉我们 JSON 的某个部分是否有顶级键:

```
SELECT *
FROM companies
WHERE data->'company'->'tags' ? 'B2B' 
```

Enter fullscreen mode Exit fullscreen mode

### JSONB 但是漂亮

在查询 JSONB 时，您通常会得到一个很好的 JSON 压缩集。如果你把它放到你的应用程序中，这一切都很好，但是如果你手动调试和测试，你可能想要一些可读性更好的东西。当然 Postgres 会支持你，你可以用一个漂亮的打印函数来包装你的 JSONB:

```
SELECT jsonb_pretty(data)
FROM companies; 
```

Enter fullscreen mode Exit fullscreen mode

### 多得多

在[文档](https://www.postgresql.org/docs/9.5/static/functions-json.html)中有更多的内容，当你需要的时候，你可以方便地找到它们。`jsonb_each`将一个 JSONB 文档展开成单独的行。因此，如果您想统计一家公司的每个标签出现的次数，这将有所帮助。想要解析出一个 JSONB 到 Postgres 中的一行/记录，这里有`jsonb_to_record`。在你想做的每件事上，医生都是你的朋友，但如果你想开始做`JSONB`，希望这几个步骤能帮助你启动事情。