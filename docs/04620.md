# 编写冗余可观察的测试

> 原文：<https://dev.to/julioolvr/writing-tests-for-redux-observable>

我最近参加了一个关于`redux-observable`的讲座，在玩过 Rx 并喜欢上它之后，我决定试一试。我不会进入如何使用它的细节，但我确实花了一些时间找出一种方法来轻松测试我想分享的史诗。

比方说，为了认证我们应用程序的用户，我们有一个类似这样的史诗:

```
function loginEpic(action$) {
  return action$.ofType(LOGIN_START).mergeMap(action =>
    api
      .login(action.payload.email, action.payload.password)
      .map(loginSuccessful)
      .catch(err => Observable.of(loginFailed(err))),
  );
} 
```

epic 采用我们的主要动作流，并且对于每个`LOGIN_START`动作，或者用`loginSuccessful`动作创建者生成一个动作，或者用`loginFailed`动作创建者生成一个动作。

在这里，我看到了三件值得进行单元测试的事情:

*   正在进行适当的 API 调用。
*   如果登录成功，我们将生成成功操作。
*   如果登录失败，我们将生成错误操作。

所有测试背后的基本原理是相同的:我们将使用 LOGIN_START 动作创建一个可观察对象，并将其传递给 epic，订阅它并断言生成的动作。让我们看一下第一个，检查 API 调用:

*我在这里用 Jest 来表示断言和嘲讽，但同样的事情也可以用其他框架来做*

```
it('logins through the api on LOGIN_START', (done) => {
  const email = 'test@test.com';
  const password = '123456';
  const action$ = ActionsObservable.from([login(email, password)]);

  api.login.mockImplementation(() => ActionsObservable.of({}));

  epic(action$)
    .subscribe(() => {
      expect(api.login).toHaveBeenCalledWith(email, password);
      done();
    });
}); 
```

有几点需要注意:

*   `login`函数是生成`LOGIN_START`动作的动作创建器。既然已经有了，那就用它有意义。
*   API 的实现是为了返回 observables，所以这就是为什么 mock 实现返回一个只是发出一个空对象的 API(在这个测试中，我们并不真正关心响应)
*   `api`在这个测试之外被 Jest 的模拟设施嘲笑，像这样:

```
jest.mock('../lib/api', () => ({ login: jest.fn() })); 
```

除此之外，我们将动作流传递给 epic，我们订阅，然后我们希望在第一个动作生成之后，我们应该已经用正确的参数调用了 API。让我们来看看检查生成的动作的测试:

```
it('emits a LOGIN_SUCCESS action if the API call succeeds', (done) => {
  const action$ = ActionsObservable.from([login('test@test.com', '123456')]);
  const user = {};

  api.login.mockImplementation(() => ActionsObservable.of(user));

  epic(action$)
    .filter(action => action.type === LOGIN_SUCCESS)
    .subscribe((action) => {
      expect(action.payload).toBe(user);
      done();
    });
});

it('emits a LOGIN_FAILED action if the API call fails', (done) => {
  const action$ = ActionsObservable.from([login('test@test.com', '123456')]);
  const error = new Error();

  api.login.mockImplementation(() => ActionsObservable.throw(error));

  epic(action$)
    .filter(action => action.type === LOGIN_FAILED)
    .subscribe((action) => {
      expect(action.payload).toBe(error);
      done();
    });
}); 
```

它的要点是，我们过滤由 epic 生成的动作，以确保我们得到了正确的类型，然后当我们订阅时，我们检查这些动作的有效载荷是正确的。

*注意*:我用的是`filter`而不是`ofType`，就像我在史诗中用动作类型过滤一样。这是因为我不能确定 epic 返回的可观测值将会是一个`ActionsObservable`而不是一个常规的可观测值。

就是这样！我认为这是检验史诗的简单方法。对于更复杂的情况，这可能还不够，但我个人认为这非常简单，也很容易推理。