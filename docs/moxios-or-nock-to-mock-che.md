# 莫西奥斯或诺克嘲弄

> 原文：<https://dev.to/hawicaesar/moxios-or-nock-to-mock-che>

我最近在测试异步动作创建器时遇到了一个有趣的障碍。我在测试 thunk 运行时是否调用了删除操作创建器。下面的代码分别显示了一个 thunk 和一个 action creator。

```
/**
 * Delete user contribution thunk
 *
 * @param {string} contribution for the contribution to be deleted
 * @return {(dispatch) => Promise<TResult2|TResult1}
 */
export const deleteUserContribution = (contribution) => {
  return ((dispatch) => {
    return http.delete(`${config.API_BASE_URL}/contributions/${contribution.id}`)
      .then((response) => {
        dispatch(deleteUserContributionSuccess(contribution.id));
      });
  });
};

/**
 * Delete user contribution action creator
 *
 * @param {string} contributionId for the contribution to be deleted
 * @return {IDeleteUserContributionSuccess}
 */
export const deleteUserContributionSuccess = (contributionId): IDeleteUserContributionSuccess => {
  return { type: DELETE_USER_CONTRIBUTION_SUCCESS, contributionId };
}; 
```

Enter fullscreen mode Exit fullscreen mode

我最初使用 nock 来模拟请求。

```
// third party libraries
import configureMockStore from 'redux-mock-store';
import thunk from 'redux-thunk';
import { expect } from 'chai';
import * as moxios from 'moxios';

// fixtures
import { sampleContribution } from '../fixtures/contribution';

// actions
import { deleteUserContribution } from './ContributionActions';

// types
import { DELETE_USER_CONTRIBUTION_SUCCESS }  from '../types';

const middleware = [thunk];
const mockStore = configureMockStore(middleware);
let store;
let url;

describe('Async Contribution Actions', () => {
 it('creates DELETE_USER_CONTRIBUTION_SUCCESS when deleting a contribution', () {
     nock(`${config.API_BASE_URL}`)
      .delete(`/contributions/${contribution.id}`)
      .reply(204,'')

     const expectedActions = [
     { type: DELETE_USER_CONTRIBUTION_SUCCESS, contributionId: sampleContribution.id },
];

    return store.dispatch(deleteUserContribution(sampleContribution)).then(() => {
      const actualActions = store.getActions();
      expect(actualActions).to.eql(expectedActions);
    });
  });
}) 
```

Enter fullscreen mode Exit fullscreen mode

我运行了测试，它通过了。然后，我开始为删除功能制作 PR，并要求我的团队进行审查。一个也在编写异步动作测试的团队成员让我放松了下来，并指出 *package.json* 已经有了 Moxios，我可以利用它而不是安装一个新的模拟库。我决定尝试一下。我遵循了 Moxios 文档并实现了它。然而，我得到了以下错误。

```
 1) Async Contribution Actions
       creates DELETE_USER_CONTRIBUTION_SUCCESS when deleting a contribution: Error: Timeout of 2000ms exceeded. For async tests and hooks, ensure "done()" is called;  if returning a Promise, ensure it resolves. 
```

Enter fullscreen mode Exit fullscreen mode

我在 expect 之后添加了 *done()* ,但是上面的错误出现了。
我决定让那个鼓励我使用 Moxios 的团队成员放松一下，在我开口之前，他已经给我发了一条信息。该消息详细描述了他拔掉了我的分支，并通过将 expect 更改为类似这样的东西来破坏我的测试

```
return store.dispatch(deleteUserContribution(sampleContribution)).then(() => {
      const actualActions = store.getActions();
      expect(actualActions).to.eql(1);
    }); 
```

Enter fullscreen mode Exit fullscreen mode

他告诉我测试还没通过。他还补充说，他也有同样的问题，nock 似乎没有正确执行其工作，因此转移到 Moxios。起初我持怀疑态度，但我决定调查此事。你瞧，我发现 nock 只是在解析这个承诺，并没有返回我让它返回的数据。唯一让我困惑的是为什么 expect 还在工作。我使用 Moxios 实现了这个测试，它的工作方式如下。导入和中间件保持不变。

```
describe('Async Contribution Actions', () => {
  beforeEach(() => {
    moxios.install();
    store = mockStore({});
    url = `${config.API_BASE_URL}`;
  });
  afterEach(() => {
    moxios.uninstall();
  });

  it('creates DELETE_USER_CONTRIBUTION_SUCCESS when deleting a contribution', (done) => {
    moxios.stubRequest(`${url}/${sampleContribution.id}`, {
      status: 204,
      response: { data: '' },
    });

    const expectedActions = [
      { type: DELETE_USER_CONTRIBUTION_SUCCESS, contributionId: sampleContribution.id },
    ];

    store.dispatch(deleteUserContribution(sampleContribution)).then(() => {
      const actualActions = store.getActions();
      expect(actualActions).to.eql(expectedActions);
      done();
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我试图通过添加一个错误的实际值来破坏测试，结果失败了，这意味着 Moxios 做得更好。我知道 redux 文档使用 fetch-mock，但我怀疑它以前使用 nock。

我现在想问你的问题是，你用什么来测试异步动作创建者？