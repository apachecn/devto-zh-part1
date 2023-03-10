# 使用 TDD 模式#5 在 Laravel 5.5 上构建高级博客用户可以创建帖子

> 原文：<https://dev.to/kris/building-advance-blog-on-laravel-55-with-tdd-mode-5-user-can-create-post-7la>

[![](img/ebc1618d3e386d9476b83a56ab510344.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yL-v-v8Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyO9YWHG9DX4KdROkIn6ICg.png)

在这一集里，我在这一集里创建了一个评论功能，它是老一套但不同模式的作品

在这一集里，我已经写了一篇文章，如果你看过我以前的文章，就很容易开始了

我们开始吧

创建特性测试名 test_a_user_can_create_post 并快速写下用户故事

```
public function test_a_user_can_create_post(){
         // Given a Guest
          $guest = factory('App\User')->create();
          // make a guest become User
          $user = $this->be($guest);
         // And Giving Post object 
         $post = factory('App\Post')->make();
         // When the user create Post
          $this->post('/blog/'.$post->id,$post->toArray());
         // When their visit
           $this->get('/blog/'.$post->id); 
         //Then their should see post
        $this->assertSee($post->title);

} 
```

保存并运行测试

[![](img/7648452f5bba6d6147ca7d5fd29cacb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E4ILEnNG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/849/1%2AsoxN5zJcyLi1jQ6Ibc2NNA.png)

应该会失败

但是应该抛出**路线未发现异常**而不是跳过它们

在 Laravel 5.5 中我们有了新的方法

> **在测试中切换异常处理:** >为应用程序编写测试时，您可能会遇到不希望 Laravel 自动捕获和转换异常的情况。现在，您可以通过 *$this- >而不调用 ExceptionHandling()* 方法，逐个测试地禁用异常处理。
> 
> 信用:[https://medium . com/@ Jane brewer 16/whats-new-in-laravel-5-5-a-best-we b-app-development-frame wwork-c 846 a 735 af 67](https://medium.com/@JaneBrewer16/whats-new-in-laravel-5-5-a-best-web-app-development-framewwork-c846a735af67)

让我们把它修好

```
public function test_a_user_can_create_post(){
       $this->withoutExceptionHandling(); 
         // Given a Guest
          $guest = factory('App\User')->create();
          // make a guest become User
          $user = $this->be($guest);
         // And Giving Post object 
         $post = factory('App\Post')->make();
         // When the user create Post
         $this->post('/post',$post->toArray());
         // When their redirect to post
         $response = $this->get('/blog/'.$post->id);
         // Then their should see post
         $response->assertSee($post->title);

} 
```

再次测试

[![](img/5aa3ea0e38e75b398deefb1037fdd683.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--h-t7cHZP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/935/1%2AQkFA_zfigF-SshxtEnHE-A.png)

好，让我们创建路线

### 创建路线

打开 route/web.php 并填充

```
Route::post('/post','PostsController@store'); 
```

再次测试

[![](img/728342899c8ad45141c14f2c10aa660e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IJ2s9LVs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/908/1%2AB-bQW-BmkW0zHcWL3UrvMg.png)

### 创建端点

打开后控制器并填充功能存储器

```
public function store(Request $request){
       $post = Post::create([
            'user_id'=>auth()->id(),
            'title' =>$request->title,
            'body' =>$request->body

        ]);
     return redirect('/blog/'.$post->id);
    } 
```

再试一次

[![](img/6d06889973839635d329e66e76be9f42.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pN3Y_L0h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/747/1%2AiRapOTIO6IU06BNqxml9Qg.png)

我们应该确保**客人不能提交帖子**

将认证中间件添加到**后控制器**

[![](img/4ad09d5d484c32f6518fe07fe0b98102.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1zE12u3j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/722/1%2AhzrR-TFV7LcbXFowAURDHg.png)

接下来，我创建了测试

```
public function test_a_guest_can_not_create_post(){
        $this->withoutExceptionHandling(); 
         // Given a Guest
          $guest = factory('App\User')->create();
          // And Giving Post object 
           $post = factory('App\Post')->make();
         // When the user create Post
         $this->post('/post',$post->toArray());
    } 
```

运行测试

[![](img/33c7db27261d87fcddeec0e7113f31db.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZF79vzdH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/820/1%2ARj3vMi2eWHd0C5lrPa-QQQ.png)

好吧，这不出所料。让我们把它变成绿色

```
public function test_a_guest_can_not_create_post(){
        $this->withoutExceptionHandling(); 
        // expect thrown exception
 $this->expectException('Illuminate\Auth\AuthenticationException');
         // Given a Guest
          $guest = factory('App\User')->create();
          // And Giving Post object 
           $post = factory('App\Post')->make();
         // When the user create Post
         $this->post('/post',$post->toArray());

    } 
```

它是绿色的

[![](img/7cee3bfdf7dfc452092cc0171bc94234.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7XSxNSIW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/673/1%2AUs9U2BBkchVvkGpG95-ZIA.png)

好，我们在引擎盖下完成

### **创建表单**

接下来，我们为提交帖子创建一个前端表单

打开 route/web.php 并填写此

```
Route::view('/blog/create','post.create'); 
```

创建文件名 post/create.blade.php 并从 post/index.blade.php 获取代码

粘贴在这

[![](img/3b21556b2019f72a03eb9a592cfc40fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xEGHEAj0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/853/1%2AQBEcEDAsoPPSooBZ2MW10Q.png)

并用
填写面板体

```
<form method="post action="/post">
        {{ csrf_field() }}
        <div class="form-group">
       <label for="title">Title</label>
      <input type="text" name="title" class="form-control"/>
          </div>
             <div class="form-group">
                          <label for="body">Body</label>
   <textarea name="body" rows="8" class="form-control"></textarea>
                      </div>
        <input type="submit" class="btn btn-block btn-primary">
                   </form> 
                   </div> 
```

结果是这样的

[![](img/60708ddfdebf30d4745fbb9edf2f0b8f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--26SJwlmM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdMeL5H4qIyDezkCNzFMjcQ.png)

保存并转到/博客/创建

[![](img/7d4457a86faa3a920c49c08d43ec5b32.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--cnamvsYj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEnBZ11q6bRGHSMwHKcDnEw.png)

然后尝试填写表格并提交

[https://medium . com/media/3bd 64 cf 3d 11d 64231 e6a 875331522891/href](https://medium.com/media/3bd64cf3d11d64231e6a875331522891/href)

使用中间件进行了重定向，我们应该将此视图包含到中间件中

```
Route::view('/blog/create','post.create')->middleware('auth'); 
```

当我们访问创建文章页面，然后仍然重定向到登录

那就再试一次

[![](img/144a64f25bcb73aa77e905fed798d750.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7pMYIO9N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmaytNMWiT5YNM8d9dXB9qw.png)

是的，仍然工作，然后我们应该创建测试，以确保客人可以访问后创建页面

创建测试 _ 来宾 _ 不能 _ 访问 _ 创建 _ 发布 _ 页面

并像这样填写代码

```
public function test_a_guest_can_not_access_create_post_page(){
        $this->get('/blog/create')
            ->assertRedirect('/login');
    } 
```

确保我们单独进行测试

[![](img/0f3b0a411505e38eb3a53baf4d514273.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6GAkKdR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/759/1%2A4m3PbqJLiNWabMihJ7oedQ.png)

现在我们有了创建帖子的表单

**如果你喜欢这个，请给我 50 个掌声，点击“跟随我”,我会在下面发表鼓励前进的文章**

**这个** [**项目 Github 提交**](https://github.com/krissnawat/advance-blog-in-laravel-5.5-in-tdd/commit/7877f1812340de1bb7b8536cae8682442f3d1779) **就可以启动了**