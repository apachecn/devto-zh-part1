# Elasticsearch repository php 包简介

> 原文：<https://dev.to/mustafah15/introducing-elasticsearch-repository-php-package-5hhk>

## Elasticsearch 活动存储库包

Elasticsearch Repository 是一个用于 Elasticsearch 的简单、智能的主动存储库实现。

### 特性

*   为您的弹性搜索索引、类型提供活动的存储库模式。
*   使用具有大业务逻辑的系统，最大限度地减少构建弹性搜索查询的代码行。
*   防止代码重复。
*   减少潜在的编程错误。
*   应用集中管理、一致的访问规则和逻辑。
*   通过将客户端对象从域模型中分离出来，提高代码的可维护性和可读性。
*   最大化可以自动化测试的代码量，并隔离客户端对象和域模型以支持单元测试。

## 安装

通过 composer
绘制

```
composer require mustafah15/elastic-repository 
```

Enter fullscreen mode Exit fullscreen mode

ElasticRepository 包含一个 queryBuilder 对象，用于以有效的方式从 elasticsearch 中获取结果

```
 public function getStudent($name, $class) {
    $this->queryBuilder->match('name', $name)->where('class', $class)->get();
} 
```

Enter fullscreen mode Exit fullscreen mode

完整的文档[请查看 github 库](https://github.com/mustafah15/elastic-repository#clipboard-documentation)