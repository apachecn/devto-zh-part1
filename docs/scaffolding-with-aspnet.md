# ASP.Net 脚手架

> 原文：<https://dev.to/andre2w/scaffolding-with-aspnet>

# 脚手架用 ASP.Net 芯

我非常喜欢 rails 的一点是能够通过 CLI 使用脚手架生成文件，最近我开始学习 ASP.Net 核心。

的。Net Core 与 CLI 配合得很好，使用`dotnet`创建项目、管理迁移和包，我甚至可以使用 VS 代码调试应用程序，但是缺少了一点，视图和控制器的架子工，在 Rails 中即使没有脚手架也能快速创建控制器，但是现在我必须处理名称空间和导入来创建一个简单的控制器。
但是让我们停止讲故事的时间，看一些有用的东西。

## 创建 ASP.Net 核心项目

显然第一件事就是下载。网芯来自[https://www.microsoft.com/net/core](https://www.microsoft.com/net/core)

安装了 CLI 后，我们就可以开始新项目了

```
mkdir ASPBlog
cd ASPBlog
dotnet new mvc
dotnet restore 
```

现在开始为 ASP.NET 核心应用程序生成代码，首先需要在`.csproj`文件中添加三个依赖项。

第一个是回退到`dotNet Framework`，因为不是每个包都与`.Net Core`一起工作，我希望这将随着 Core 2.0 而改变。

```
<PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
    <PackageTargetFallback>$(PackageTargetFallback);dotnet5.6;portable-net45+win8</PackageTargetFallback>
</PropertyGroup> 
```

然后添加以下包:

```
<PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="1.1.1" /> 
```

最后，添加 Cli 工具:

```
<ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="1.0.0" />    
 </ItemGroup> 
```

最后一步是执行一个`dotnet restore`，现在您可以使用`dotnet aspnet-codegenerator`检查生成器是否启动并运行

```
Usage: dotnet aspnet-codegenerator --project [projectname] [code generator name]

Code Generators:
view
controller
area

Try dotnet aspnet-codegenerator --project [projectname] [code generator name] -? for help about specific code generator.
RunTime 00:00:06.74 
```

### 控制器

对于控制器架子工，我们有以下命令:

```
Options:
  --help|-h|-?                         Show help information
  --useAsyncActions|-async             Switch to indicate whether to generate async controller actions
  --noViews|-nv                        Switch to indicate whether to generate CRUD views
  --controllerName|-name               Name of the controller
  --restWithNoViews|-api               Specify this switch to generate a Controller with REST style API, noViews is assumed and any view related options are ignored
  --readWriteActions|-actions          Specify this switch to generate Controller with read/write actions when a Model class is not used
  --model|-m                           Model class to use
  --dataContext|-dc                    DbContext class to use
  --referenceScriptLibraries|-scripts  Switch to specify whether to reference script libraries in the generated views
  --layout|-l                          Custom Layout page to use
  --useDefaultLayout|-udl              Switch to specify that default layout should be used for the views
  --force|-f                           Use this option to overwrite existing files 
```

### 生成我们的第一个控制器

让我们从为静态页面(如 About 和 Contact)创建一个空控制器开始。运行以下命令:
`dotnet aspnet-codegenerator controller -name StaticPagesController -outDir Controllers`

如果一切顺利，您应该有一个名为`StaticPagesController.cs`的新文件，该文件如下所示:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;

namespace ASPBlog.Controllers
{
    public class StaticPagesController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }
    }
} 
```

生成器创建了一个包含所有 usings、命名空间和索引操作的控制器。
但是使用了哪些参数呢？
`-name`参数定义控制器的名称，`-outDir`设置将创建控制器的文件夹。如果你不使用`-outDir`，控制器将被创建在项目的根目录下。
创建控制器后，只需通过向类中添加以下代码来添加 About 和 Contact 动作:

```
 public IActionResult Contact()
        {
            return View();
        }

        public IActionResult About()
        {
            return View();
        } 
```

最后，我们可以创建一个视图来测试我们的代码。
在路径`Views/StaticPages/About.cshtml`中创建必要的文件和文件夹，并添加:

```
@{
    ViewData["Title"] = "About";
}

Normally, both your asses would be dead as fucking fried chicken, but you happen to pull this shit while I'm in a transitional period so I don't wanna kill you, I wanna help you. 
But I can't give you this case, it don't belong to me. Besides, I've already been through too much shit this morning over this case to hand it over to your dumb ass.

Now that we know who you are, I know who I am. I'm not a mistake! It all makes sense! 
In a comic, you know how you can tell who the arch-villain's going to be? He's the exact opposite of the hero. 
And most times they're friends, like you and me! I should've known way back when... You know why, David? Because of the kids. 
They called me Mr Glass. 
```

我们可以通过用`dotnet run`运行 ASP.Net 服务器并访问`http://localhost:5000/StaticPages/About`来检查结果。

### 带有 CRUD 的控制器

我们可以做得更多，一些控制器是简单的 crud 控制器，有一个上下文和一些动作。我们可以只使用`aspnet-codegenerator`为我们的帖子创建一个控制器，包含我们需要的所有东西。

