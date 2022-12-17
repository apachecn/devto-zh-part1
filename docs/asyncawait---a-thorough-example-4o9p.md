# async/await——一个完整的例子

> 原文：<https://dev.to/kbariotis/asyncawait---a-thorough-example-4o9p>

随着 Node.js 的第八(8)个版本成为 LTS，我认为这是一个考虑切换到它并享受令人敬畏的新 [async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 特性的好时机，它将帮助我们转移到更具可读性和同步性的流。在过去的 2 年左右的时间里，承诺为我们提供了很好的服务，但它们也带来了挫折。

在这篇文章中，我将尝试提供一个真实的例子，说明我们如何将基于承诺的 REST API 控制器转移到 async/await 风格。通过这种方式，我们可以更好地了解事情是如何变化的，以及这种变化的好处是什么。

## 一个基于承诺的例子

让我们举个例子，然后从这里开始。这是一个实际的控制器(为了这篇文章的目的稍微改变了一下)，来自我一直在做的一个项目:

```
const BPromise = require('bluebird');

const { WrongCredentialsError, DBConnectionError, EmailError } = require('./../errors');

/**
 * Emulate an Express.js route call as an example
 */
loginController({}, { json: response => console.log(response) }, null)

function loginController (req, res, err) {
  const { email, password } = req;

  let user;

  BPromise.try(() => validateUserInput(req))
    .then(() => fetchUserByEmail(email))
    .then(fetchedUser => user = fetchedUser)
    .then(() => comparePasswords(req.password, user.password))
    .then(() => markLoggedInTimestamp(user.userId))
    .then(() => sendEmail(user.userId))
    .then(() => generateJWT(user))
    .then(token => res.json({ success: true, token }))
    .catch(WrongCredentialsError, () => res.json({ success: false, error: 'Invalid email and/or password' }))
    .catch(EmailError, DBConnectionError, () => res.json({ success: false, error: 'Unexpected error, please try again' }))
    .catch(() => res.json({ success: false }))
}

/**
 * Validate input from Request
 *
 * @param {Object} input
 * @throws {WrongCredentialsError}
 * @returns {Void}
 */
function validateUserInput(input) {
  if (!input.email || !input.password) {
    throw new WrongCredentialsError();
  }
}

/**
 * Fetch a User from the DB by Email
 *
 * @throws WrongCredentialsError
 * @throws DBConnectionError
 * @returns {BPromise}
 */
function fetchUserByEmail(email) {
  const user = {
    userId: 'DUMMY_ID',
    email: 'konmpar@gmail.com',
    password: 'DUMMY_PASSWORD_HASH'
  }
  return new BPromise(resolve => resolve(user));
}

/**
 * Compare two password
 *
 * @param {String} inputPwd
 * @param {String} storedPwd
 * @throws {WrongCredentialsError}
 * @returns {Void}
 */
function comparePasswords(inputPwd, storedPwd) {
  if (hashPassword(inputPwd) !== storedPwd) {
    throw new WrongCredentialsError();
  }
}

/**
 * Hash password
 *
 * @param {String} password
 * @returns {String}
 */
function hashPassword(password) {
  return password;
}

/**
 * Mark a user's logged in timestamp
 *
 * @param {String} userId
 * @throws DBConnectionError
 * @returns {BPromise}
 */
function markLoggedInTimestamp(userId) {
  return new BPromise(resolve => resolve());
}

/**
 * Send a follow up email
 *
 * @param {String} userId
 * @throws EmailError
 * @returns {BPromise}
 */
function sendEmail(userId) {
  return new BPromise(resolve => resolve());
}

/**
 * Generate a JWT token to send to the client
 *
 * @param {Object} user
 * @returns {BPromise<String>}
 */
function generateJWT(user) {
  const token = 'DUMMY_JWT_TOKEN';

  return new BPromise(resolve => resolve(token));
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几个注意事项:

### 外部作用域变量

```
let user;

/* ... */
.then(fetchedUser => user = fetchedUser)
/* ... */
.then(() => sendEmail(user.userId))
/* ... */ 
```

Enter fullscreen mode Exit fullscreen mode

请注意我是如何在函数内部创建一个全局变量的，以便在我的承诺链中的各种调用中使用用户对象。一个可能的克服方法是让我的函数总是返回用户对象，但是这将 a)使我的函数毫无意义，b)将我的函数与这个特殊的承诺链紧密耦合，这样我就不能在其他地方使用它们。

### 以承诺开始承诺链

```
/* ... */
BPromise.try(() => validateUserInput(req))
/* ... */ 
```

Enter fullscreen mode Exit fullscreen mode

承诺链必须从承诺开始，但是`validateUserInput`函数不返回承诺。`Bluebird`去雷斯克。这样，我可以将我的函数包装在一个承诺调用中。当你告诉我这只是噪音时，我同意。

### 蓝鸟

我经常使用蓝鸟。这是因为如果没有它，我的代码会变得更加臃肿，到处都是承诺回报。蓝鸟很好地利用了干燥，所以我不必。我可以让我所有的函数，甚至那些不做异步的函数，返回一个承诺，但这意味着我必须“等待”它们，这意味着更多的噪音。

但是，蓝鸟只是另一个依赖，它可能会在下一个版本中破坏我的代码。我们不想那样。

## 异步/等待版本

现在让我们看看相同的代码，但是用 async/await 编写，并与上面的代码进行比较。

```
const { WrongCredentialsError, DBConnectionError, EmailError } = require('./../errors');

/**
 * Emulate an Express.js route call as an example
 */
loginController({}, { json: response => console.log(response) }, null)

/**
 *
 * @param {Object} req
 * @param {Object} res
 * @param {Object} err
 * @returns {Void}
 */
async function loginController(req, res, err) {
  const { email, password } = req.email;

  try {
    if (!email || !password) {
      throw new WrongCredentialsError();
    }

    const user = await fetchUserByEmail(email);

    if (user.password !== hashPassword(req.password)) {
      throw new WrongCredentialsError();
    }

    await markLoggedInTimestamp(user.userId);
    await sendEmail(user.userId);

    const token = await generateJWT(user);

    res.json({ success: true, token });

  } catch (err) {
    if (err instanceof WrongCredentialsError) {
      res.json({ success: false, error: 'Invalid email and/or password' })
    } else if (err instanceof DBConnectionError || err instanceof EmailError) {
      res.json({ success: false, error: 'Unexpected error, please try again' });
    } else {
      res.json({ success: false })
    }
  }
}

/**
 * Fetch a User from the DB by Email
 *
 * @throws WrongCredentialsError
 * @throws DBConnectionError
 * @returns {Promise}
 */
function fetchUserByEmail(email) {
  const user = {
    userId: 'DUMMY_ID',
    email: 'konmpar@gmail.com',
    password: 'DUMMY_PASSWORD_HASH'
  }
  return new Promise(resolve => resolve(user));
}

/**
 * Hash password
 *
 * @param {String} password
 * @returns {String}
 */
function hashPassword(password) {
  return password;
}

/**
 * Mark a user's logged in timestamp
 *
 * @param {String} userId
 * @throws DBConnectionError
 * @returns {Promise}
 */
function markLoggedInTimestamp(userId) {
  return new Promise(resolve => resolve());
}

/**
 * Send a follow up email
 *
 * @param {String} userId
 * @throws EmailError
 * @returns {Promise}
 */
function sendEmail(userId) {
  return new Promise(resolve => resolve());
}

/**
 * Generate a JWT token to send to the client
 *
 * @param {Object} user
 * @returns {Promise<String>}
 */
function generateJWT(user) {
  const token = 'DUMMY_JWT_TOKEN';

  return new Promise(resolve => resolve(token));
} 
```

Enter fullscreen mode Exit fullscreen mode

耶！

### 没有外作用域变量

现在我们所有的函数都在同一个块中被调用，因此是在同一个作用域中，而不是在一个`then`函数中被传递。我们不必做不必要的赋值，也不必保留全局变量。

### 无无卡承诺退货

先前声明的函数`validateInput`和`comparePasswords`现在可以存在于主程序块中。我不会对它们编写单元测试，也不会在代码库中的其他地方使用它们，所以我不必将它们放在单独的函数中。功能越少，代码越少。

### 可读代码

更少的代码意味着更容易阅读和讨论。

### 没有青鸟依赖

现在不需要蓝鸟了，因为我们可以返回本地 Promise 对象。之前，我使用特定错误类型的`catch`和特定于蓝鸟的`try`。

## 结论

我们应该一直努力完善和改进我们的代码库。Async/await 可以带来很多改进，帮助我们编写更可读的代码，更容易争论和发现错误。如果你不得不使用 still Promises，查看 Axel Rauschmayer 博士的一篇精彩文章,了解更多的承诺和异步/等待模式。

Node.js 8 现在处于 LTS 模式，所以你没有理由不升级和错过 Javascript 闪亮的新特性。

请在下面的评论中告诉我你的想法，并与你认为对你有帮助的人分享这篇文章。🤝