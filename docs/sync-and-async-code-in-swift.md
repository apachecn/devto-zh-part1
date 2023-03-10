# Swift 中的同步和异步代码

> 原文：<https://dev.to/onmyway133/sync-and-async-code-in-swift>

原帖[https://github.com/onmyway133/blog/issues/75](https://github.com/onmyway133/blog/issues/75)

我们应该使用`DispatchQueue`来构建线程安全代码。这个想法是为了防止两个`read and write`从两个不同的线程中同时发生，这会导致数据损坏和意外行为。注意，你应该尽量避免死锁[https://stack overflow . com/questions/15381209/how-do-I-create-a-deadlock-in-grand-central-dispatch](https://stackoverflow.com/questions/15381209/how-do-i-create-a-deadlock-in-grand-central-dispatch)

## 全部同步

与`serial queue`一起使用`try catch`。使用`sync`功能阻塞当前队列。

```
func getUser(id: String) throws -> User {
  var user: User!
  try serialQueue.sync {
    user = try storage.getUser(id)
  }

  return user
}

func setUser(_ user: User) throws {
  try serialQueue.sync {
    try storage.setUser(user)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 全异步

使用`Result`，与`serial queue`一起。使用`async`功能返回当前队列。

```
enum Result<T> {
  case value(T)
  case error(Error)
}

func getUser(id: String, completion: (Result<User>) - Void) {
  try serialQueue.async {
    do {
      user = try storage.getUser(id)
      completion(.value(user))
    } catch {
      completion(.error(error))
    }
  }

  return user
}

func setUser(_ user: User, completion: (Result<()>) -> Void) {
  try serialQueue.async {
    do {
      try storage.setUser(user)
      completion(.value(())
    } catch {
      completion(.error(error))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 同步读取，异步写入

使用`try catch`读，`Result`写，连同`concurrent queue`。使用`sync`函数读阻塞当前线程，使用`async`函数带`barrier`标志写返回当前队列。这有利于在没有写操作的情况下优先选择多次读操作。当带有`barrier`的写入进入队列时，其他操作必须等待。

```
func getUser(id: String) throws -> User {
  var user: User!
  try concurrentQueue.sync {
    user = try storage.getUser(id)
  }

  return user
}

func setUser(_ user: User, completion: (Result<()>) -> Void) {
  try concurrentQueue.async(flags: .barrier) {
    do {
      try storage.setUser(user)
      completion(.value(())
    } catch {
      completion(.error(error))
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 异步测试

之前，我们可以使用 [dispatch_apply](https://developer.apple.com/documentation/dispatch/1453050-dispatch_apply) 向调度队列提交一个块以进行多次调用。从 Swift 开始，等价于[并发执行](https://developer.apple.com/documentation/dispatch/dispatchqueue/2016088-concurrentperform)T4】

```
DispatchQueue.concurrentPerform(iterations: 1000) { index in
    let last = array.last ?? 0
    array.append(last + 1)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 引用

*   [http://basememara.com/creating-thread-safe-arrays-in-swift/](http://basememara.com/creating-thread-safe-arrays-in-swift/)
*   [https://oleb . net/blog/2013/07/parallelize-for-loops-gcd-dispatch _ apply/](https://oleb.net/blog/2013/07/parallelize-for-loops-gcd-dispatch_apply/)