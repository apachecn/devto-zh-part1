# NodeJS 中并发 HTTP 请求的同步

> 原文：<https://dev.to/orkon/synchronization-of-concurrent-http-requests-in-nodejs-43o3>

作为我上一篇文章“改进 HTTP API”的后续，我写了一个简单的 NodeJS 服务器，演示了如何实现并发请求的同步，这样业务逻辑的某些部分就不会被执行两次。

我使用了上一篇文章中的例子，一个支付 API，并编写了一个简单的服务器，它遵循 POST/PUT 模式来创建资源，但不能正确处理并发的 PUT 请求。首先，让我们看看基本的实现，然后，让我们扩展服务器来同步并发请求。

服务器有两个处理程序`POST /payments`和`PUT /payments/id`。

```
app.post('/payments', (req, res) => {
  const paymentId = nextPaymentId++;
  const context = `request(post) #${nextRequestId++}`;
  handle(() => createPayment(context, paymentId), res);
}); 
```

```
app.put('/payments/:id', (req, res) => {
  const context = `request(put) #${nextRequestId++}`;
  const paymentId = req.params.id;
  handle(() => conductPayment(context, paymentId), res);
}); 
```

两个处理程序都定义了包含请求 ID 的上下文变量。上下文对于将由同一请求产生的日志消息分组很有用。此外，`POST /payments`处理器生成一个新的支付 id。之后，两个处理程序都将执行委托给`handle`函数，后者调用正确的业务逻辑函数并处理 HTTP 响应。

`handle`功能也很简单。请注意，可以通过使用扩展的错误类来改进错误处理。这意味着业务函数要么返回一个要发送给客户端的对象，要么抛出一个错误:

```
async function handle(fn, res) {
  try {
    const result = await fn();
    if (result) return res.status(200).json(result);
    res.status(204).end();
  } catch (err) {
    res.status(409).json({
      error: err.message,
    });
  }
} 
```

现在让我们检查一下业务逻辑。`createPayment`函数的作用仅仅是存储支付 id，并指出这是一个空的 id。`conductPayment`比`createPayment`更复杂:

```
async function conductPayment(context, paymentId) {
  const payment = await getPayment(context, paymentId);
  if (!payment) {
    throw new Error('Payment does not exist');
  }
  if (payment.state === 'PROCESSING') {
    throw new Error('Payment is in progress. Try again later.');
  }
  if (payment.state === 'PAID') {
    return payment;
  }
  if (payment.state === 'EMPTY') {
    await processPayment(context, paymentId);
  }
  throw new Error('Payment is in bad state');
} 
```

该函数首先检索付款对象，然后检查付款的状态。如果付款没有支付，并且此刻没有被处理，那么该函数调用`processPayment`方法。由于在现实世界中这是一个漫长的操作，并且通常涉及到调用第三方服务，因此可能需要一段时间。我已经用`setTimeout`模拟过了。执行`processPayment`大约需要 3 秒钟。

让我们总结一下目前服务器的能力:

1.  它可以处理并发的`POST /payments`请求。存储在数据库中的空支付没有外部副作用，我们可以在以后清理它们。

2.  它只能处理相同 ID 的连续`PUT /payments/id`请求。

第二点乍一看可能不明显，但是如果我们检查`conductPayment`函数的代码，我们会注意到在`await getPayment`和`await processPayment`之间有一个时间间隔。在这两次调用之间，一个并发请求可以到达并读取相同的付款状态。因此，并发请求可以启动并行(和重复)的付款流程。

## 同步并发 HTTP 请求

为了避免并发请求的问题，我们只需要确保在`await getPayment`和`await processPayment`之间的代码段中存在另一个请求时，不会启动对同一支付 id 的请求。有几种方法可以实现:
1) **排队**。我们可以确保服务器不会立即执行`conductPayment`功能，而是将消息放入特定于相应支付的队列中。另一个进程(worker)将一次获取一个支付 ID 的消息，这样就消除了并发执行的问题。这种方法是一个很好的可伸缩的解决方案，但有一个缺点:它使架构更加复杂，需要管理几个流程和维护一个消息代理。
2) **锁定**。我们可以利用乐观或悲观的锁定策略。使用悲观锁定，我们可以使用数据库或其他东西，例如 Redis，来确保当有另一个请求正在进行时，没有并发请求可以进入`conductPayment`。使用乐观锁定，我们可以在尝试将其状态更改为正在处理时(以原子方式)检查支付状态是否仍然为空。如果失败，我们可能会抛出一个错误，不发送付款给第三方。

因为这不是一篇关于锁定或排队的文章，所以我展示了悲观锁定策略在代码中的样子。

```
app.put('/payments/:id', (req, res) => {
  const context = `request(put) #${nextRequestId++}`;
  const paymentId = req.params.id;
  handleWithLock(() => conductPayment(context, paymentId), res);
}); 
```

这里，`handleWithLock`函数与`handle`完全一样，但是它确保一次只能运行一个业务逻辑实例。这是如何实现它的:

```
async function handleWithLock(context, lockId, fn, res) {
  try {
    const lockState = await lock(context, lockId); // per paymentId
    if (lockState === 'locked') throw new Error('Resource is locked.');
    const result = await fn();
    if (result) {
      return res.status(200).json(result);
    }
    res.status(204).end();
  } catch (err) {
    res.status(409).json({
      error: err.message,
    });
  } finally {
    await unlock(context, lockId);
  }
} 
```

重要的是`lock`函数是这样的，只有一个进程可以获得锁。此外，如果节点进程崩溃(或者锁在一段时间后过期)，释放锁也是至关重要的。在这个简单的例子中，我实现了基本的内存锁。**对于生产就绪的实现**来说，可以使用类似于 [PostgreSQL 顾问锁](https://www.postgresql.org/docs/9.6/static/explicit-locking.html)或[红锁]((https://redis.io/topics/distlock))的东西。一旦处理完成，使用`unlock`功能解除锁定。

在这个实现中，如果资源被锁定，`handleWithLock`函数会抛出一个错误。或者，服务器可以使用[自旋锁](https://en.wikipedia.org/wiki/Spinlock)等待资源再次空闲。下面你可以看到运行中的简单服务器。

[![https://60devs.com/img/article-synchronization-of-http-requests-in-NodeJS/concurrent-requests.gif](img/745fb3bc96e7f0e8c0bc01ad5b26ecbc.png "First PUT request is aborted, client re-tries multiple times")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--JwF-7VPN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://60devs.com/img/article-synchronization-of-http-requests-in-NodeJS/concurrent-requests.gif)

完整的服务器代码可以在这里找到:[要点](https://gist.github.com/OrKoN/a638d88d4e03c0e12953fdc666a1d3e2)。

如果我错过了一些实现同步的方法，或者你发现了代码中的错误，请告诉我，并在 Twitter 上关注我。

*最初发表于[60 devs](https://60devs.com/synchronization-of-concurrent-http-requests-in-node.html)T3【我的博客。*