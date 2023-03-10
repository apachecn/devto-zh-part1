# 评论我的新手测试方法

> 原文：<https://dev.to/imthedeveloper/critique-my-newbie-approach-to-testing-381>

我刚刚开始了一个小爱好项目，制作一个电报机器人。我正在利用这个机会开始构建和测试。通常我会回避测试，所以是时候解决这个问题了。

也就是说，我很难理解几个要点，我已经包括了一个我正在测试的模块的例子以及测试用例。我已经决定使用 Jest，因为我已经很好地将它与 VSCode 集成在一起。

首先是一般性的问题:

1.  当谈到我应该测试什么时，我的测试似乎没有遵循太多的结构化方法。我的目标是检查快乐的路径和一些糟糕的情况。有人知道他们应该测试什么吗？例如，错误的输入、正确的输入、预期的输出？

2.  你想做什么深度的测试？您是简单地测试整个功能的工作情况，还是尝试隔离某个功能的特定元素？我读过关于“黑盒”测试和不关心实现的文章，但是你应该瞄准多详细/多低的级别呢？我可以很容易地测试公共接口并完成工作？

3.  如果我有一些没有导出的内部函数，我可能也想专门测试它们的功能。这是拒绝吗？

4.  “期望”描述的最佳约定是什么？在我的例子中，我刚刚添加了一个对我来说非常有意义的句子，但对另一个人来说可能不是。我应该选择“它应该基于 xxx 返回 xxx”还是其他一些好的框架描述？

模块如下:

```
const dbConnection = require('../Services/db/Db')
const config = require('../Config')
const USER_COLLECTION = config.envs[process.env.NODE_ENV].databaseConnection.DB_USER_COLLECTION

// Set database connection to use the User table
const userTable = dbConnection.collection(USER_COLLECTION)

class User {
    constructor(pNick = '', firstName = '', userName = '', phoneNumber = '', userIdentifier = 0) {
        this._pNick = pNick
        this._firstName = firstName
        this._userName = userName
        this._phoneNumber = phoneNumber
        this._userID = userIdentifier
        this._isAdmin = this.isAdmin()
    }

    static getByID(userID = 0) {
        return new Promise((resolve, reject) => {
            userTable.findOne({
                _userID: userID
            }, function (err, result) {

                // If error reject
                if (err) {
                    return reject(err)
                }

                // Return user records if found
                return resolve(result)
            })
        })
    }

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
            }).toArray(function (err, result) {

                // If error reject
                if (err) {
                    return reject(err)
                }

                // Return user records if found
                return resolve(result)
            })
        })
    }

    isAdmin() {
        if (this._userID === config.adminUser.ADMIN_ID) {
            return true
        }
        return false
    }

    isAuthenticated() {
        if (this._pNick.length > 1) {
            return true
        }
        return false
    }

}

module.exports = User 
```

Enter fullscreen mode Exit fullscreen mode

以下是我的测试:

```
const User = require('./User')
const user = new User()

test('Returns a new user with sensible default values', () => {
    expect(user).toBeTruthy();
    expect(user).toEqual({
        _firstName: "",
        _isAdmin: false,
        _pNick: "",
        _phoneNumber: "",
       _userID: 0,
       _userName:""
    })
})

test('Returns a new known user', () => {
    const admin = new User('munkee','Chris','munkeeusername','000',234234)
     const expectation =  {
        _pNick: 'munkee',
        _firstName: 'Chris',
        _userName: 'munkeeusername',
        _phoneNumber: '000',
        _userID: 234234,
        _isAdmin: false

     }
    expect(admin).toEqual(expectation)
    expect(admin._isAdmin).toBeFalsy()
})

test('Returns a new unknown user', () => {
    const admin = new User('','Chris','munkeeusername','',234234)
     const expectation =  {
        _pNick: '',
        _firstName: 'Chris',
        _userName: 'munkeeusername',
        _phoneNumber: '',
        _userID: 234234,
       _isAdmin: false

     }
    expect(admin).toEqual(expectation)
    expect(admin._isAdmin).toBeFalsy()
})

test('Returns a new admin user', () => {
    const admin = new User('munkee','Chris','munkeeusername','000',60178217)
     const expectation =  {
        _pNick: 'munkee',
        _firstName: 'Chris',
        _userName: 'munkeeusername',
        _phoneNumber: '000',
        _userID: 60178217,
       _isAdmin: true

     }
    expect(admin).toEqual(expectation)
    expect(admin._isAdmin).toBeTruthy()
})

test('Identify an authenticated user', () => {
    const authableUser = new User('munkee','Chris','munkeeusername','000',60178217)
    const expectation =  {
       _pNick: 'munkee',
       _firstName: 'Chris',
       _userName: 'munkeeusername',
       _phoneNumber: '000',
       _userID: 60178217,
      _isAdmin: true

    }
   expect(authableUser).toEqual(expectation)
   expect(authableUser.isAuthenticated()).toBeTruthy()
})

test('Identify a non authed user', () => {
    const nonAuthUser = new User('','Chris','munkeeusername','',234234)
     const expectation =  {
        _pNick: '',
        _firstName: 'Chris',
        _userName: 'munkeeusername',
        _phoneNumber: '',
        _userID: 234234,
       _isAdmin: false

     }
    expect(nonAuthUser).toEqual(expectation)
    expect(nonAuthUser.isAuthenticated()).toBeFalsy()
})

test('Returns no results when searching for a user with unknown ID', () => {
    expect.assertions(1)
    return User.getByID(2048098).then((result) => {
        expect(result).toBeFalsy()
    })
});

test('Returns no results when searching for an invalid ID type e.g. string', () => {
    expect.assertions(1)
    return User.getByID('string').then((result) => {
        expect(result).toBeFalsy()
    })
});

test('Returns user munkee results when searching with exact ID', () => {
    expect.assertions(2)
    return User.getByID(60178217).then((result) => {
        expect(result).toBeTruthy()
        expect(result._pNick).toEqual('munks')
    })
});

test('Returns the user munkee when searching via pnick', () => {
    expect.assertions(2)
    return User.getBypNick('munks').then((result) => {
        expect(result).toHaveLength(1)
        expect(result[0]._pNick).toEqual('munks')
    })
});

test('Finds the user munkee from partial pnick search', () => {
    expect.assertions(2)
    return User.getBypNick('mun').then((result) => {
        expect(result).toHaveLength(1)
        expect(result[0]._pNick).toEqual('munks');
    })
})

test('Returns an empty array when not finding a user', () => {
    expect.assertions(1)
    return User.getBypNick('xxxx').then((result) => {
        expect(result).toHaveLength(0);
    })
}); 
```

Enter fullscreen mode Exit fullscreen mode

残酷的诚实拜托:)