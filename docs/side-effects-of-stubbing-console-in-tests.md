# 测试中敲击控制台的副作用

> 原文：<https://dev.to/gyandeeps/side-effects-of-stubbing-console-in-tests>

我们都喜欢在单元测试中剔除对象，因为这让我们在编写测试时更加轻松。您可以存根由您的源代码创建的对象或我们的环境特定的对象，如节点或浏览器。在 [ESLint](https://github.com/eslint/eslint) 项目中，我们删除了`console`对象，这样我们可以看到测试运行的清晰输出。但是后来我们发现，如果测试有一些错误，那么 cmd 上的测试框架不会显示出来，因为我们已经完全清除了控制台对象。

为了更好地理解 stubbing，请参考 Sinon.js 的[以利亚庄园](https://twitter.com/elijahmanor) - [单元测试像特工一样的优秀文章。](http://elijahmanor.com/unit-test-like-a-secret-agent-with-sinon-js/)

让我们来看一些场景，看看当你模拟控制台和不模拟控制台时会发生什么。出于演示目的，我们将使用以下库 <sup id="fnref:1">[1](#fn:1)</sup> :

1.  [摩卡](https://mochajs.org/)
2.  [否则。js〔t1〕](http://sinonjs.org/)
3.  [柴断言](http://chaijs.com/api/assert/)
4.  [代理查询](https://github.com/thlorenz/proxyquire)

## 场景

#### 测试通过且控制台未被嘲讽

没有嘲笑任何东西，如果我们运行一个所有测试都通过的样本测试。

```
var assert = require("chai").assert;
var sinon = require("sinon");

describe("validation", function(){
    it("Expect it to be true", function(){
        assert.isTrue(true);
    });

    it("Expect it to be false", function(){
        assert.isFalse(false);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是

```
$ ./node_modules/.bin/mocha test.js

  validation
    √ Expect it to be true
    √ Expect it to be false

  2 passing (6ms) 
```

Enter fullscreen mode Exit fullscreen mode

一切正常，mocha 能够轻松显示测试的输出。

#### 测试失败，控制台未被嘲笑

没有嘲笑任何东西，如果我们运行一个样本测试，其中一个测试失败了。

```
var assert = require("chai").assert;
var sinon = require("sinon");

describe("validation", function(){
    it("Expect it to be true", function(){
        assert.isTrue(false);
    });

    it("Expect it to be false", function(){
        assert.isFalse(false);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是

```
$ ./node_modules/.bin/mocha test.js

  validation
    1) Expect it to be true
    √ Expect it to be false

  1 passing (8ms)
  1 failing

  1) validation Expect it to be true:
     AssertionError: expected false to be true
      stack trace... 
```

Enter fullscreen mode Exit fullscreen mode

一切都按预期运行，mocha 能够很容易地显示测试失败的输出和错误信息的堆栈跟踪。

#### 测试失败，控制台被嘲笑

我们将在`console`对象上模拟一些常见的方法，然后进行一些失败的测试。

```
var assert = require("chai").assert;
var sinon = require("sinon");

describe("validation", function(){
    beforeEach(function(){
        this.cStub1 = sinon.stub(console, "info");
        this.cStub2 = sinon.stub(console, "log");
        this.cStub3 = sinon.stub(console, "error");
        this.cStub4 = sinon.stub(console, "trace");
    });
    afterEach(function(){
        this.cStub1.restore();
        this.cStub2.restore();
        this.cStub3.restore();
        this.cStub4.restore();
    });

    it("Expect it to be true", function(){
        assert.isTrue(false);
    });

    it("Expect it to be false", function(){
        assert.isFalse(false);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是

```
$ ./node_modules/.bin/mocha test.js

  validation

  1 passing (13ms)
  1 failing

  1) validation Expect it to be true:
     AssertionError: expected false to be true
      stack trace... 
```

Enter fullscreen mode Exit fullscreen mode

一切正常，mocha 能够轻松显示测试失败的输出。但是如果你仔细观察，就会发现单独的测试运行检查并没有打印出来。这是因为您的测试删除了控制台对象。因为这是一个非常简单的例子，这就是为什么损害最小，但它可以导致更多更大的问题。

#### 最坏的情况

当你在测试中(在`it`中)而不是在`beforeEach`中进行所有的模仿时。然后，如果测试失败，存根永远不会恢复自己。

```
var assert = require("chai").assert;
var sinon = require("sinon");

describe("validation", function(){
    it("Expect it to be true", function(){
        this.cStub1 = sinon.stub(console, "info");
        this.cStub2 = sinon.stub(console, "log");
        this.cStub3 = sinon.stub(console, "error");
        this.cStub4 = sinon.stub(console, "trace");

        assert.isTrue(false);

        this.cStub1.restore();
        this.cStub2.restore();
        this.cStub3.restore();
        this.cStub4.restore();
    });

    it("Expect it to be false", function(){
        assert.isFalse(false);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是

```
$ ./node_modules/.bin/mocha test.js

  validation 
```

Enter fullscreen mode Exit fullscreen mode

这里什么也没有显示，因为`console`从未恢复到原始状态。如果你有很多测试，那么这可能会导致潜在的未被发现的问题。在这种情况下，调试是一场噩梦。

## 解

处理这种情况的最好方法是将日志逻辑提取到它自己的模块中，然后将该模块插入到您的测试中。

创建要消费的`logging.js`模块 <sup id="fnref:2">[2](#fn:2)</sup> 。

```
module.exports = {
    info: function() {
        console.log.apply(console, Array.prototype.slice.call(arguments));
    },

    error: function() {
        console.error.apply(console, Array.prototype.slice.call(arguments));
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在你的`app.js`模块中使用上面创建的`logging`模块。

```
var log = require("./logging.js");

module.exports = {
    sayHi: function(name){
        log.info("Hi " + name);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

因此，当你为`app`模块编写单元测试时，你实际上可以存根`logging`模块。

```
var assert = require("chai").assert;
var sinon = require("sinon");
var proxyquire = require("proxyquire");

proxyquire = proxyquire.noCallThru().noPreserveCache();

describe("validation", function(){
    var log = {
        info: sinon.spy(),
        error: sinon.spy()
    };

    it("Expect it to be true", function(){
        var app = proxyquire("../lib/app", {
            "./logging": log
        });
        app.sayHi("test");
        assert.isTrue(log.info.calledOnce);
    });

    it("Expect it to be false", function(){
        assert.isFalse(false);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

永远避免磕磕绊绊，因为总有更好的方法来实现你想要做的事情。有时像这样的争吵会导致你不知道有问题，因为他们都被忽视了。

##### 参考文献

1.  有关所用库的列表，请参考文章的顶部。↩

2.  一些代码示例取自于 [ESLint](http://eslint.org/) 库。↩

*本帖最初发表于[gyandeeps.com](https://gyandeeps.com/console-stubbing/)T3】*