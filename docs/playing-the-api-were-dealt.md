# 播放 API 我们发牌

> 原文：<https://dev.to/rysilva/playing-the-api-were-dealt>

> 重要的不是你拿到的 API，而是你如何玩这手牌

当编写 web 应用程序时，有时我们没有得到我们想要的 API。要么“RESTful”API 不是，要么数据以奇怪的格式发送，要么我们可能不得不支持不适合 REST 模型的请求。当我们不能改变我们的手，我们的工作是使 UI 代码功能，而不是成为一个不可维护的混乱。

我使用 API 的方法遵循“瘦控制器，胖服务”的方法。我希望所有的业务逻辑和数据操作都发生在一个服务中，抽象在合理的、可预测的、易于测试的方法之后。以下是我玩不太理想的牌的一些方式。我用的是 Angular，但是这些想法并不是特定于框架的。

### 将有效载荷混合起来

我在使用我的 API 时遇到的最常见的情况是我想改变有效载荷的结构。有时我想先对集合进行排序，因为 API 不会为我排序，或者我只需要重命名一个属性。在其他情况下，我需要从 API 响应中提取完整的结构。

为了解决这个问题，我从 BackboneJS 借用了一个约定，并实现了一个`parse`方法，该方法获取原始响应数据并返回对象所需的数据。我有一个使用这种方法的公共 REST 服务。在我们的代码库中工作的每个人都知道任何传入的数据操作都发生在`parse`中，这提供了可预测性和一致性。有了明确的输入输出需求，`parse`也就容易进行单元测试。

`parse`中的逻辑应该*永远不要*进入控制器。我们希望数据表示在到达时只改变一次，并且在不同的视图中保持一致。如果一个控制器重新排列了数据，那么如果这个控制器被移除并且其他代码依赖于新的格式，我们就有回归的风险。

下面的代码片段显示了一个基本 API 调用的服务、控制器和单元测试，该调用需要对一些数据进行排序，并在获取后重新分配一个字段:

```
//MyResource will store the data fetched from the API
class MyResource {
  get url() {
    return '/api/my_resource';
  }
  //Incoming data will have its list sorted and a description field set
  parse(data) {
    if (data) {
      if (data.list) {
        data.list = data.list.sort();
      } else {
        data.list = [];
      }
      data.description = data.notes;
    }
    return data;
  }
}

//An abbreviated example of a common REST service
function restServiceFactory($http) {
  class RestService {
    //Perform a GET of the resource
    fetch(resource) {
      return $http.GET(resource.url).then(resp => {
        //First run the data through parse
        let data = resource.parse(resp.data);
        //Is this a collection? Update the array with the response data
        if (isArray(resource)) {
          resource.splice(0, resource.length, ...data);
        } 
        //It's an object; update it with the response data
        else {
          angular.extend(resource, data);
        }
        return resource;
      });
    }
  }

  return new RestService();
}

angular.module('my.module', []).factory('restService', restServiceFactory);

//Example controller which fetches MyResource
class MyController {
  constructor(restService) {
    this.resource = new MyResource();
    restService.fetch(this.resource);
  }
}

//Unit tests for MyResource
describe('MyResource test', function() {
  it('initializes list', function() {
    let resource = new MyResource();
    expect(resource.parse({}).list).toEqual([]);
  });

  it('sorts the list', function() {
    let resource = new MyResource(),
      list = ["pear", "apple", "banana"];
    expect(resource.parse({list}).list).toEqual(["apple", "banana", "pear"]);
  });

  it('sets description field from notes', function() {
    let resource = new MyResource(),
      notes = "some notes";
    expect(resource.parse({notes}).description).toEqual(notes);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

有时候，我需要提取一个与 API 提供的完全不同的结构。假设我有一个图书列表 API。我的应用程序实际上需要一个作者列表，但是那个 API 不存在。我可能会想做这样的事情:

```
class BookCollection extends Array {
  get url() {
    return '/api/books';
  }
}

