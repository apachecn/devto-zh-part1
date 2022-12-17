# 确保使用所有诺克拦截器

> 原文：<https://dev.to/neverendingqs/ensure-all-nock-interceptors-are-used-2eff>

nock 是一个模拟 HTTP 调用的 Node.js 测试库，允许测试发出 HTTP 请求的代码，而不需要实际发出任何网络调用。然而，HTTP 模拟是在全局级别应用的，这可能会导致意外和不希望的行为。

# 总结

确保没有测试留下未使用的 nock 拦截器，以防止一个测试影响另一个测试的状态，并通过在每个测试后运行以下内容来保持主动:

```
// Example for mocha; translate as needed for your favourite test framework
afterEach(function() {
  ...
  if(!nock.isDone()) {
    this.test.error(new Error('Not all nock interceptors were used!'));
    nock.cleanAll();
  }
  ...
}); 
```

# 一个简单的诺克例子

考虑下面的例子(使用[摩卡](https://mochajs.org/)和[柴](http://chaijs.com/)):

```
'use strict';
const request = require('superagent');

const express = require('express');
const app = express();

app.get('/', function(req, res) {
  request
    .get('https://www.example.com')
    .then(response => res.send(response.text));
});

app.listen(3000);
module.exports = app; 
```

为了测试这一点，我可以像这样使用 nock:

```
'use strict';
const assert = require('chai').assert;
const nock = require('nock');
const request = require('supertest');
const uniqueValue = 'da64daaf-182b-4af6-a4af-09727bf8d5aa';

const app = require('../../src/server');

describe('server', function() {
  it('GET / once', function() {
    nock('https://www.example.com')
      .get('/')
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });
}); 
```

对于每个`nock()`调用，我们为一个特定的请求创建一个拦截器。当发出请求时，拦截器根据它的设置做出响应。在这种情况下，它将返回`uniqueValue`，我用它来断言输出。这使得测试无法进行真正的网络调用，并允许我们测试代码内部的逻辑。

# 诺克截击机是一次性的

需要注意的是，一旦使用了 nock 拦截器，它就会从拦截器列表中删除，并且不再拦截那个特定的请求。对于下面的例子，记住这一点很重要。

例如，假设我想测试基于五次相同调用的行为(也许我想测试一个缓存)。因为`server.js`中的代码将调用`https://www.example.com`五次，所以我必须设置五个拦截器:

```
'use strict';
const assert = require('chai').assert;
const nock = require('nock');
const request = require('supertest');
const uniqueValue = 'da64daaf-182b-4af6-a4af-09727bf8d5aa';

const app = require('../../src/server');

describe('server', function() {
  it('GET / once', function() {
    nock('https://www.example.com')
      .get('/')
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });

  it('GET / five times', function() {
    nock('https://www.example.com')
      .get('/')
      .times(5) // sets up 5 interceptors
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });
}); 
```

注意到这个 bug 了吗？我创造了五个诺克拦截器，但只用了一个！然而，由于我们实际上没有验证所有五个拦截器的使用，两个测试仍然会通过。

# 未使用的拦截器导致意外错误

假设我们现在想要在`https://www.example.com`关闭时提供一个好的错误。我可以这样更新`server.js`文件:

```
'use strict';
const request = require('superagent');

const express = require('express');
const app = express();

app.get('/', function(req, res) {
  request
    .get('https://www.example.com')
    .then(response => res.send(response.text))
    // add a catch here and return a custom message
    .catch(response => res.status(503).send('Please try again later.'));
});

app.listen(3000);
module.exports = app; 
```

我们还应该为这个新功能添加一个新的测试:

```
'use strict';
const assert = require('chai').assert;
const nock = require('nock');
const request = require('supertest');
const uniqueValue = 'da64daaf-182b-4af6-a4af-09727bf8d5aa';

const app = require('../../src/server');

describe('server', function() {
  it('GET / once', function() {
    nock('https://www.example.com')
      .get('/')
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });

  it('GET / five times', function() {
    nock('https://www.example.com')
      .get('/')
      .times(5) // sets up 5 interceptors
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });

  it('GET / fails gracefully if example.com is down', function() {
      nock('https://www.example.com')
        .get('/')
        .reply(500);

      return request(app)
        .get('/')
        .then(res => assert.equal(res.status, 503));
    });
}); 
```

这就是剩下的四个拦截器开始产生问题的地方:

```
server
    √ GET / once (46ms)
    √ GET / five times
    1) GET / fails gracefully if example.com is down

  2 passing (76ms)
  1 failing

  1) server GET / fails gracefully if example.com is down:
     AssertionError: expected 200 to equal 503
      at test/server.js:38:29 
```

因为还剩下四个返回状态 200 的拦截器，所以新测试首先用完了其中一个拦截器，而不是我们在测试中设置的那个。然而，这个错误乍一看并不明显。

# 确保每次测试后没有拦截器留下

这里的解决方法是调用路由五次:

```
return Promise.all(
  Array(5).fill(0).map(i =>
    request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  )
); 
```

然而，为了更加主动，我们可以利用 nock 的[。isDone()](https://github.com/node-nock/nock#isdone) 和[。cleanAll()](https://github.com/node-nock/nock#cleanall) :

```
'use strict';
const assert = require('chai').assert;
const nock = require('nock');
const request = require('supertest');
const uniqueValue = 'da64daaf-182b-4af6-a4af-09727bf8d5aa';

const app = require('../../src/server');

describe('server', function() {
  afterEach(function() {
    if(!nock.isDone()) {
      this.test.error(new Error('Not all nock interceptors were used!'));
      nock.cleanAll();
    }
  })
  it('GET / once', function() {
    nock('https://www.example.com')
      .get('/')
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });

  it('GET / five times', function() {
    nock('https://www.example.com')
      .get('/')
      .times(5) // sets up 5 interceptors
      .reply(200, uniqueValue);

    return request(app)
      .get('/')
      .then(res => assert.equal(res.text, uniqueValue));
  });

  it('GET / fails gracefully if example.com is down', function() {
      nock('https://www.example.com')
        .get('/')
        .reply(500);

      return request(app)
        .get('/')
        .then(res => assert.equal(res.status, 503));
    });
}); 
```

这确保测试不会留下任何未使用的 nock 拦截器，同时防止一个测试的未使用的拦截器影响其他测试:

```
server
    √ GET / once (47ms)
    √ GET / five times
    1) GET / five times
    √ GET / fails gracefully if example.com is down

  3 passing (78ms)
  1 failing

  1) server GET / five times:
     Error: Not all nock interceptors were used!
      at Context.<anonymous> (test/server.js:12:23) 
```

# 源代码

在 [node-nock-example](https://github.com/neverendingqs-sandbox/node-nock-example) 随意摆弄源代码。