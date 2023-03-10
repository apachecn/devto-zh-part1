# 节点 js 中的异步迭代

> 原文：<https://dev.to/kwabenberko/async-iteration-in-nodejs-bm6>

Node.js 是一个令人惊叹的 JavaScript 运行时环境，毫无疑问。它的非阻塞 IO 模型使得用它构建的应用程序惊人地快速和高效。但是有时当执行异步迭代时，它的非阻塞特性会带来一些有趣的挑战。

假设我们有一个用户 id 数组，我们想遍历该数组并查询我们的用户集合(或表)以获得更多信息，如他们的名字、姓氏和电子邮件地址。最后，我们希望返回包含这些数据的另一个对象数组。

我们可以使用的一种方法如下:

```
 const getUserDetails = (userIds, callback) => {

    let userInfoArr = [];

    for(let i = 0; i < userIds.length; i++){
        User.findById(userIds[i], (err, user) => {
            if(err){
                return next(err);
            }

            userInfoArr.push({
                firstName: user.firstName,
                lastName: user.lastName,
                email: user.email
            });
        })
    }

    callback(userInfoArr);
}

getUserDetails(ids, (users) => {
    console.log(users);
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可能认为这可以工作，我们成功地获得了我们想要的用户数组，但是当我们将得到的用户数组记录到控制台时，我们得到的是一个空数组。这是为什么呢？

众所周知，node 是异步的:它不会无所事事地等待执行的结果，而是只在结果可用时才返回。所以我们得到一个空数组的原因是，在我们记录结果数组的时候，我们的代码执行还没有完成。

这意味着在返回结果之前，我们必须等待执行完成。对我有效的一种方法是引入一个新的计数器变量。在每次迭代中，计数器的值增加，然后检查它是否等于我们正在迭代的数组的长度。如果计数器等于数组的长度，那么我们假设执行已经完成，我们返回结果数组，就像这样:

```
 const getUserDetails = (userIds, callback) => {

    let userInfoArr = [];
    let counter = 0;

    for(let i = 0; i < userIds.length; i++){
        User.findById(userIds[i], (err, user) => {
            if(err){
                return next(err);
            }

            userInfoArr.push({
                firstName: user.firstName,
                lastName: user.lastName,
                email: user.email
            });

            counter++;

            if(counter == userIds.length){
                return callback(userInfoArr);
            }
        })
    }
}

getUserDetails(ids, (users) => {
    console.log(users);
}); 
```

Enter fullscreen mode Exit fullscreen mode

编码快乐！