# easypath 现可通过 nuget 获得

> 原文：<https://dev.to/adamkdean/easyauth-now-available-via-nuget-4k3o>

现在我的新网站已经上线并开始工作了，我花了一点时间把 [EasyAuth](https://github.com/Imdsm/EasyAuth) 放到 NuGet 上，这样每个人/任何人都可以使用它，我也可以更容易地处理使用它的项目(即这个网站)。

EasyAuth 是一个简单、安全、易于使用的轻量级替代 ASP.NET 会员资格的工具，我在过去的一两个月里一直在开发它，这个网站实际上用它来进行管理认证。

目前它只支持作为数据提供者的代码优先实体框架，但我的好朋友兼学者 [@LewisArdern](https://twitter.com/LewisArdern) 目前正致力于为它添加 [RavenDB](http://ravendb.net/) 支持。

### 安装

要通过 NuGet 安装 EasyAuth，请打开您的软件包管理器控制台，并键入:

`PM> Install-Package EasyAuth`

一旦安装完毕，EasyAuth 只需很少的设置。

在`global.asax`中，你需要初始化`UserStore`并将其传递给`Authentication`类。我们现在将使用`EntityUserStore`。

请注意，可以扩展`EasyAuth.Storage.UserStoreContext`来使您的网站使用与`EntityUserStore`相同的数据库。我将在接下来的几天里添加一个教程来展示如何做，但是你可以在这个网站的[资源中找到一个例子。](https://github.com/Imdsm/PersonalWebsite)

下面的例子可以帮助你了解如何使用 EasyAuth。有任何问题，请留言评论。

### global.asax

```
namespace EasyAuthExample
{
    public class MvcApplication : System.Web.HttpApplication
    {
        // store the instance of the selected UserStore here
        static IUserStore UserStore = EntityUserStore.Instance;

        protected void Application_Start()
        {
            Authentication.UserStore = UserStore;
        }

        protected void Application_BeginRequest(Object sender, EventArgs e)
        {
            // we have to give feed the httpcontext through to the
            // auth class at the beginning of each page request
            Authentication.HttpContext = HttpContext.Current;
        }
    }
} 
```

### Web.config

确保您记得为您选择的数据存储放置 ConnectionString。

```
<connectionStrings>
    <add name="DefaultConnection"
        providerName="System.Data.SqlClient"
        connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=EasyAuthExample;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\EasyAuthExample.mdf" />
</connectionStrings> 
```

当然，您可以使用其他连接字符串/数据源，只要它们是有效的。

### HomeController.cs

现在，您可以像使用任何常规 MVC 应用程序一样创建一个控制器，但是不使用`[Authorize]`和`[AllowAnonymous]`属性，而是简单地使用`[EzAuthorize]`和`[EzAllowAnonymous]`来代替。

用`Authentication.Login(username, password)`认证用户，用`Authentication.Logout();`注销用户。

```
namespace EasyAuthExample.Controllers
{
    [EzAuthorize]
    public class HomeController : Controller
    {
        //
        // GET: /Home/

        [EzAllowAnonymous]
        public ActionResult Index()
        {
            return View();
        }

        //
        // GET: /Home/Login

        [EzAllowAnonymous]
        public ActionResult Login()
        {
            return View();
        }

        //
        // POST: /Home/Login

        [HttpPost]
        [EzAllowAnonymous]
        public ActionResult Login(LoginModel model)
        {
            if (ModelState.IsValid &&
                    Authentication.Login(model.Username, model.Password))
            {
                return RedirectToAction("MembersOnly", "Home");
            }

            ViewBag.Message = "Invalid user credentials";
            return View(model);
        }

        //
        // GET: /Home/Logout

        public ActionResult Logout()
        {
            Authentication.Logout();
            return RedirectToAction("Index", "Home");
        }

        //
        // GET: /Home/MembersOnly

        public ActionResult MembersOnly()
        {
            return View();
        }
    }
} 
```

就是这样！

## 创建、读取、更新、删除

创建、读取、更新和删除用户很容易。

### 创建用户

添加用户非常简单，现在只需要两个参数。

`void AddUser(string username, string password);`

```
// first we check that the username doesn't already exist
if (!Authentication.UserStore.UserExistsByUsername(username))
{
    // then we simply add them to the database
    Authentication.UserStore.AddUser(username, password);
} 
```

### 读取用户

您可以通过`Authentication.UserStore`使用以下方法。

```
User GetUserById(int id);
User GetUserByUsername(string username);
List<User> GetAllUsers(); 
```

示例:

```
User test = Authentication.UserStore.GetUserByUsername("TestUser");
int testId = test.UserId;

User test = Authentication.UserStore.GetUserById(14);
string testUsername = test.Username;

List<User> users = Authentication.UserStore.GetAllUsers();
foreach(var user in users)
{
    DoSomething.With(user.Username);
} 
```

### 更新用户

更新用户也很容易。确保您传递了正确的用户 Id 和用户对象！

`void UpdateUserById(int id, User user);`

```
User test = Authentication.UserStore.GetUserByUsername("TestUser");
test.Username = "IFancyANewName";
Authentication.UserStore.UpdateUserById(test.UserId, test); 
```

### 删除用户

当你不得不删除用户时，尽管很遗憾，你也可以这样做。

`void DeleteUserById(int id);`

```
User test = Authentication.UserStore.GetUserByUsername("TestUser");
Authentication.UserStore.DeleteUserById(test.UserId); 
```

尽情享受吧！