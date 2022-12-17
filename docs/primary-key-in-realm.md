# 领域中的主键

> 原文：<https://dev.to/onmyway133/primary-key-in-realm>

原帖[https://github.com/onmyway133/blog/issues/4](https://github.com/onmyway133/blog/issues/4)

境界很棒。但是如果没有主键，就会复制记录，像[https://github.com/realm/realm-java/issues/2730](https://github.com/realm/realm-java/issues/2730)，[http://stack overflow . com/questions/32322460/should-I-define-the-primary-key-for-each-entity-in-realm](http://stackoverflow.com/questions/32322460/should-i-define-the-primary-key-for-each-entity-in-realm)，...因此，为了强迫我们自己养成声明主键的好习惯，我们可以利用 Swift 协议

像这样创建主要约束协议

```
protocol PrimaryKeyAware {
  var id: Int { get }
  static func primaryKey() -> String?
} 
```

Enter fullscreen mode Exit fullscreen mode

并使其符合外部领域对象

```
class Profile: Object, PrimaryKeyAware {

  dynamic var firstName: String = ""
  dynamic var lastName: String = ""
  dynamic var id: Int = 0

  override static func primaryKey() -> String? {
    return "id"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，当在 out `RealmStorage`中使用该对象时，我们可以肯定地说它有一个主键

```
class RealmStorage<T: Object> where T: PrimaryKeyAware {
  let realm: Realm

  init(realm: Realm = RealmProvider.realm()) {
    self.realm = realm
  }

  func save(_ objects: [T]) {
    try? realm.write {
      realm.add(objects, update: true)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

用法是这样的

```
let profile = Profile()
let storage = RealmStorage<Profile>()
storage.save([profile]) 
```

Enter fullscreen mode Exit fullscreen mode