class AuthorListController {
  constructor(restService) {
    //Fetch list of books, then extract the author list from it
    restService.fetch(new BookCollection()).then(books => {
      let authorsMap = {};
      this.authors = [];
      books.forEach(book => {
        if (!authorsMap[book.author.id]) {
          authorsMap[book.author.id] = true;
          this.authors.push(book.author);
        }
      });
    });
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

但是现在控制器更胖了，这个逻辑不能被其他视图重用。这样会好得多:

```
class BookCollection extends Array {
  get url() {
    return '/api/books';
  }
  getAuthors() {
    let authorsMap = {}, authors = [];
    this.forEach(book => {
      if (!authorsMap[book.author.id]) {
        authorsMap[book.author.id] = true;
        authors.push(book.author);
      }
    });
    return authors;
  }
}

class AuthorListController {
  constructor(restService) {
    restService.fetch(new BookCollection())
      .then(books => this.authors = books.getAuthors());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在逻辑隐藏在服务中，任何有藏书的人都可以获得作者列表。但是，如果这个应用程序主要关注作者，而我们从来不需要藏书，那该怎么办呢？通过使用解析方法，我们可以更改服务以更好地表达我们的目标——我们可以从一开始就处理作者的集合:

```
class AuthorCollection extends Array {
  get url() {
    return '/api/books';
  }
  //API input is a list of books
  parse(data) {
    let authorsMap = {}, authors = [];
    this.forEach(book => {
      if (!authorsMap[book.author.id]) {
        authorsMap[book.author.id] = true;
        authors.push(book.author);
      }
    });
    return authors;
  }
}

class AuthorListController {
  constructor(restService) {
    restService.fetch(new AuthorCollection())
      .then(authors => this.authors = authors);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在控制器可以单独处理作者集合，并且它必须使用不同的 API 来获取数据的事实是完全透明的。

### 自定义或非 RESTful 请求

前面的例子展示了如何在基本 get 上操作数据——它们都可以使用相同的`fetch`方法来执行请求。但是如果请求是非 RESTful 的，使用了意外的动词，或者需要以某种特殊的方式定制，该怎么办呢？我将默认的`RestService`扩展为一个新服务，并根据需要添加或覆盖方法。服务应该总是有可读的、显而易见的方法来描述它们要做什么。

#### 覆盖服务方法

如果我们已经在使用`fetch`来做 GETs，那么当下载资源时，如果我们需要做一些不同的事情，覆盖`fetch`是有意义的。例如，如果我想获得图书列表，但我想先使用一个查询参数对集合进行排序:

```
class RestService {
  //fetch accepts httpOptions 
  fetch(resource, httpOptions) {
    return $http.GET(resource.url, httpOptions).then(/*...*/);
  }
}

class BookService extends RestService {
  //Override the default fetch, passing sortBy=name query parameter
  fetch(resource) {
    return super.fetch(resource, {params: {sortBy: 'name'}});
  }
}

class BookListController {
  constructor(restService) {
    restService.fetch(new BookCollection())
      //Already sorted by name
      .then(books => this.books = books);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当 API 开发者坚持更新一本书应该作为一个 POST to `/books`而不是一个传统的 PUT to `/books/{id}`的时候会怎么样？您可以想象 RestService 有一个`save`方法，它通常对资源 URI 进行 PUT。所以我们覆盖了`save` :

```
class RestService {
  //Typically, save does a PUT to the resource URI
  save(resource) {
    return $http.PUT(resource.url, resource).then(/*...*/);
  }
}

class BookService extends RestService {
  save(book) {
    return $http.POST(book.collectionUrl).then(/*...*/);
  }
}

class BookController {
  /*...*/
  save() {
    this.restService.save(this.book);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有许多定制的可能性，但是主要的一点是控制器可能不知道这个端点是怪异的。它只是调用它总是调用的同一个`save`方法。

#### 自定义服务方式

有时我们需要做一些不 RESTful 的事情，不适合我们的模型。假设 URI 的一本书包括作者:`/authors/{authorId}/books/{bookId}`。我们想改变一本书的作者，这意味着一本新书 URI 和一些更新的作者集合。我们的 API 提供了一个单独的端点来将书籍重新分配给新作者:一个到`/authors/{authorId}/books/{bookId}/reassign`的帖子。我们可以用一种新的服务方法来处理这个问题:

```
class BookService extends RestService {
  reassign(book) {
    return $http.POST(`${book.url}/reassign`).then(/*...*/);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

类似于自定义的`save`和`fetch`，这个新方法为一本书提供了新的功能。重新分配一本书所涉及的任何复杂性都应该隐藏在这里，所以控制器不需要知道这些。

* * *

当我们不得不使用分配给我们的 API 而不是我们想要的 API 时，需要花一些心思来保持 UI 代码的良好状态。通过将所有特殊的 API 逻辑限制在服务层中，我们保持了控制器的简单性，减少了回归，并保持了可测试性。你有哪些玩法？