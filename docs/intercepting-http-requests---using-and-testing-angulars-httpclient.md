# 拦截 Http 请求——使用和测试 Angular 的 HttpClient

> 原文：<https://dev.to/alisaduncan/intercepting-http-requests---using-and-testing-angulars-httpclient>

在 Angular 4.3 中首次亮相的`HttpClientModule`，是一个易于使用的 API。它自动将 json 作为默认响应类型，内置了拦截请求和响应的能力，使测试变得轻而易举。

我最近致力于将通话从`HttpModule`转换为使用`HttpClientModule`。在这个项目中，我很难找到好的资源和例子，尤其是测试的例子。所以我决定写一个简单的应用程序，使用`HttpClientModule`和相应的单元测试作为例子，比 [Angular 的文档](https://angular.io/guide/http#httpclient)多一点*魅力*。它并不完美，因为我自己还在学习，但我希望它能对你有所帮助。

## 更新

6/10/2018-本文中的代码示例和 GitHub repo 中的代码现在使用 Angular v6。

## 假设

本教程假设您对 Angular、angular-cli、Material2 库、Jasmine、调用 REST apis，...本教程的范围仅仅是使用`HttpClientModule`和相关的单元测试来进行 http 调用。

## 背景

使用 angular-cli，我生成了一个新的应用程序，并使用 Material 2 starter 应用程序作为设计和可爱图标的基础。angular-cli 用于为服务、组件和类生成所有的脚手架。我使用了 [JSONPlaceholder API](https://jsonplaceholder.typicode.com/) 来获取和发布用户信息。完整的 app 和代码可以在我的 GitHub repo 中找到[。](https://github.com/alisaduncan/tutorial-angular-httpclient)

## 创建用户服务

用户服务包装了创建和检索用户信息的功能。这就是我们将使用`HttpClient`的地方。在方法`getUsers()`中，注意我们不再需要将响应映射到 json 格式。我们还可以声明响应数据的类型。我使用了一个令人难以置信的无用的`Array<any>`，但这只是为了显示语法。

```
 public getUsers(): Observable<User[]> {
    return this.http.get<Array<any>>(this.apiEndpoint, {
      headers: new HttpHeaders().set('Accept', 'application/json')
    }).pipe(
      map(this.mapUsers),
      catchError(error => {
        return observableThrowError('An error occurred');
      }),
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记将`HttpClientModule`添加到 *app.module.ts* 导入中。

## 测试用户服务

Angular 对通过`HttpClientTestingModule`和`HttpClientTestingController`测试 http 调用做了很多改进。只需将`HttpClientTestingModule`导入到`TestBed`中，并将`HttpClient`和`HttpTestingController`都注入到测试中。

```
describe('UserService', () => {
  beforeEach(() => {
    TestBed.configureTestingModule({
      imports: [HttpClientTestingModule],
      providers: [UserService]
    });
  });

  afterEach(inject([HttpTestingController], (httpMock: HttpTestingController) => {
    httpMock.verify();
  })); 
```

Enter fullscreen mode Exit fullscreen mode

在测试中，您模拟一个 http 响应，然后通过刷新`TestRequest`来模拟 http 调用。您可以断言 http 方法、预期响应、预期头和 http 调用的次数。您将需要一个`afterEach`方法来验证不再有请求被消费。这里有一个快乐路径的例子来验证我的响应过滤和映射工作。

```
it('returns users with an id <= 5', inject([HttpClient, HttpTestingController], (http: HttpClient, httpMock: HttpTestingController) => {
      const mockResponse = [
        {
          id: 5,
          name: 'Test5',
          company: {bs: 'blah'}
        },
        {
          id: 6,
          name: 'Test6',
          company: {bs: 'blah'}
        }
      ];

      const userService = getTestBed().get(UserService);
      userService.getUsers().subscribe(
        actualUsers => {
          expect(actualUsers.length).toBe(1);
          expect(actualUsers[0].id).toEqual(5);
        }
      );

      const req = httpMock.expectOne(userService.apiEndpoint);
      expect(req.request.method).toEqual('GET');

      req.flush(mockResponse);
      httpMock.verify();
    })); 
```

Enter fullscreen mode Exit fullscreen mode

测试不愉快的路径一点也不难。

```
it('should throw with an error message when API returns an error',
      inject([HttpClient, HttpTestingController], (http: HttpClient, httpMock: HttpTestingController) => {
        const userService = getTestBed().get(UserService);
        userService.getUsers().subscribe({
          error(actualError) {
            expect(of(actualError)).toBeTruthy();
            expect(actualError).not.toBeNull();
            expect(actualError).not.toBeUndefined();
          }
        });

        const req = httpMock.expectOne(userService.apiEndpoint);
        expect(req.request.method).toEqual('GET');

        req.flush({ errorMessage: 'Uh oh!' }, { status: 500, statusText: 'Server Error' });
        httpMock.verify();
      })); 
```

Enter fullscreen mode Exit fullscreen mode

## 拦截电话

在真正的应用程序中，我们需要验证我们的请求。所以我们可以通过实现 Angular 的`HttpInterceptor`接口来创建自己的拦截器。在这个例子中，我为每个调用添加了一个授权头。我创建了一个假的身份验证服务来欺骗令牌检索，并注入身份验证服务来更好地模仿现实生活，形成我的令牌。

```
export class AuthInterceptor implements HttpInterceptor {
  constructor(private authService: AuthService) {}

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
  const authReq = req.clone({setHeaders: {Authorization: `${this.authService.tokenType}  ${this.authService.tokenValue}`}});
    return next.handle(authReq);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了使用拦截器，我们必须将它提供给 *app.module.ts* 。

```
providers: [UserService, AuthService,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }], 
```

Enter fullscreen mode Exit fullscreen mode

现在，所有使用`HttpClient`进行的调用都将包含授权头。

## 测试拦截器

就像之前一样，你需要准备`Testbed`。但这次我们也将提供拦截器。我还为认证服务提供了一个模拟。

```
beforeEach(() => {
        TestBed.configureTestingModule({
            imports: [HttpClientTestingModule],
            providers: [
                {
                    provide: AuthService,
                    useValue: mockAuthService
                },
                {
                    provide: HTTP_INTERCEPTORS,
                    useClass: AuthInterceptor,
                    multi: true
                }]
        });
    }); 
```

Enter fullscreen mode Exit fullscreen mode

测试拦截器与测试用户服务非常相似。除了在这种情况下，我们将定制验证来检查头。

```
describe('making http calls', () => {
        it('adds Authorization header', inject([HttpClient, HttpTestingController], (http: HttpClient, httpMock: HttpTestingController) => {

            http.get('/data').subscribe(
                response => {
                    expect(response).toBeTruthy();
                }
            );

            const req = httpMock.expectOne(r =>
                r.headers.has('Authorization') &&
                r.headers.get('Authorization') === `${mockAuthService.tokenType}  ${mockAuthService.tokenValue}`);
            expect(req.request.method).toEqual('GET');

            req.flush({ hello: 'world' });
            httpMock.verify();
        }));
    }); 
```

Enter fullscreen mode Exit fullscreen mode

我目前只有这些了。我希望你觉得这很有用。我所有的代码都可以在我的 GitHub repo 中找到[。请随时查看，我欢迎您的任何反馈。](https://github.com/alisaduncan/tutorial-angular-httpclient)