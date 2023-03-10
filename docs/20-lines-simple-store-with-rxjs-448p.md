# 带 RxJS 的 20 线简易商店

> 原文：<https://dev.to/lacolaco/20-lines-simple-store-with-rxjs-448p>

```
import { distinctUntilChanged, map } from 'rxjs/operators';
import { BehaviorSubject } from 'rxjs/BehaviorSubject';

export abstract class Store<T> extends BehaviorSubject<T> {
  constructor(initialState: T) {
    super(initialState);
  }

  public dispatch(fn: (state: T) => T) {
    this.next(fn(this.getValue()));
  }

  public select<R>(fn: (state: T) => R) {
    return this.pipe(map<T, R>(fn), distinctUntilChanged());
  }

  public selectSync<R>(fn: (state: T) => R) {
    return fn(this.getValue());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

示例:用户商店

```
import { User } from '../entity/user';
import { Store } from '../../store';

export interface State {
  currentUser: User | null;
}

export class UserStore extends Store<State> {
  constructor() {
    super({
      currentUser: null,
    });
  }

  get currentUser$() {
    return this.select(state => state.currentUser);
  }
  get currentUser() {
    return this.selectSync(state => state.currentUser);
  }

  setCurrentUser(user: User) {
    this.dispatch(state => ({
      ...state,
      currentUser: user,
    }));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode