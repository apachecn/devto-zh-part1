# 在 Laravel: load()或 with()中贪婪地下载？

> 原文：<https://dev.to/phpprofi/---laravel-load--with-cp2>

T2，T3，T4，T5。佩尔。T6-这是每个使用 Laravel 的开发人员都必须了解的一个关于 T0 和 t1 laravel eloquent-a 方法的小笔记。这不是什么新鲜事，但似乎不是每个人都知道。T7/

今天，在处理我的一个项目(写在 laravel 框架上)时，我遇到了一种情况，即我需要为一个模型获取与该模型相关联的其他模型的数据。因此，对于这一点，我有两个 Laravel 提供的选项，通常被称为贪婪下载:

*   [同()](https://laravel.com/docs/5.2/eloquent-relationships#eager-loading)
*   [load()](https://laravel.com/docs/5.2/eloquent-relationships#lazy-eager-loading)

这两种方法都达到同样的最终结果——贪婪地将模型装入第一个。实际上，他们两个都运行相同的两个查询。选哪个？

http://phpprofi.ru/blogs/post/71 的читать