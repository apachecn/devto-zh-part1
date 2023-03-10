# 使用 PostgreSQL 在 Django 中进行全文搜索

> 原文：<https://dev.to/pauloxnet/full-text-search-in-django-with-postgresql-ddp>

> 这篇文章基于我在 Django 中用 PostgreSQL 进行全文搜索的演讲。

*我的演讲内容有: [PyCon IT 2017](https://www.paulox.net/2017/04/08/pyconit-2017/) ，[europhon 2017](https://www.paulox.net/2017/07/12/europython-2017/)， [PGDay。IT 2017](https://www.paulox.net/2017/10/13/pgdayit-2017/) 、 [PyRoma 2017](https://www.paulox.net/2017/11/14/pyroma-2017/) 、T9】*

## 目标

> 为了展示我如何在一个真实项目中使用 **[Django](#django-support)** **[全文搜索](#full-text-search)** 和 **[PostgreSQL](#fts-in-postgresql)** 。

## 动机

> 仅使用 **[Django](#django-support)** 和 **[PostgreSQL](#fts-in-postgresql)** 实现 **[全文搜索](#full-text-search)** ，而不借助 **[外部产品](#tested-products)** 。

## 目录

这些是本文的主要主题:

*   [全文搜索](#full-text-search)一般情况下
*   [用于全文搜索的外部产品](#tested-products)
*   [PostgreSQL](#fts-in-postgresql) 中的全文搜索支持
*   对全文搜索的支持
*   concertiaroma.com[现实世界项目](#wwwconcertiaromacom)
*   [全文搜索即将推出的创新](#whats-next)
*   一些个人的[结论](#conclusions)
*   有用的[资源](#resources)

## 全文搜索

全文搜索源于在计算机存储的文档中进行一些搜索的需要。例如，查找包含特定单词及其变体的所有文档。如果一个文档包含“house”或“houses ”,对于搜索也是如此。

> "...**全文搜索** *指的是**搜索**单个计算机存储的**文档**或**全文数据库****集合**的技术
> 
> ——**[维基百科](https://en.wikipedia.org/wiki/Full-text_search)**

***FTS**=**F**ul-**T**ext**S**earch

## FTS 特色

这是我们可以在一个非常高级的全文搜索中找到的一些功能的列表，将在现实世界的网站中使用。

*   堵塞物
*   等级
*   停用词移除
*   多语言支持
*   重音支持
*   索引
*   短语搜索

## 被测产品

Elastic 和 Solr 是目前流行的两种全文搜索软件。还有其他的，但是这些是我在我的专业项目中唯一用过的。它们基于 Lucene，用 Java 编写。

*   [弹性搜索](#elasticsearch)
*   [阿帕奇 Solr](#apache-solr)

## 弹性搜索

### 抢购市场

Snap market 是我工作过的一家初创公司，它开发了一个移动电话应用程序，为大约 50 万移动用户买卖二手物品。

### 问题

#### 管理问题

在这个项目中，我们使用了已经安装在 FreeBSD 系统上的 **Elasticsearch** 。我们在管理和同步它时遇到了一些困难。

#### 修补 Java 插件

我们不得不对一个 Java 插件应用一些补丁，这个插件是我们用来“分解”德语单词的。

`Java`

```
@@ -52,7 +52,8 @@ public class DecompoundTokenFilter … {
-            posIncAtt.setPositionIncrement(0); +            if (!subwordsonly)
+                posIncAtt.setPositionIncrement(0);
             return true;
         } 
```

Enter fullscreen mode Exit fullscreen mode

## 阿帕奇 Solr

### 守门员

GoalScout.com 是一个致力于展示公众用户上传的体育视频的网站，大约有 30，000 个视频。

### 问题

#### 同步问题

在这个项目中使用 Solr 进行全文搜索是客户的选择。在 PostgreSQL 和 Solr 之间同步数据时，我们总是会遇到一些问题。

#### 单向

在这些问题之后，我们必须开始做所有的**写**到 *PostgreSQL* 和所有的**读**从 *Apache SOLR* 。

## 现有产品

### 优点

*   **功能齐全的**产品
*   **资源** *(文献，文章，...)*

我所写的产品功能全面且先进，有许多关于它的在线资源:文档、文章和常见问题解答。

### CONS

*   **同步**
*   强制使用**驱动** *(haystack，bungiesearch，...)*

我发现了一些同步方面的问题。我总是不得不使用一个驱动程序，这是 Django 和搜索引擎之间的瓶颈。在某些情况下，我不得不修改代码。

### 面向运营

重点是系统集成。我更像是一名开发人员，而不是运营人员，所以我不喜欢被迫集成各种系统。我更喜欢通过编写 python 代码来开发和解决问题。

## PostgreSQL 中的 FTS

自 2008 年以来，PostgreSQL 一直支持全文搜索。在内部，它使用“tsvecor”和“tsquery”数据类型来处理要搜索的数据。它有一些索引可以用来加速搜索:杜松子酒和要点。PostgreSQL 在 2016 年增加了对短语搜索的支持。版本 10 中增加了对 JSON[B]全文搜索的支持。

*   **FTS 支持** *自***8.3 版本** *(2008)*
*   **TSVECTOR** 来表示**文本数据**
*   **TSQUERY** 来表示**搜索谓词**
*   **特殊指标** ( **杜松子**，**要点**)
*   **短语搜索** *自***9.6 版** *(2016)*
*   FTS 为**JSON【B】***自* **版本 10** *(2017)*

## 什么是单据

*“文档”是全文搜索中使用的一般概念，也是搜索完成的位置。在数据库中，文档可以是表上的一个字段，也可以是一个表中或不同表中多个字段的组合。*

> "...一个**文档**是在一个**全文搜索**系统中搜索的**单元**；例如，杂志**文章**或电子邮件**消息**……”
> 
> - **[PostgreSQL 文档](https://www.postgresql.org/docs/10/static/textsearch-intro.html)**

## 姜戈支持

### 特性

从版本 1.10 开始，django.contrib.postgres 模块包含了对 django 中全文搜索的支持。1.11 版本中增加了布林和杜松子索引。GIN 索引对于加速全文搜索非常有用。

*   **模块**[django . contrib . postgres](https://docs.djangoproject.com/en/2.0/ref/contrib/postgres/)
*   **FTS 支持**自版本 **1.10** *(2016)*
*   **布林**和**轧棉**1.11*(2017)*版本以来的指数

### 面向开发

重点是编程。在 Django 中使用 Postgres 全文搜索对开发人员来说更加友好。

## 制作查询

从 Django 官方文档中的模型开始，我们可以看看 Django 中的全文搜索功能。我们有一个 Blog 和一个 Author 类，它们通过一个入口类连接在一起。

`Python`

```
from django.contrib.postgres.search import SearchVectorField
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()
    lang = models.CharField(max_length=100, default='english')

    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField(auto_now_add=True)
    mod_date = models.DateField(auto_now=True)
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField(default=0)
    n_pingbacks = models.IntegerField(default=0)
    rating = models.IntegerField(default=5)
    search_vector = SearchVectorField(null=True)

    def __str__(self):
        return self.headline 
```

Enter fullscreen mode Exit fullscreen mode

## 标准查询

*这些是我们可以在 Django 中使用“过滤器”对模型进行的基本搜索。*

`Python`

```
from blog.models import Author, Blog, Entry

Author.objects.filter(
  name__contains='Terry'
).values_list('name', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_author"."name"
  FROM "blog_author"
 WHERE "blog_author"."name"::text LIKE '%Terry%' 
```

Enter fullscreen mode Exit fullscreen mode

```
['Terry Gilliam', 'Terry Jones']

```

为了得到更多的结果，我们可以使用“不区分大小写的包容”。

`Python`

```
Author.objects.filter(
  name__icontains='ERRY'
).values_list('name', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_author"."name"
  FROM "blog_author"
 WHERE UPPER("blog_author"."name"::text) LIKE UPPER('%ERRY%') 
```

Enter fullscreen mode Exit fullscreen mode

```
['Terry Gilliam', 'Terry Jones', 'Jerry Lewis']

```

## 无重音查询

通过激活非 accent PostgreSQL 模块，我们可以使用“非 accent”扩展。

`Python`

```
from django.contrib.postgres.operations import UnaccentExtension

class Migration(migrations.Migration):
    ...

    operations = [
        UnaccentExtension(),
        ...
    ] 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
CREATE EXTENSION unaccent; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以搜索而不用担心不同语言中有用的重音字符。

`Python`

```
Author.objects.filter(
  name__unaccent='Helene Joy'
).values_list('name', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_author"."name"
  FROM "blog_author"
 WHERE UNACCENT("blog_author"."name") = (UNACCENT('Helene Joy')) 
```

Enter fullscreen mode Exit fullscreen mode

```
['Hélène Joy']

```

> **警告**
> 
> 使用此过滤器的查询通常会执行全表扫描，这在大型表上可能会很慢。

## 三元组相似度

通过激活 trigram PosgreSQL 模块，我们可以使用“trigram”扩展。

`Python`

```
from django.contrib.postgres.operations import TrigramExtension

class Migration(migrations.Migration):
    ...

    operations = [
        TrigramExtension(),
        ...
    ] 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
CREATE EXTENSION pg_trgm; 
```

Enter fullscreen mode Exit fullscreen mode

“三元组”是从字符串中提取的一组三个连续的字符。我们可以通过两个字符串共享的“三元组”的数量来评估它们的相似性。

`Python`

```
Author.objects.filter(
  name__trigram_similar='helena'
).values_list('name', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_author"."name"
  FROM "blog_author"
 WHERE "blog_author"."name" % 'helena' 
```

Enter fullscreen mode Exit fullscreen mode

```
['Helen Mirren', 'Helena Bonham Carter']

```

## 搜索查找

这是 Django 的基本搜索查找，通过它我们可以对一个字段执行真正的全文搜索调用。

`Python`

```
Entry.objects.filter(
  body_text__search='Cheese'
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE to_tsvector(COALESCE("blog_entry"."body_text", ''))
       @@ (plainto_tsquery('Cheese')) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Cheese on Toast recipes', 'Pizza Recipes']

```

为了执行更复杂的查询，我们必须使用三个新的 Django 对象: [SearchVector](#searchvector) ， [SearchQuery](#searchquery) ， [SearchRank](#searchrank) 。

## 搜索矢量

我们可以使用“搜索向量”在同一对象或相关对象的更多字段中构建我们的文档。然后我们可以用字符串过滤文档。

`Python`

```
from django.contrib.postgres.search import SearchVector

search_vector = SearchVector('body_text', 'blog__name')

Entry.objects.annotate(
  search=search_vector
).filter(
  search='Cheese'
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 INNER JOIN "blog_blog"
    ON ("blog_entry"."blog_id" = "blog_blog"."id")
 WHERE to_tsvector(
         COALESCE("blog_entry"."body_text", '') || ' ' ||
         COALESCE("blog_blog"."name", '')
       ) @@ (plainto_tsquery('Cheese')) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Cheese on Toast recipes', 'Pizza Recipes']

```

## SearchQuery

当我们使用“搜索查询”将文本插入全文搜索时，我们甚至可以对用户文本应用“词干提取”和“停用词移除”。我们可以对这些应用基本的逻辑运算。

### 不

`Python`

```
from django.contrib.postgres.search import SearchQuery

search_query = ~SearchQuery('toast')
search_vector = SearchVector('body_text')

Entry.objects.annotate(
  search=search_vector
).filter(
  search=search_query
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE to_tsvector(COALESCE("blog_entry"."body_text", ''))
       @@ (!!(plainto_tsquery('toast'))) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Pizza Recipes', 'Pain perdu']

```

### 或

`Python`

```
search_query = SearchQuery('cheese') | SearchQuery('toast')
search_vector = SearchVector('body_text')

Entry.objects.annotate(
  search=search_vector
).filter(
  search=search_query
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE to_tsvector(COALESCE("blog_entry"."body_text", ''))
       @@ ((
         plainto_tsquery('cheese') ||
         plainto_tsquery('toast')
       )) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Cheese on Toast recipes', 'Pizza Recipes']

```

### 和

`Python`

```
search_query = SearchQuery('cheese') & SearchQuery('toast')
search_vector = SearchVector('body_text')

Entry.objects.annotate(
  search=search_vector
).filter(
  search=search_query
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE to_tsvector(COALESCE("blog_entry"."body_text", ''))
       @@ ((
         plainto_tsquery('cheese') &&
         plainto_tsquery('toast')
       )) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Cheese on Toast recipes']

```

## SearchRank

我们可以使用 PostgreSQL“rank”来计算文档相对于搜索文本的分数，并使用它来过滤和排序。

`Python`

```
from django.contrib.postgres.search import SearchRank

search_vector = SearchVector('body_text')
search_query = SearchQuery('cheese')
search_rank = SearchRank(search_vector, search_query)

Entry.objects.annotate(
  rank=search_rank
).order_by(
  '-rank'
).values_list('headline', 'rank')) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline",
       ts_rank(
         to_tsvector(COALESCE("blog_entry"."body_text", '')),
         plainto_tsquery('cheese')
       ) AS "rank"
  FROM "blog_entry"
 ORDER BY "rank" DESC 
```

Enter fullscreen mode Exit fullscreen mode

```
[
  ('Cheese on Toast recipes', 0.0889769),
  ('Pizza Recipes', 0.0607927),
  ('Pain perdu', 0.0)
]

```

## 搜索配置

我们可以设置“搜索向量”或“搜索查询”来对特定语言执行“词干提取”或“停用词移除”。

`Python`

```
language = 'french'

search_vector = SearchVector('body_text', config=language)
search_query = SearchQuery('œuf', config=language)

Entry.objects.annotate(
  search=search_vector
).filter(
  search=search_query
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE to_tsvector(
         'french'::regconfig,
         COALESCE("blog_entry"."body_text", ''))
       @@ (
         plainto_tsquery('french'::regconfig, 'œuf')
       ) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Pain perdu']

```

我们可以从一个类字段中获得语言。

`Python`

```
from django.db.models import F

language = F('blog__lang')

search_vector = SearchVector('body_text', config=language)
search_query = SearchQuery('œuf', config=language)

Entry.objects.annotate(
  search=search_vector
).filter(
  search=search_query
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 INNER JOIN "blog_blog"
    ON ("blog_entry"."blog_id" = "blog_blog"."id")
 WHERE to_tsvector(
         "blog_blog"."lang"::regconfig,
         COALESCE("blog_entry"."body_text", '')
       )
       @@ (
         plainto_tsquery("blog_blog"."lang"::regconfig, 'œuf')
       ) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Pain perdu']

```

## 查询权重

可以设置搜索，为不同的字段赋予不同的权重，然后在搜索中使用这些值。

`Python`

```
search_vector = SearchVector('body_text', weight='A') +
                SearchVector('headline', weight='B')
search_query = SearchQuery('cheese')
search_rank = SearchRank(search_vector, search_query)

Entry.objects.annotate(
  rank=search_rank
).order_by(
  '-rank'
).values_list('headline', 'rank') 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline",
       ts_rank((
         setweight(
           to_tsvector(COALESCE("blog_entry"."body_text", '')),
           'A'
         )
         ||
         setweight(
           to_tsvector(COALESCE("blog_entry"."headline", '')),
           'B'
         )
       ), plainto_tsquery('cheese')) AS "rank"
  FROM "blog_entry"
 ORDER BY "rank" DESC 
```

Enter fullscreen mode Exit fullscreen mode

```
[
  ('Cheese on Toast recipes', 0.896524),
  ('Pizza Recipes', 0.607927),
  ('Pain perdu', 0.0)
]

```

## [搜索向量场](#searchvectorfield)

如果我们想加快和简化查询的执行，我们可以在模型中添加一个“搜索向量域”,然后在这个特定的域上执行搜索。

`Python`

```
Entry.objects.filter(
  search_vector='cheese'
).values_list('headline', flat=True) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
SELECT "blog_entry"."headline"
  FROM "blog_entry"
 WHERE "blog_entry"."search_vector"
       @@ (plainto_tsquery('cheese')) = true 
```

Enter fullscreen mode Exit fullscreen mode

```
['Cheese on Toast recipes', 'Pizza Recipes']

```

我们必须手动更新该字段，例如定期执行命令，使用 Django 信号或 PostgreSQL 触发器。

`Python`

```
search_vector = SearchVector('body_text')

Entry.objects.update(search_vector=search_vector) 
```

Enter fullscreen mode Exit fullscreen mode

`SQL`

```
UPDATE "blog_entry"
   SET "search_vector" = to_tsvector(
         COALESCE("blog_entry"."body_text", '')) 
```

Enter fullscreen mode Exit fullscreen mode

## 【www.concertiaroma.com】T2

> "...今天在首都的演出”

*[【www.concertiaroma.com】](https://www.concertiaroma.com/)是一个用于搜索罗马市的表演、节日、乐队和场地的网站，自 2014 年开始上线。*

项目的**号**:

*   约 1000 个**场馆**
*   大约 15000 个**波段**
*   16000 多**显示**
*   大约 200 个**节日**
*   约 3 万**用户/月**

## 2.0 版本

*该网站的旧版本是几年前用 Django 1.7 开发的，运行在 Python 2.7 上。数据由 PostgreSQL 版管理，搜索使用 SQL `LIKE`语法执行。*

*   Python **2.7**
*   姜戈 **1.7**
*   PostgreSQL **9.1**
*   SQL **像**

## 3.0 版本

最近发布的新版本是用 Django 1.11 开发的，运行在 Python 3.6 上。数据由 PostgreSQL 9.6 管理，搜索使用其全文搜索引擎。

*   Python **3.6**
*   姜戈 **1.11**
*   PostgreSQL **9.6**
*   FTS

## 波段型号

*我们可以从项目中出现的相同模型开始，在[www.concertiaroma.com](http://www.concertiaroma.com)中查看全文搜索的功能。我们有一个* *流派* *类连接到一个**乐队**类。*

`Python`

```
from django.db import models
from .managers import BandManager

class Genre(models.Model):
    name = models.CharField(max_length=255)

class Band(models.Model):
    nickname = models.CharField(max_length=255)
    description = models.TextField()
    genres = models.ManyToManyField(Genre)

    objects = BandManager() 
```

Enter fullscreen mode Exit fullscreen mode

## 乐队经理

这是 Band 类的“Manager”的一个例子，它定义了一个包含所有全文搜索逻辑的搜索方法。

`Python`

```
from django.contrib.postgres.aggregates import StringAgg
from django.contrib.postgres.search import (
    SearchQuery, SearchRank, SearchVector, TrigramSimilarity,
)
from django.db import models

search_vectors = (
    SearchVector('nickname', weight='A', config='english') +
    SearchVector(
        StringAgg('genres__name', delimiter=' '),
        weight='B', config='english') +
    SearchVector('description', weight='D', config='english')
)

class BandManager(models.Manager):

    def search(self, text):
        search_query = SearchQuery(text, config='english')
        search_rank = SearchRank(search_vectors, search_query)
        trigram_similarity = TrigramSimilarity('nickname', text)
        return self.get_queryset().annotate(
            search=search_vectors
        ).filter(
            search=search_query
        ).annotate(
            rank=search_rank + trigram_similarity
        ).order_by('-rank') 
```

Enter fullscreen mode Exit fullscreen mode

## 波段测试设置

为了更好地理解该机制，我们可以考虑一个简化测试的例子。

在测试设置中，我们定义了示例数据，随后我们将使用这些数据来测试我们的搜索:两个乐队和我们分配给这两个乐队的两种音乐类型。

`Python`

```
from collections import OrderedDict
from django.test import TestCase
from .models import Band, Genre

class BandTest(TestCase):

    def setUp(self):
        # Genres
        blues, _ = Genre.objects.get_or_create(name='Blues')
        jazz, _ = Genre.objects.get_or_create(name='Jazz')
        swing, _ = Genre.objects.get_or_create(name='Swing')
        # Bands
        ella_fitzgerald, _ = Band.objects.get_or_create(
            nickname='Ella Fitzgerald',
            description=(
                'Ella Jane Fitzgerald (25 Apr 1917-15 Jun 1996)'
                ' was an American jazz singer often referred to'
                ' as the First Lady of Song, Queen of Jazz and '
                'Lady Ella. She was noted for her purity of '
                'tone, impeccable diction, phrasing and '
                'intonation, and a horn-like improvisational '
                'ability, particularly in her scat singing.'))
        django_reinhardt, _ = Band.objects.get_or_create(
            nickname='Django Reinhardt',
            description=(
                'Jean Django Reinhardt (23 Jan 1910-16 May 1953)'
                ' was a Belgian-born, Romani French jazz '
                'guitarist and composer, regarded as one of the '
                'greatest musicians of the twentieth century. He'
                ' was the first jazz talent to emerge from '
                'Europe and remains the most significant.'))
        louis_armstrong, _ = Band.objects.get_or_create(
            nickname='Louis Armstrong',
            description=(
                'Louis Armstrong (4 Aug 1901-6 Jul 1971), '
                'nicknamed Satchmo, Satch and Pops, was an '
                'American trumpeter, composer, singer and '
                'occasional actor who was one of the most '
                'influential figures in jazz. His career spanned'
                ' five decades, from the 1920s to the 1960s, '
                'and different eras in the history of jazz.'))
        # Bands and Genres
        ella_fitzgerald.genres.add(blues)
        django_reinhardt.genres.add(jazz)
        louis_armstrong.genres.add(blues, swing)

    def test_band_search(self):
        # ... 
```

Enter fullscreen mode Exit fullscreen mode

*内容来自“维基百科，免费的百科全书”*:

*   [艾拉·费兹杰拉](https://en.wikipedia.org/wiki/Ella_Fitzgerald)
*   [坦哥·雷恩哈特](https://en.wikipedia.org/wiki/Django_Reinhardt)
*   路易斯·阿姆斯特朗

## 波段测试方法

*在对频段的搜索测试中，我们简单地调用了搜索方法，给出了一个搜索文本，并得到了字段“昵称”和“费率”的值列表。“昵称”存储在频段表中，而“速率”则由我们的搜索方法在运行时计算。*

`Python`

```
from collections import OrderedDict
from django.test import TestCase
from .models import Band, Genre

class BandTest(TestCase):

    def setUp(self):
        # ... 
    def test_band_search(self):
        band_queryset = Band.objects.search(
            'jazz').values_list('nickname', 'rank')
        band_objects = list(
            OrderedDict(band_queryset).items())
        band_list = [
            ('Django Reinhardt', 0.265124),
            ('Ella Fitzgerald', 0.0759909),
            ('Louis Armstrong', 0.0759909)]
        self.assertSequenceEqual(band_objects, band_list) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将我们的搜索结果与一个列表进行了比较，在这个列表中，我们定义了由乐队的昵称和搜索率的数值组成的对，换句话说，就是定义该项目的重要性的数值。

## 接下来是什么

我们已经看到了 Django 和 PostgreSQL 全文搜索的当前特性的简化使用。

这两个软件程序在这些领域都变得越来越好，这些是在新的未来可以使用的一些功能。

*   高级**拼写错误**支持
*   **多种**语言配置
*   搜索**建议**
*   **用**触发**搜索向量场**
*   **JSON/JSONB** 全文搜索
*   **朗姆**索引

## 结论

总之，下面是评估 Django 中使用 PostgreSQL 实现全文搜索的条件:

*   没有任何额外的**依赖性**
*   不做非常复杂的**搜索**
*   **轻松管理**所有组件
*   避免不同系统之间的数据**同步**
*   堆栈中已经有 **PostgreSQL**
*   在一个只有 Python 的环境中运行

## 鸣谢

### 20tab

为所有**支持**(【www.20tab.com】T2)

### 马克·塔姆林

对于**django . contrib . postgres**(【github.com/mjtamlyn】T2

## 资源

这些是我用来准备这篇文章和开发我向你展示的搜索功能的资源。

*   [Wikipedia.org-全文搜索定义](https://en.wikipedia.org/wiki/Full-text_search)
*   [DjangoProject.com-全文搜索文档](https://docs.djangoproject.com/en/2.0/ref/contrib/postgres/search/)
*   [PostgreSQL.org-全文搜索文档](https://postgresql.org/docs/10/static/textsearch.html)
*   [StackOverflow.com-姜戈 PostgreSQL 全文搜索问题](https://stackoverflow.com/questions/tagged/django+postgresql+full-text-search)
*   [SimonWillison.net——用 Django 和 PostgreSQL 实现分面搜索](https://simonwillison.net/2017/Oct/5/django-postgresql-faceted-search/)
*   [PostgreSQL 源代码](https://github.com/postgres/postgres)
*   [Django 源代码](https://github.com/django/django)

## 谢谢

### CC BY-SA

本文和相关演示文稿是以知识共享署名共享许可协议发布的。

【creativecommons.org/licenses/by-sa】T2

### 源代码

我在 GitHub 上发布了本文使用的源代码。

【github.com/pauloxnet/django_queries】T2

### 幻灯片

您可以从我的 SpeakerDeck 帐户下载演示文稿。

【speakerdeck.com/pauloxnet】T2

> 最初发布于[www . paulox . net/2017/12/22/full-text-search-in-django-with-PostgreSQL](https://www.paulox.net/2017/12/22/full-text-search-in-django-with-postgresql/#full-text-search-in-django-with-postgresql)