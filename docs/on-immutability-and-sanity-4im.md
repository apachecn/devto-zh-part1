# 论不变性和健全性

> 原文：<https://dev.to/damcosset/on-immutability-and-sanity-4im>

## 简介

这只是一篇分享我遇到的一个问题的小文章。我的任务是建立一个客户关系管理系统，交易必须按日期过滤。这一点都不花哨。您检查事务的日期是否在用户提供的另外两个日期(开始日期和结束日期)之间。

## 代码

嗯，这是在我设法解决它之前代码的样子。我对这段代码使用了 React。我使用流行的 javascript 库 *moment.js* 来处理日期。

```
 renderTransactions(){
    return this.props.transactions.filter( transaction => {
        const start = moment(this.state.startDate)
        const end = moment(this.state.endDate)
        const tDate = moment(transaction.date)

        return tDate.isBetween(start, end) // Compare dates here
    })
    .map( ( transaction, i ) => {
        transaction.date = moment(transaction.date).format('DD/MM/YYYY')
        return (
            <tr key={i}>
                <td>{transaction.amount}</td>
                <td>{transaction.date}</td>
                <td>{transaction.client}</td>
            </tr>
    })
}

// etc.. etc... 
```

Enter fullscreen mode Exit fullscreen mode

好的，你能看出问题出在哪里吗？组件第一次呈现时，一切正常，日期被正确过滤。但是，一旦我修改了日期，什么都不管用了...

问题就在这里:

`transaction.date = moment(transaction.date).format('DD/MM/YYYY')`

看到这有多蠢了吗？我用这一行改变了我的数组的状态。在下一次渲染时，*日期*字段不再是 moment 库可以处理的日期对象，而是一个字符串...

## 解决它，恢复我的理智...

#### 取其一:

不要直接使用 this.props。如果我在函数中使用对 this.props.transactions 的引用，就不会出现这个问题。每次运行该函数时，都会使用 this.props.transactions 的新副本。即使我保留了直接突变的有问题的行，它也能工作。

#### 取二:

**不变性。不要直接改变应用程序的状态。这一行本来可以解决一切问题:**

`const date = moment(transaction.date).format('DD/MM/YYYY')`

完成的...问题解决了。

## 结论

我写这些只是为了确保我不会再犯同样愚蠢的错误。我在这上面花了太多时间...我累了，我讨厌编程。