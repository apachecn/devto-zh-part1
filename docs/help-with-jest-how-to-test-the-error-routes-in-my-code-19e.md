# Jest 帮助:如何测试我的代码中的错误路径？

> 原文：<https://dev.to/imthedeveloper/help-with-jest-how-to-test-the-error-routes-in-my-code-19e>

我的 ES6 用户类中有以下静态函数，它搜索给定字符串的用户。

```
 // Search for a user by their pNick, includes partial matching
        static getBypNick(pNick = '') {
            // Define our search criteria regex and normalise to lower case
            const userSearchRegex = new RegExp(`^${pNick.toLowerCase()}`, 'i')

            return new Promise((resolve, reject) => {

                // Search user collection for pNick using regex like search and return array of results
                userTable.find({
                    _pNick: userSearchRegex
                }).sort({
                    _pNick: 1
                }).exec(function (err, result) {

                    // If error reject
                    if (err) {
                        return reject(err)
                    }
                    const userArray = result.map((user) => {
                        return new User(
                            user._pNick,
                            user._firstName,
                            user._userName,
                            user._phoneNumber,
                            user._userID)
                    })
                    // Return user records if found
                    return resolve(userArray)
                })
            })
        } 
```

虽然我可以很容易地使用 Jest 测试成功路线，但我很难理解如何调用错误案例，尤其是在。函数中的 exec 方法来调用承诺中的拒绝路由。

我知道我可以使用各种 Jest 特性，比如模仿和强制实现/返回值，但我就是想不出这种情况下的最佳情况。后台使用的数据库是 NeDB，我很肯定我只需要强制。exec 部分返回一个错误，然后我应该在我的承诺中捕捉到这个错误。

我不打算测试底层的 NeDB 库，因为它有自己的测试成功执行，所以这真的是我自己的方法。

我的覆盖范围至此:
[![Code Coverage](img/feeaaadbebae1b5836e55ccf0d9505c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bEWjoDv9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/r3QbU.png)