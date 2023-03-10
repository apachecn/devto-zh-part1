# 迁移可编码对象

> 原文：<https://dev.to/onmyway133/migrating-codable-object-7e3>

原帖[https://github.com/onmyway133/blog/issues/83](https://github.com/onmyway133/blog/issues/83)

从 swift 4 迁移开始，我们更新了[缓存](https://github.com/hyperoslo/Cache)，以充分利用 Codable。它适用于大多数情况，因为我们通常应该将实体声明为类型化安全对象，而不是数组或 json 字典。并且通过符合`Codable`，很容易与 json 数据进行编码和解码。并且将它们坚持到`Cache`就像吃饼干一样简单。

前几天，我看到有人问如果模式改变如何迁移[https://github.com/hyperoslo/Cache/issues/153](https://github.com/hyperoslo/Cache/issues/153)，他喜欢`Realm`做[https://realm.io/docs/swift/latest/#migrations](https://realm.io/docs/swift/latest/#migrations)T5】的方式

```
 Realm.Configuration.defaultConfiguration = Realm.Configuration(
  schemaVersion: 1,
  migrationBlock: { migration, oldSchemaVersion in
      if (oldSchemaVersion < 1) {
          // The enumerateObjects(ofType:_:) method iterates
          // over every Person object stored in the Realm file
          migration.enumerateObjects(ofType: Person.className()) { oldObject, newObject in
              // combine name fields into a single field
              let firstName = oldObject!["firstName"] as! String
              let lastName = oldObject!["lastName"] as! String
              newObject!["fullName"] = "\(firstName)  \(lastName)"
          }
      }
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我想我们可以依靠`Codable`来迁移。仅供参考，这里是公关[https://github.com/hyperoslo/Cache/pull/154](https://github.com/hyperoslo/Cache/pull/154)

### 类名变更

我看`Codable`是基于 json 的，json 的重要性是它的数据结构，而不是类名。因此，如果您更改类名，它仍然有效。

首先，我们保存类型为`Person`的模型，稍后我们加载类型为`Alien`的模型。它之所以有效是因为结构保持不变

```
struct Person: Codable {
  let firstName: String
  let lastName: String
}

struct Alien: Codable {
  let firstName: String
  let lastName: String
}

let person = Person(firstName: "John", lastName: "Snow")
try! storage.setObject(person, forKey: "person")

// As long as it has same properties, it works too
let cachedObject = try! storage.object(ofType: Alien.self, forKey: "person")
XCTAssertEqual(cachedObject.firstName, "John") 
```

Enter fullscreen mode Exit fullscreen mode

### 属性改变

如果属性发生变化，那么您需要做一些迁移工作。

首先，我们保存类型为`Person1`的模型，它只有`fullName`。后来我们用一些新的属性把模型改成了`Person2`。为了进行迁移，我们需要首先用旧的`Person1`加载模型，然后基于这个`Person1`构建一个新的模型`Person2`。最后，用同一个密钥保存到`Cache`。

```
struct Person1: Codable {
  let fullName: String
}

struct Person2: Codable {
  let firstName: String
  let lastName: String
}

// Firstly, save object of type Person1
let person = Person1(fullName: "John Snow")

try! storage.setObject(person, forKey: "person")
XCTAssertNil(try? storage.object(ofType: Person2.self, forKey: "person"))

// Later, convert to Person2, do the migration, then overwrite
let tempPerson = try! storage.object(ofType: Person1.self, forKey: "person")
let parts = tempPerson.fullName.split(separator: " ")
let migratedPerson = Person2(firstName: String(parts[0]), lastName: String(parts[1]))
try! storage.setObject(migratedPerson, forKey: "person")

XCTAssertEqual(
  try! storage.object(ofType: Person2.self, forKey: "person").firstName,
  "John"
) 
```

Enter fullscreen mode Exit fullscreen mode