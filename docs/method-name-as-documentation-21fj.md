# 作为文档的方法名

> 原文：<https://dev.to/burdettelamar/method-name-as-documentation-21fj>

嘿，红宝石编码员！

你认识这个习语吗？

```
Object.const_get(class_name) 
```

Enter fullscreen mode Exit fullscreen mode

还是这个？

```
Object.const_get(class_name).new(*args) 
```

Enter fullscreen mode Exit fullscreen mode

当我想在我的 RubyTest 项目中做这两件事的时候，我不得不 Google 一下如何做。

现在，如果我把这段代码放到我的项目中，以后我能认出这些习惯用法吗？下个月？明年？

我可以添加注释来解释，但是注释可能会过时(跟不上代码的变化)，或者与代码分离，甚至被删除。

您可以通过将一个不寻常的习语推入一个命名良好的方法来帮助下游的代码增强器/维护器。

(提示:如果你不确定谁是下游的增强者/维护者，那就是你！)

因此，我创建了这个:

```
class ObjectHelper

  def self.get_class_for_class_name(class_name)
    Object::const_get(class_name)
  end

  def self.instantiate_class_for_class_name(class_name, *args)
    self.get_class_for_class_name(class_name).new(*args)
  end

end 
```

Enter fullscreen mode Exit fullscreen mode

PS:我的 GitHub 项目是关于 Ruby 中的测试自动化。它有一个针对 web UI 和 REST API 的演示测试的[测试者之旅](https://github.com/BurdetteLamar/RubyTest/blob/master/examples/github/TesterTour.md#tester-tour)。

从我的[博客](https://burdettelamar.wordpress.com/)交叉发布。