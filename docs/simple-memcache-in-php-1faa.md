# PHP 中的简单内存缓存

> 原文：<https://dev.to/scottrobertson/simple-memcache-in-php-1faa>

在过去的一年左右，我越来越多地使用缓存。它已经成为我工作的一个重要部分，几乎整个网站都依赖于它，如果没有它，事情将会很快变得一团糟。

下面是一个在 PHP 中使用 Memcache 的基本例子:

```
$cache = new Memcache;
$cache->connect('127.0.0.1', 11211);

function getPosts()
{
    $cache_key = 'getPosts';
    $posts = $cache->get($cache_key);

    if ($posts === false) {
        $posts = DB::query('SELECT * FROM posts');
        $cache->set($cache_key, $posts);
    }

    return $posts;
} 
```

Enter fullscreen mode Exit fullscreen mode