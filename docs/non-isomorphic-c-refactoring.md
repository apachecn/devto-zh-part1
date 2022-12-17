# 非同构 C++重构

> 原文：<https://dev.to/dmerejkowsky/non-isomorphic-c-refactoring>

*原发表于我的[博客](https://dmerej.info/blog/post/non-isomorphic-cpp-refactoring/)* 。

# 简介

让我告诉你一个基于真实事件的 C++重构的故事。

这个例子有点做作，但是代码实际上非常接近它看起来的样子。

# 问题

假设您正在编写一个 C++程序来安全地存储重要文档。

你有一个用密码和钥匙打开的精致保险箱。在插入钥匙之前，你必须输入密码*，但是你一关门，保险箱就会自动锁上。(这可能不完全是保险箱的工作方式，但是对于我们的例子来说很方便)。*

你已经写好了`main()`函数，比如:

```
int main() {
  Safe safe;
  Document passport("passport");
  Document idCard("ID card");
  std::vector<Document> documents{passport, idCard};

  safelyStoreDocuments(safe, documents);
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

你的工作是编写`safelyStoreDocuments`函数。

# 用 RAII 为门

您刚刚了解了 RAII(资源获取是初始化)模式，因此您决定使用它。

关于 RAII 是什么的快速提醒:在 C++中，当退出创建实例的作用域时，保证调用类的析构函数。
它可以用来实现一个锁，就像这样:

```
class Lock {
  public:
  Lock(Mutex mutex): _mutex(mutex) {
    _mutex.acquire();
  }
  ~Lock() {
    _mutex.release();
  }

  private:
  Mutex _mutex;

}:

void doSomething() {
  {
    Lock lock(mutex):
    updateCounter();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里你知道锁将在调用`updateCounter()`之前被获取，然后在调用之后被释放。

所以您决定为保险箱的门实现相同的模式:

```
class Door {
  public:
  void close() {
    // ...
  }

  ~Door() {
    close();
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这样你就可以开始实现`safelyStoreDocuments`函数:

```
void safelyStoreDocuments(Safe& safe, std::vector<Document> const& documents) {
  Door door;

  // TODO: open the door and store documents
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们如何使用一个安全*引用*，以确保安全没有被复制，并确保安全被初始化。

# 回忆组合

找到组合并不难，你只需要先认真思考一下:

```
void safelyStoreDocuments(Safe& safe, std::vector<Document> const& documents) {
  Door door;

  // Find and enter combination:
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();
  door.enterCombination(combination);

  // TODO: unlock the door
  // TODO: store documents
} 
```

Enter fullscreen mode Exit fullscreen mode

# 寻找钥匙

找到钥匙有点棘手。你必须遍历所有的口袋，找到包含钥匙的那个。

找到钥匙后，你所要做的就是用钥匙打开门，然后打开它:

您的代码现在看起来像:

```
Pocket findCorrectPocket() {
  for(auto pocket: pockets) {
    // ...
  }
}

void safelyStoreDocuments(Safe& safe, std::vector<Document>& documents) {
  Door door;

  // Find and enter combination:
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();
  door.enterCombination(combination);

  // Find the key and unlock the door
  auto correctPocket = findCorrectPocket();
  auto key = correctPocket.key();
  door.unlock(key);

  door.open();

  // TODO: store documents
} 
```

Enter fullscreen mode Exit fullscreen mode

# 存储文档

存储文件很容易:你只需要循环，检查保险箱里是否有足够的空间。

已经有一个您可以调用的`safe.isFull()`方法。

所以你终于能够实现 TODO:

```
void safelyStoreDocuments(Safe& safe, std::vector<Document>& documents) {
  Door door;

  // Find and enter combination:
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();
  door.enterCombination(combination);

  // Find the key and unlock the door
  auto correctPocket = findCorrectPocket();
  auto key = correctPocket.key();
  door.unlock(key);

  door.open();

  // Put documents in the safe:
  for(auto const& document: documents) {
    if(!safe.isFull()) {
      safe.putDocument(document);
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

对您的工作感到满意后，您编译并运行代码:

```
$  g++ -Wall safe.cpp -o safe && ./safe
Thinking really hard about combination ...
Entering combination
Looking for correct pocket ...
Unlock door
Opening door
Putting passport in safe
Putting ID card in safe
Closing door 
```

Enter fullscreen mode Exit fullscreen mode

看起来代码起作用了！

# 打扫卫生

满意之后，您提交您的更改进行代码评审。

你的一个同事指出了一种代码气味。该函数很长，带有解释性注释。最好将一些功能提取到更小的函数中。

你同意，重新写代码:

```
void openSafeDoor() {
  Door door;
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();
  door.enterCombination(combination);

  auto correctPocket = findCorrectPocket();
  auto key = correctPocket.key();
  door.unlock(key);

  door.open();
}

void putDocumentsIntoSafe(Safe& safe, std::vector<Document> const& documents) {
  for(auto document: documents) {
    if(!safe.isFull()) {
      safe.putDocument(document);
    }
  }
}

void safelyStoreDocuments(Safe& safe, std::vector<Document> const& documents) {
  openSafeDoor();
  putDocumentsIntoSafe(safe, documents);
} 
```

Enter fullscreen mode Exit fullscreen mode

发现错误了吗？

我会让你思考几分钟，同时欣赏这个
动画图形乐谱:

# 认错了

当我们将函数一分为二时，我们在`openSafeDoor()`函数中移动了门类的实例化，这意味着当我们试图将文档放入保险箱时，门将被*关闭*！

```
$  g++ -Wall safe.cpp -o safe && ./safe
Unlock door
Opening door
Closing door
Putting passport in safe
Putting ID card in safe 
```

Enter fullscreen mode Exit fullscreen mode

# 怎么会这样

这是我在编写 bug 修复补丁时经常问自己的一个问题。

为什么会出现 bug，我们能做些什么让它不再发生？

我发现，简单地问这个问题通常会带来有趣的发现，有时会引发过程、工具或重构习惯的改变。

在我们的例子中，我认为主要的问题是我们在保险箱和门之间有一个隐含的依赖关系。

# 更好的重构

首先，让我们将 Door 类移到 Safe 类中，并且只转发调用:

```
class Safe() {
  public:
  Safe(): door_(Door()) {}

  void enterDoorCombination(std::string const& combination) {
    door_.enterCombination(combination);
  }

  void unlockDoor(Key key) {
    door_.unlock(key);
  }

  void openDoor() {
    door_.open();
  }

  private:
  Door door_;
}; 
```

Enter fullscreen mode Exit fullscreen mode

代码现在变成:

```
void openSafeDoor(Safe& safe) {
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();
  safe.enterDoorCombination(combination);

  auto correctPocket = findCorrectPocket();
  auto key = correctPocket.key();
  safe.unlockDoor(key);

  safe.openDoor();
}

void safelyStoreDocuments(Safe& safe, std::vector<Document> const& documents) {
  openSafeDoor(safe);

  putDocumentsIntoSafe(safe, documents);
} 
```

Enter fullscreen mode Exit fullscreen mode

问题就解决了。

但是我们可以做得更好！让我们为 safe 引入一个更通用的`open`方法:

```
class Safe() {
  // ...
  void open(std::string const& combination, const Key& key) {
    door_.enterCombination(combination);
    door_.unlock(key);
    door_.open();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

代码现在变成:

```
void openSafe(Safe& safe) {
  Brain brain;
  brain.thinkReallyHard();
  auto combination = brain.getCombination();

  auto correctPocket = findCorrectPocket();
  auto key = correctPocket.key();

  safe.open(combination, key);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意现在*不可能*在不输入密码的情况下尝试使用密钥，因为需要密码和密钥来调用`safe.open()`方法。

# 更好的界限

我认为密码的另一个问题是，它是通过考虑事件的顺序(输入密码，找到钥匙，开门)而被拆分的...)，但没有尝试引入有用的抽象。

看看我们如何移动代码，得到更具可读性的东西:

```
class Safe() {
  // ...
  void open() {
  // same as before
  }

  void putDocuments(std::vector<Document> const& documents) {
    for(auto const& document: documents) {
      if(! _full);
      putDocument(document);
    }
  }

  // ...
}

Key getKey() {
  auto correctPocket = findCorrectPocket();
  return correctPocket.key();
}

const std::string getCombination() {
  Brain brain;
  brain.thinkReallyHard();
  return brain.getCombination();
}

int main() {
  Document passport("passport");
  Document idCard("ID card");
  std::vector<Document> documents{passport, idCard};

  auto key = getKey();
  auto combination = getCombination();

  Safe safe;
  safe.open(combination, key);
  safe.putDocuments(documents);
} 
```

Enter fullscreen mode Exit fullscreen mode

什么变了？

首先，我们去掉了三个名字中带有“安全”的方法(`safelyStoreDocuments`、`openSafe`和`putDocumentsIntoSafe`)。

这些名字暗示了这些函数实际上属于`Safe`类。

`Safe`类本身已经成长，现在负责维护不变量(确保门打开和关闭，并且有足够的空间存放所有文档)。还要注意`_full`私有成员不再需要公开。

最后，`main()`现在只关心收集它需要的各种元素，并且只调用保险箱的“高级”方法。它不知道安全门是如何工作的，也不知道钥匙和密码来自哪里。

我认为这是一个更好的设计。

# 最后一件事

我们的重构将`Door`移到了`Safe`类中，现在门的生存期与其包含的对象相同。

但情况并非总是如此。例如，将文件存放在保险箱后，您可能想要清除堆积在上面的灰尘:

```
int main() {
  Safe safe;
  safe.open(combination, key);
  safe.putDocuments(documents);
  safe.dust();
} 
```

Enter fullscreen mode Exit fullscreen mode

```
$  g++ -Wall safe.cpp -o safe && ./safe
Putting passport in safe
Putting ID card in safe
Removing dust from the safe
Closing door 
```

Enter fullscreen mode Exit fullscreen mode

这里有点不对劲。在呼叫到`dust()`后*门关闭。你不想让管家看到保险箱里的东西，对吗？*

为了解决这个问题，我们可以完全移除`~Door`析构函数，并在`Safe`类中引入一个显式的`close()`方法:

```
class Safe {
  // ...
  void close() {
    if (_opened) {
      _door.close();
      _opened = false;
    }
  }

  // ..
  ~Safe() {
    close();
  }

  private:
  bool _opened;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将保险箱的状态存储在一个布尔成员中，这样我们就不会试图关门两次。

现在`main`变成了:

```
int main() {
  auto combination = getCombination();
  auto key = getKey();
  Safe safe;
  safe.open(combination, key);
  safe.putDocuments(documents);
  safe.close();
  safe.dust();
} 
```

Enter fullscreen mode Exit fullscreen mode

一切都很好。

这说明了一个有趣的问题。我们并不真正关心门的寿命(这只是一个实现细节)，我们真正关心的是*保险箱*的寿命，这就是为什么在门自己的
析构函数中关闭门是一个坏主意。

# 结论

面向对象编程很难，C++很难，命名东西很重要，考虑边界会产生更好的代码。

希望你喜欢我的故事，下次再见！

*感谢您阅读到目前为止:)*

我很想听听你的想法，所以请在下面留下你的评论，或者阅读[反馈页面](https://dmerej.info/blog/pages/feedback/)了解更多与我联系的方式。