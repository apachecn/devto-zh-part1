# 为什么代码覆盖率不是一个可靠的度量标准

> 原文：<https://dev.to/conectionist/why-code-coverage-is-not-a-reliable-metric-327l>

我经常听到类似“让我们增加代码覆盖率”或“我们的代码覆盖率是多少？”(大多来自管理者)。我对自己说“哦，天啊……”。

不是因为我不喜欢单元测试或者觉得单元测试没用。恰恰相反。我认为单元测试非常重要和有用。

我反对的是使用代码覆盖率作为衡量标准。因为它可能毫无意义。通常不会。

# 让我解释一下为什么

考虑下面这个验证电子邮件地址的类:

```
class EmailValidator
{
    public bool ValidateEmail(string email)
    {
        // just for demo purposes
        // I know it's not a very good email regex :P
        return new RegEx("[a-zA-Z0-9_]+[@][a-z]+[.]com").matches(email);
    }
}

```

和一个用于电子邮件验证器单元测试的类:

```
class EmailValidatorTest
{
    void TestValidEmail()
    {
        Assert.IsTrue(EmailValidator.ValidateEmail("someone@email.com"));
    }
}

```

# 我的代码覆盖率是多少？

我只有一个类和一个方法。我的单元测试遍历了这段代码。这意味着我的整个“代码库”都被覆盖了。耶！100%覆盖！那意味着我的代码是防弹的，对吗？**错了！**

为什么？因为我的正则表达式虽然不是很复杂，但是涵盖了很多场景。我只写了一个小字母的单元测试。
我还没有为包含以下内容的电子邮件编写过单元测试:

*   空字符串
*   大写字母
*   数字
*   强调
*   字母数字字符
*   以上的组合(还有很多)

那么，我的*真实*覆盖率是多少？我认为不到 15%。

# 现实生活呢？

我并不是说代码覆盖率总是不可靠。但是它只对简单的情况起预期的作用。比如下面:

```
bool IsEven(int n)
{
    if(n%2 == 0)
        return true;
    else
        return false;
}

```

这段代码的两个单元测试将给出 100%的覆盖率，这实际上是你需要的所有单元测试。

但是现实生活中没有这样的代码。根据我的经验，任何代码库中只有不到 5%的代码像这样简单。

大多数时候，你使用第三方库，它们有类/函数，就像我上面使用的 regex。尽管我只写了一行，但在那后面隐藏着一堆 if/else 语句。

# 结论

我不同意的不是代码覆盖率本身。这实际上有助于开发人员可视化他/她根本没有编写任何单元测试的区域。

问题是大多数人不明白
**高代码覆盖率≠代码可靠性**。

您可以拥有超过 95%的代码覆盖率，并且您的应用程序可能存在不可接受的错误(实际上我亲眼目睹了这一点)。