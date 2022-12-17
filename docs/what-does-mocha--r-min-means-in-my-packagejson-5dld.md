# package.json 中我的测试脚本中的“-R min”标志是什么意思？

> 原文：<https://dev.to/jjjjcccjjf/what-does-mocha--r-min-means-in-my-packagejson-5dld>

大家好。我在谷歌和文档上都找不到答案。我也不想在 Stackoverflow 发帖。知道国旗是什么意思吗？

```
 "scripts": {
    "test": "nodemon --exec \"mocha -R min\"",
    "start": "nodemon app.js"
  }, 
```

Enter fullscreen mode Exit fullscreen mode

谢谢！

~~编辑:好了，我弄明白了`min`旗。它抑制了冗长的测试消息。~~

~~不带闽旗:`√ it should GET all the users (78ms)`
带闽旗:`1 passing (156ms)`~~

~~那-R 呢？~~

编辑#2:好的，`-R`标志和`min`参数应该放在一起。正如 [@weswedding](https://dev.to/weswedding) 所说，这是记者旗，下一个字是记者的名字。在这种情况下，`min`。

当然，这些都在文档中....