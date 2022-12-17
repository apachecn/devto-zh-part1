# type error:“list”对象不可调用

> 原文：<https://dev.to/farhatnawaz/typeerror-list-object-is-not-callable-28>

我正在用 mongoengine 编写 Flask 中的 API。在我的“搜索”端点中，API 接收一个“id ”,并根据该 id 从数据库中检索所有对象。但是当我试图像这样以 json 形式返回这些对象时:

```
 return [redemp.to_json for redemp in redemps] 
```

Enter fullscreen mode Exit fullscreen mode

我得到错误消息说“type error:' list ' object is not callable”
这里出了什么问题？