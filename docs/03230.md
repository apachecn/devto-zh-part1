# 使用 ES6 数组析构与承诺

> 原文：<https://dev.to/mrm8488/using-es6-array-destructing-with-promises-cco>

(最初发布于一年多前的 Medium 上)

如你所知，几个月前，它宣布了 ES6 或 EcmaScript2015 的新功能。一开始，并不是所有的代码都是由一些浏览器或 Node.js 实现的，但是我们有 Babel.js 这样的工具允许我们在 ES6 中编程，然后它会将代码翻译成 ES5。
我是一名 Node.js 开发者，当我阅读 ES6 的新功能列表时，其中有一项可以帮助我继续**对抗回调地狱，并提高承诺的可读性**。特别是 Promise.all()的可读性，正如我们所知，它并行启动了一组承诺。这个特性是数组析构。

这是我在使用 ES6 数组析构之前使用 **Promise.all()的方式:**

```
"use strict";

const getUserInfo = () =>
  new Promise((resolve, reject) => {
    resolve({
      id: 1,
      name: "Manuel",
      surname: "Romero"
    });
  });

const countUserFriends = () => new Promise((resolve, reject) => resolve(50));

const getUserSkills = () =>
  new Promise((resolve, reject) => {
    resolve({
      skills: ["JavaScript", "ES6", "MongoDB", "Angular.js"]
    });
  });

Promise.all([getUserInfo(), countUserFriends(), getUserSkills()])
  .then(results => {
    const userInfo = results[0];
    const numberOfFriends = results[1];
    const userSkills = results[2];

    console.log(userInfo);
    console.log(numberOfFriends);
    console.log(userSkills);
  })
  .catch(err => console.log(err)); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们有 3 个可以并行启动的承诺:第一个是获取用户的基本信息。第二个是获取用户的好友数量，最后一个获取用户的技能。
正如我们看到的，包含 Promise.all()函数结果的变量被命名为 results。Results 是一个数组，其位置与作为 Promise.all()的参数发送的 promises 一样多。此外，第一个承诺的结果放在数组的第一个位置，第二个承诺的结果放在第二个位置，依此类推。但是我为了提高可读性所做的是将数组的每个结果(位置)赋给一个新的变量。因为访问结果[n]是没有意义的。
现在，让我们看看如何用 ES6 数组直接完成**:**:

```
"use strict";

const getUserInfo = () =>
  new Promise((resolve, reject) => {
    resolve({
      id: 1,
      name: "Manuel",
      surname: "Romero"
    });
  });

const countUserFriends = () => new Promise((resolve, reject) => resolve(50));

const getUserSkills = () =>
  new Promise((resolve, reject) => {
    resolve({
      skills: ["JavaScript", "ES6", "MongoDB", "Angular.js"]
    });
  });

Promise.all([getUserInfo(), countUserFriends(), getUserSkills()])
  .then(([userInfo, userFriends, userSkills]) => {
    console.log(userInfo);
    console.log(userFriends);
    console.log(userSkills);
  })
  .catch(err => console.log(err)); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我给数组的每个位置取了一个名字。这些名称是我们可以在回调中管理的变量。这样我们节省了代码，最重要的是，我们写的代码可读性强，易于理解和维护。
PS:本文中的两个片段(上图)是在 Node v6 下测试的。

### 异步/等待版本(额外提供给朋友) :

```
"use strict";

const getUserInfo = () =>
  new Promise((resolve, reject) => {
    resolve({
      id: 1,
      name: "Manuel",
      surname: "Romero"
    });
  });

const countUserFriends = () => new Promise((resolve, reject) => resolve(50));

const getUserSkills = () =>
  new Promise((resolve, reject) => {
    resolve({
      skills: ["JavaScript", "ES6", "MongoDB", "Angular.js"]
    });
  });

const getAllUserData = async () => {
  try {
    const [userInfo, userFriends, userSkills] = await Promise.all([
      getUserInfo(),
      countUserFriends(),
      getUserSkills()
    ]);

    const userData = {
      userInfo,
      userFriends,
      userSkills
    };
    return userData;

  } catch (error) {
    return Promise.reject(error);
  }
};

/* MAIN */
getAllUserData()
  .then(userData => console.log(userData))
  .catch(error => console.log(error)); 
```

Enter fullscreen mode Exit fullscreen mode