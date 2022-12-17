# 雷诺柴

> 原文：<https://dev.to/horia141/raynor-chai-k31>

这篇文章是关于 raynor-chai 的，它是 JavaScript 断言库 chai 的一个漂亮的扩展/助手。它允许人们使用[雷诺](https://github.com/horia141/raynor)的方法来检查某些对象是否以你喜欢的方式运行。

使用它看起来像这样:

```
import * as chai from 'chai'
import { raynorChai } from 'raynor-chai'

class User {
    @MarshalWith(StringMarshaller)
    name: string;
    @MarshalWith(ArrayOf(IntegerMarshaller))
    scoresByDay: number[];

    totalScore(): number {
        return this.scoresByDay.reduce((a,b) => a + b, 0);
    }
}

chai.use(raynorChai);

const user = new User();
user.name = 'Raynor';
user.scoresByDay = [10, 20, 30];

chai.expect(user).to.be.raynor(new (MarshalFrom(User))()); // Assertion passes

const badUser = new User();
badUser.name = 'Raynor';
badUser.scoresByDay = [10, 20.5, 30];

chai.expect(badUSer).to.not.be.raynor(new (MarshalFrom(User))()); // Assertion passes 
```

Enter fullscreen mode Exit fullscreen mode

就像 raynor 一样，它应该超越从 typescript 获得的简单类型检查，而是关注对象的更深层次的属性。测试作为更大流程的一部分而构造的对象是否是它们应该是的方式特别有用。因此，如果你正在测试你的 REST API，你可以使用你在应用程序代码中使用的相同的实体定义来查看 API 是否做了它应该做的事情，并输出它应该输出的东西。

现在只有一个版本，但是我会随着我的使用和 Raynor 自身的发展来改进它。