# 需要模拟数据库/测试清理(单元测试)方面的建议

> 原文：<https://dev.to/jjjjcccjjf/need-recommendations-in-mocking-database--testing-cleanup-unit-testing-28eb>

大家好！刚开始学写测试。我用的是快递，猫鼬，摩卡，柴，柴-http。

到目前为止，我只是为每次测试清除我的模型。

```
 before(function (done) {
    Users.remove({}, (err) => {
      console.error(err)
      done()
    })
  }) 
```

Enter fullscreen mode Exit fullscreen mode

我遇到过 Mockgoose，但是我很难实现它(或者说我没有尽力实现它)

你通常如何模拟数据库或者在测试中进行清理？

下面是我的 users.js 测试，只是为了给出更多的上下文。我正在使用 Chai-http 测试一个 API。

```
'use strict'

// Require the dev-dependencies
const chai = require('chai')
const chaiHttp = require('chai-http')
const app = require('../app')

const mongoose = require('mongoose')
const Users = mongoose.model('Users')

chai.should()

chai.use(chaiHttp)

describe('Users', () => {
  before(function (done) {
    Users.remove({}, (err) => {
      console.error(err)
      done()
    })
  })

  describe('/GET users', () => {
    it('it should GET all the users', (done) => {
      chai.request(app)
      .get('/v1/users')
      .end((err, res) => {
        if (err) { console.error(err) }
        res.should.have.status(200)
        res.body.should.be.a('array')
        // res.body.length.should.be.eql(0)
        done()
      })
    })
  })

  describe('/POST users/register', () => {
    it('it should register a user', (done) => {
      chai.request(app)
      .post('/v1/users')
      .send({ email: 'test@gmail.com', password: 'password', name: 'Jason Bourne' })
      .end((err, res) => {
        if (err) { console.error(err) }
        res.should.have.status(201)
        res.body.should.be.a('object')
        // res.body.length.should.be.eql(0)
        done()
      })
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

非常感谢！