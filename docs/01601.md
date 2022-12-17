# 自动化测试工程师和 OOP

> 原文：<https://dev.to/havryliuk/automation-test-engineers-and-oop-38g8>

我的测试工程师同事需要重新阅读 OOP 原则，理解它们的价值，并真正开始使用它们。例如，他们写道-

```
class Epic {
    Type field = new Type();
}

class Test1 extends Epic {
    @Test
    public void test() {
        assertNotNull(field);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是-

```
class Epic {
}

class Test1 extends Epic {
    private Type field = new Type();

    @Test
    public void test() {
        assertNotNull(field);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

他们的理由是:“当我们在这个 epic 下添加另一个测试并声明同一个字段时，我们将得到一个重复字段的声纳问题。”

我的结论是他们仍然不对。你只需要在你使用它的类中声明和初始化字段，而不是在一个超类中希望有一天有人会在另一个子类中使用它。