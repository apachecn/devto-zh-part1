# 使用 MySQLWithElasticsearch 包转换 MySQL 和 Elasticsearch

> 原文：<https://dev.to/ahmedkhan/convert-mysql-with-elasticsearch-using-mysqlwithelasticsearch-package>

Elasticsearch 是一个非常流行的全文搜索组件，可以很容易地集成到各种描述的项目中。排名靠前的网站(《华尔街日报》就是一个很好的例子！)使用 Elasticsearch 进行网站范围的搜索。

开发人员经常使用 Java 数据库连接(JDBC)将 Elasticsearch 与 MySQL 连接起来。但是，在本教程中，我将使用一个 PHP 包[mysqlwitheelasticsearch](https://packagist.org/packages/ahmedkhan847/mysqlwithelasticsearch)将一个现有项目与 elastic search 连接起来，然后进行全文搜索。

请注意，本教程中的代码仅适用于 Elasticsearch 版本 2 或更高版本！

### 安装和配置软件包

在本地 web 文件夹中新建一个文件夹，命名为 elasticsearch。接下来使用 Composer 安装软件包:

`composer require ahmedkhan847/mysqlwithelasticsearch:dev-release1`

等待该过程完成。

在根文件夹中，创建一个新文件，并将其命名为 config.php。将以下代码粘贴到该文件中:

```
<?php
$config['index'] = "blog";
$config['type'] ="article";
/*Fields for Elasticsearch same as you are using in your mysql table*/
$config['mappings'] = [
    'article' => [
        'properties' => [
            'id' => [
                'type' => 'integer'
            ],
            'article_name' => [
                'type' => 'string'
            ],
            'article_content' => [
                'type' => 'string'
            ],
            'article_url' => [
                'type' => 'string'
            ],
            'category_name' => [
                'type' => 'string'
            ],
            'username' => [
                'type' => 'string'
            ],
            'date' => [
                'type' => 'date',
                'format' => 'dd-MM-yyyy'
            ],
            'article_img' => [
                'type' => 'string'
            ],
        ]
    ]
];
/* SQL query to insert all the data useful when you are using insertAllData($conn) function */
$config['sqlalldata'] = "SELECT articles.article_id,articles.article_name,articles.article_content,articles.img,articles.url,categories.category_name,CONCAT(users.u_fname,' ',users.u_lname) AS username,DATE_FORMAT(articles.date,'%d-%m-%Y') AS dates FROM article INNER JOIN users ON users.user_id = article.user_Id INNER JOIN articles ON articles.article_id = article.article_id INNER JOIN categories ON categories.category_id = articles.category_id ";
/* SQL query to insert or update single data for insertNode($id = null, $con) where id will concated on runtime passed in parameter*/
$config['sqlsingledata'] = "SELECT articles.article_id,articles.article_name,articles.article_content,articles.img,articles.url,categories.category_name,CONCAT(users.u_fname,' ',users.u_lname) AS username,DATE_FORMAT(articles.date,'%d-%m-%Y') AS dates FROM article INNER JOIN users ON users.user_id = article.user_Id INNER JOIN articles ON articles.article_id = article.article_id INNER JOIN categories ON categories.category_id = articles.category_id WHERE articles.article_id =";

/*Replace your table row id field name here*/
//$config['rowid'] = "article_id";

/*Where to search*/
$config['search']['match'] = "article_content";
?>  
```

在这个配置文件中，我首先为 Elasticsearch 定义了索引和类型。接下来，我为 Elasticsearch 设置映射。然后，我插入了决定获取哪些数据的 SQL 查询。最后，我定义了执行全文搜索所需的列。

现在我已经为这个包创建了配置文件，接下来我将把它与 Elasticsearch 连接起来。

### 从 MySQL 获取 Elasticsearch 中的数据

创建一个新文件，命名为 index.php。

对于这个文件，我将首先从 MySQL 中为 Elasticsearch 获取所有数据。在继续之前，请确保 Elasticsearch 正在运行。现在，将以下代码粘贴到 index.php 文件中:

```
<?php
require "vendor/autoload.php";
include "config.php";
use SearchElastic\SearchElastic;

$elastic = new SearchElastic($config);
$con = new mysqli("localhost","root","","cms");
$result = $elastic->insertAllData($con);

echo '<pre>';
print_r($result);
echo '</pre>';
?>  
```

在上面的代码中，我首先创建了包的一个实例，然后将配置文件传递给它。接下来，我创建了一个到 MySQL 的连接，并将其传递给函数 insertAllData()。这个函数使用我在配置文件中定义的查询从 MySQL 中获取所有数据，并将所有数据保存在 Elasticsearch 中。当执行上述查询时，所有适当的数据都从 MySQL 中提取出来并保存在 Elasticsearch 中。

### 搜索 MySQL

一旦数据从 MySQL 获取到 Elasticsearch，我现在将在其中执行搜索。

为此，请用以下代码替换 index.php 的当前代码:

```
<?php
require "vendor/autoload.php";
include "config.php";
use SearchElastic\SearchElastic;

$elastic = new SearchElastic($config);
$result = $elastic->search("working with node js");
echo "<pre>";
print_r($result);
echo "</pre>";
?>  
```

在上面的代码中，我在 Elasticsearch 中执行了全文搜索。

### CUD 弹性搜索操作

如果每次在 MySQL 中添加数据都要添加到 Elasticsearch 中怎么办？mysqlwithelasticsearch 提供了一个函数，您可以使用它对 elasticsearch 数据执行 CUD(创建、更新和删除)操作。

1.  要在 Elasticsearch 中添加新的单个数据项，使用`insertNode($id = null, $con)`函数，其中$id 是要插入 Elasticsearch 的数据项的 id，$con 是数据库连接。
2.  要更新现有记录，使用`updateNode($id = null, $con)`。
3.  要删除现有节点，请使用`deleteNode($id)`。

### 结论:

在本文中，我介绍了连接 MySQL 和 elasticsearch 的[MySQL witheelastic search](https://packagist.org/packages/ahmedkhan847/mysqlwithelasticsearch)包。我还演示了如何使用这个包来实现搜索功能。我还强调了如何使用这个包实现 CUD 操作。