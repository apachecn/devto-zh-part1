# 将视图模型添加到 Laravel 项目中

> 原文：<https://dev.to/lloople/adding-view-models-to-a-laravel-project-hod>

自 2017 年初以来，我开始尝试以前没有使用过的不同类型的代码和模式。

其中一个变化发生在 11 月，当时我正在开发另一个博客的[代码。我发现我在我的控制器中复制了很多代码，这些代码是关于资源的`create`和`edit`的方法，在这些方法中我使用了相同的刀片文件。](https://github.com/lloople/blog)

该视图的一些数据取决于我正在编辑或创建的模型。例如，如果是一个现有的记录，标题应该是`Edit post: {$post->title}`而不是`Create a post`。该逻辑也适用于 form 操作和 form 方法。

我创建了一个 main `FormViewModel`，每个专用于表单的视图模型都扩展了它。

```
abstract class FormViewModel
{

    public $model;
    public $title;
    public $action;
    public $method;

    public function __construct(Model $model)
    {
        $this->model = $model;

        $this->setTitle();

        $this->setAction();

        $this->setMethod();
    }
} 
```

这个视图模型被强制声明在构造函数上调用的方法

```
protected abstract function setTitle();

protected abstract function setAction();

protected abstract function setMethod(); 
```

这是`CategoryDetailViewModel`中`setTitle`方法的一个实现:

```
protected function setTitle()
{
    $this->title = $this->model->exists
        ? "Edit Category: {$this->model->name}"
        : "Create Category";
} 
```

他们还可以在自己的构造函数中添加更多的逻辑，比如`PostDetailViewModel`:

```
public function __construct(Post $post)
{
    parent::__construct($post);

    $this->published_at = $this->model->exists
        ? $this->model->published_at->format('Y-m-d\TH:i')
        : Carbon::now()->format('Y-m-d\TH:i');

    $this->categories = Category::select('id', 'name')->get();
} 
```

使用视图模型非常容易，控制器只需要将这个对象传递给视图

```
public function edit(Theme $theme)
{
    return view('backend.themes.edit', [
        'view' => new ThemeDetailViewModel($theme)
    ]);
} 
```

和视图使用对象属性和方法

```
@section('content')
    <h1>{{ $view->title }}</h1>

    <form method="POST" action="{{ $view->action }}">
        {{ method_field($view->method) }}
        {{ csrf_field() }}
        ...
    </form>
@endsection 
```