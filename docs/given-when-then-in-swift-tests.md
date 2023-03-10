# 在 Swift 测试中给出

> 原文：<https://dev.to/onmyway133/given-when-then-in-swift-tests>

### 规格

使用像 [Quick](https://github.com/Quick/Quick) 这样的`spec`测试框架是不错的，这使得`BDD`风格成为可能。

```
describe("the 'Documentation' directory") {
  it("has everything you need to get started") {
    let sections = Directory("Documentation").sections
    expect(sections).to(contain("Organized Tests with Quick Examples and Example Groups"))
    expect(sections).to(contain("Installing Quick"))
  }

  context("if it doesn't have what you're looking for") {
    it("needs to be updated") {
      let you = You(awesome: true)
      expect{you.submittedAnIssue}.toEventually(beTruthy())
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是万一你不想要额外的框架，并且想尽可能住得离苹果 SDK 近一些，这里有几个小技巧。

### 命名

这是我非常喜欢的一本书《单元测试的艺术》。如果你不介意下划线，你可以遵循[unit of work _ state under test _ expected behavior](http://osherove.com/blog/2005/4/3/naming-standards-for-unit-tests.html)结构

```
func testSum_NegativeNumberAs1stParam_ExceptionThrown()
func testSum_NegativeNumberAs2ndParam_ExceptionThrown()
func testSum_simpleValues_Calculated() 
```

Enter fullscreen mode Exit fullscreen mode

### 当那时给予

这是从 BDD 来的，在[黄瓜](https://github.com/cucumber/cucumber/wiki/Given-When-Then)练了很多。你可以阅读更多关于 https://martinfowler.com/bliki/GivenWhenThen.html 的[。](https://martinfowler.com/bliki/GivenWhenThen.html)

首先，给`XCTestCase`
添加一些扩展

```
import XCTest

extension XCTestCase {
  func given(_ description: String, closure: () throws -> Void) throws {
    try closure()
  }

  func when(_ description: String, closure: () throws -> Void) throws {
    try closure()
  }

  func then(_ description: String, closure: () throws -> Void) throws {
    try closure()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，为了测试，只需按照`given when then`

```
func testRemoveObject() throws {
  try given("set to storage") {
    try storage.setObject(testObject, forKey: key)
  }

  try when("remove object from storage") {
    try storage.removeObject(forKey: key)
  }

  try then("there is no object in memory") {
    let memoryObject = try? storage.memoryCache.object(forKey: key) as User
    XCTAssertNil(memoryObject)
  }

  try then("there is no object on disk") {
    let diskObject = try? storage.diskCache.object(forKey: key) as User
    XCTAssertNil(diskObject)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这个比评论更有意思。所有都是代码和描述性的。还可以进一步开发抛出`description`文本。

支持我的应用程序

*   [推送 Hero -测试推送通知的纯 Swift 原生 macOS 应用](https://onmyway133.com/pushhero)
*   [PastePal -粘贴板、便笺和快捷方式管理器](https://onmyway133.com/pastepal)
*   [快速检查-智能待办事项管理器](https://onmyway133.com/quickcheck)
*   [Alias - App 和文件快捷方式管理器](https://onmyway133.com/alias)
*   [我的其他应用](https://onmyway133.com/apps/)

❤️❤️😇😍🤘❤️❤️