首先我们需要一个模型和一个数据库。在这种情况下，让我们使用 SQLite 和一个带有标题和正文的 Post 模型。

#### 岗位模型

不是 HTTP 的帖子，是我们在博客上做的。我们创建文件`Models/Post.cs`

```
using System.ComponentModel.DataAnnotations;

namespace ASPBlog.Models
{
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }

        [DataType(DataType.MultilineText)]
        public string Body { get; set; }
    }
} 
```

#### SQLite 数据库

将以下包添加到您的`.csproj`文件中:

```
<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="1.1.2" />

<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="1.0.0" /> 
```

最后用`dotnet restore`恢复包。现在我们为 EntityFramework 创建一个上下文，这样我们就可以持久化我们的模型。

创建文件`Context/BlogContext.cs`:

```
using Microsoft.EntityFrameworkCore;
using ASPBlog.Models;

namespace ASPBlog.Context {
    public class BlogContext : DbContext
    {
        public BlogContext(DbContextOptions<BlogContext> options) : base(options) 
        {}

        public DbSet<Post> Posts { get; set; }
    }
} 
```

在`Startup.cs`中，我们需要添加 EntityFrameworkCore 和上下文名称空间，我们还需要配置我们的数据库。

```
using Microsoft.EntityFrameworkCore;
using ASPBlog.Context;

...

        public void ConfigureServices(IServiceCollection services)
        {
            // Add framework services.
            services.AddMvc();
            services.AddDbContext<BlogContext>(options => 
                options.UseSqlite("Data Source=ASPBlog.db") 
            );
        }

... 
```

配置好模型和数据库后，我们只需运行命令来创建迁移和生成数据库。

```
dotnet ef migrations add "Initial Commit"
dotnet ef database update 
```

#### 为 Post 生成一个 CRUD

现在所有的东西都已经创建好了，我们可以生成我们的`PostsController`。运行以下命令:

`dotnet aspnet-codegenerator controller -name
PostsController -outDir Controllers -m Post -dc BlogContext`

这些是我们使用的新参数:

*   这是我们想要用来在控制器中创建动作的模型。
*   `-dc`:这是 DataContext 参数。我们正在进行 CRUD 操作，所以我们必须查看数据库和上下文

现在让我们来看看我们生成的控制器:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.Rendering;
using Microsoft.EntityFrameworkCore;
using ASPBlog.Context;
using ASPBlog.Models;

namespace ASPBlog.Controllers
{
    public class PostsController : Controller
    {
        private readonly BlogContext _context;

        public PostsController(BlogContext context)
        {
            _context = context;    
        }

        // GET: Posts
        public async Task<IActionResult> Index()
        {
            return View(await _context.Posts.ToListAsync());
        }

        // GET: Posts/Details/5
        public async Task<IActionResult> Details(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var post = await _context.Posts
                .SingleOrDefaultAsync(m => m.Id == id);
            if (post == null)
            {
                return NotFound();
            }

            return View(post);
        }

        // GET: Posts/Create
        public IActionResult Create()
        {
            return View();
        }

        // POST: Posts/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Create([Bind("Id,Title,Body")] Post post)
        {
            if (ModelState.IsValid)
            {
                _context.Add(post);
                await _context.SaveChangesAsync();
                return RedirectToAction("Index");
            }
            return View(post);
        }

        // GET: Posts/Edit/5
        public async Task<IActionResult> Edit(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var post = await _context.Posts.SingleOrDefaultAsync(m => m.Id == id);
            if (post == null)
            {
                return NotFound();
            }
            return View(post);
        }

        // POST: Posts/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> Edit(int id, [Bind("Id,Title,Body")] Post post)
        {
            if (id != post.Id)
            {
                return NotFound();
            }

            if (ModelState.IsValid)
            {
                try
                {
                    _context.Update(post);
                    await _context.SaveChangesAsync();
                }
                catch (DbUpdateConcurrencyException)
                {
                    if (!PostExists(post.Id))
                    {
                        return NotFound();
                    }
                    else
                    {
                        throw;
                    }
                }
                return RedirectToAction("Index");
            }
            return View(post);
        }

        // GET: Posts/Delete/5
        public async Task<IActionResult> Delete(int? id)
        {
            if (id == null)
            {
                return NotFound();
            }

            var post = await _context.Posts
                .SingleOrDefaultAsync(m => m.Id == id);
            if (post == null)
            {
                return NotFound();
            }

            return View(post);
        }

        // POST: Posts/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<IActionResult> DeleteConfirmed(int id)
        {
            var post = await _context.Posts.SingleOrDefaultAsync(m => m.Id == id);
            _context.Posts.Remove(post);
            await _context.SaveChangesAsync();
            return RedirectToAction("Index");
        }

        private bool PostExists(int id)
        {
            return _context.Posts.Any(e => e.Id == id);
        }
    }
} 
```

通过一行代码，我们有了一个控制器，它使用`async`和`await`来完成 CRUD 的所有操作，但是等等，还有更多。代码生成器还为这个控制器创建了一个包含所有视图的文件夹，请看`Views/Posts`文件夹。最后访问[http://localhost:5000/Posts/Create](http://localhost:5000/Posts/Create)查看结果。

就这样了，谢谢。