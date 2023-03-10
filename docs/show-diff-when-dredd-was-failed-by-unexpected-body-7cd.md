# 意外主体导致 dredd 失败时显示差异

> 原文：<https://dev.to/ryosukes/show-diff-when-dredd-was-failed-by-unexpected-body-7cd>

如果 dredd 因意外主体而失败，我们可以在 cli 上显示一些消息、实际响应和预期响应。但是，cli 上不会显示 diff。

> 目前没有差异，Dredd 显示预期的响应和真实的响应，让用户自己决定差异

[https://github.com/apiaryio/dredd/issues/765](https://github.com/apiaryio/dredd/issues/765)

如果要显示 diff，就需要像这样将 diff 插入到`transaction.fail`中(使用 node)。

```
var hooks = require('hooks');
var diff  = require('diff'); // need diff package

hooks.beforeEachValidation(function (transaction) {
    var real     = transaction.real.body;
    var expected = transaction.expected.body;

    if (real !== expected) {
        transaction.fail = diff.createPatch(
            transaction.id + " failed diff",
            JSON.stringify(JSON.parse(real), undefined, 4),
            JSON.stringify(JSON.parse(expected), undefined, 4),
            "real",
            "expected"
        );
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

cf: [编程测试失败](https://github.com/apiaryio/dredd/blob/master/docs/hooks-nodejs.md#failing-tests-programmatically)

如果有更好的方法，请告诉我怎么做🙂

谢谢你。