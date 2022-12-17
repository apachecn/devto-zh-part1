# 境界构成

> 原文：<https://dev.to/onmyway133/composition-in-realm>

原帖[https://github.com/onmyway133/blog/issues/13](https://github.com/onmyway133/blog/issues/13)

有时候我们有一些具有某种继承性的模型，比如`Dog`、`Cat`、`Mouse`可以是`Animal`。我们可以使用组合来模仿继承，我们只需要确保它有唯一的主键

## 猫和狗

这些都是非常基本的领域对象

```
class Dog: Object {
  dynamic var id: Int = 0

  required convenience init(json: [String: Any]) {
    self.init()
    id <- json.integer(key: "id")
  }
}

class Cat: Object {
  dynamic var id: Int = 0

  required convenience init(json: [String: Any]) {
    self.init()
    id <- json.integer(key: "id")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 动物

这里的`Animal`既可以包含`dog`也可以包含`cat`，如果`Animal`还有很多其他的“继承”，我们可以添加更多。`required init`确保动物只能初始化 1 种`child`职业

```
class Animal: Object {
  dynamic var id: Int = 0

  dynamic var cat: Cat?
  dynamic var dog: Dog?

  required convenience init(cat: Cat) {
    self.init()
    self.cat = cat
    self.id = cat.id
  }

  required convenience init(dog: Dog) {
    self.init()
    self.dog = dog
    self.id = dog.id
  }
} 
```

Enter fullscreen mode Exit fullscreen mode