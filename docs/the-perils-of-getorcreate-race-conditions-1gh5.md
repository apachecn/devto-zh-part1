# get_or_create 的危险:竞争条件

> 原文：<https://dev.to/adriennedomingus/the-perils-of-getorcreate-race-conditions-1gh5>

是一个很棒的帮助工具，直到它不再是。在[文件](https://docs.djangoproject.com/en/2.0/ref/models/querysets/#get-or-create)中有一个对我们将要谈论的内容的认可:

> *这个方法是原子的，假设正确的用法、正确的数据库配置和底层数据库的正确行为。但是，如果在数据库级别没有对在 *`get_or_create`* 调用中使用的 _ `kwargs`* (参见 *`unique`* 或 _ `unique_together` *)强制执行唯一性，这种方法很容易出现竞争情况，这会导致同时插入多个具有相同参数的行。*

让我们更详细地谈论它。下面是`get_or_create` :
的源代码中有趣的部分

```
lookup, params = self._extract_model_params(defaults, **kwargs)
try:
    return self.get(**lookup), False
except self.model.DoesNotExist:
    return self._create_object_from_params(lookup, params) 
```

非常简单明了，而且确实如其名所示。如果你进一步研究`_create_object_from_params`，你会注意到它不仅仅是调用`.create()`。这里发生了什么:

```
try:
    with transaction.atomic(using=self.db):
        obj = self.create(**params)
    return obj, True
except IntegrityError:
    exc_info = sys.exc_info()
    try:
        return self.get(**lookup), False
    except self.model.DoesNotExist:
        pass
    six.reraise(*exc_info) 
```

这很酷——它实际上考虑了竞争条件。它试图创建对象，但是如果该操作抛出一个`IntegrityError`，它将再次查找并尝试返回找到的内容。

问题是这样的:如果你在一个线程中点击这部分代码(意思是查找已经发生并且没有返回任何东西)*在一个对象上，这个对象对你正在基于*进行查找的属性没有唯一性约束，如果在另一个线程中创建了一个，在这个线程中的创建将**而不是**抛出一个`IntegrityError`，你将得到两个！这可能是好的——就目前而言。毕竟，您对`get_or_create`的调用返回了一个与您的参数匹配的实例，另一个线程中的调用也是如此，两者都将继续各自的快乐之路。

下一次您试图使用与`get_or_create`相同的查找参数检索对象时，问题就出现了*。因为现在你的数据库中有两个对象，当`get_or_create`尝试它的`.get()`(注意，不是`.filter()`)时，你会得到一个`MultipleObjectsReturned`错误，除非你自己捕捉到这个异常，否则没有办法。*

这个故事的寓意是什么？不要在那些没有惟一性约束的对象上使用`get_or_create`，在数据库级别上使用*。*