# XNA 是一个绿色代码

> 原文：<https://dev.to/adamkdean/xna-iscreen-code-5169>

我一直在做一些 XNA，为了在不使用微软版本的情况下使用某种游戏屏幕系统，我创建了这个。有些部分借鉴了互联网，比如 ServiceExtensionMethods，但是很好用。

is green . cs

```
using Microsoft.Xna.Framework;

namespace IScreenSystem
{
    public interface IScreen
    {
        void Update(GameTime gameTime);
        void Draw(GameTime gameTime);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

ServiceExtensionMethods.cs

```
using Microsoft.Xna.Framework;

namespace IScreenSystem
{
    static class ServiceExtensionMethods
    {
        public static void AddService<t>
            (this GameServiceContainer services, T service)
        {
            services.AddService(typeof(T), service);
        }

        public static T GetService<t>(this GameServiceContainer services)
        {
            return (T)services.GetService(typeof(T));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

VariableService.cs(编辑:删除了 IVariableService，因为不需要它)

```
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Content;

namespace IScreenSystem
{
    public class VariableService
    {
        public Game Game { get; set; }
        public GraphicsDeviceManager Graphics { get; set; }
        public GraphicsDevice GraphicsDevice { get; set; }
        public ContentManager Content { get; set; }
        public SpriteBatch SpriteBatch { get; set; }
        public IScreen CurrentScreen { get; set; }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

测试屏幕. cs

```
using System;
using System.Collections.Generic;
using System.Linq;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Audio;
using Microsoft.Xna.Framework.Content;
using Microsoft.Xna.Framework.GamerServices;
using Microsoft.Xna.Framework.Graphics;
using Microsoft.Xna.Framework.Input;
using Microsoft.Xna.Framework.Media;

namespace IScreenSystem
{
    class TestScreen : IScreen
    {
        private VariableService vars;

        public TestScreen(Game game)
        {
            vars = ServiceExtensionMethods
                .GetService<variableservice>(game.Services);
        }

        public void Update(GameTime gameTime)
        {
            // to change screen, simply
            // vars.CurrentScreen = new TestScreen(vars.Game);
        }

        public void Draw(GameTime gameTime)
        {
            vars.GraphicsDevice.Clear(Color.Black);
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

游戏 1.cs

```
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace IScreenSystem
{
    public class Game1 : Microsoft.Xna.Framework.Game
    {
        private VariableService vars;
        private GraphicsDeviceManager graphics;

        public Game1()
        {
            graphics = new GraphicsDeviceManager(this);
            Content.RootDirectory = "Content";
        }

        protected override void Initialize()
        {
            base.Initialize();
        }

        protected override void LoadContent()
        {
            ServiceExtensionMethods.AddService<variableservice>
                (this.Services, new VariableService());

            vars = ServiceExtensionMethods
                .GetService<variableservice>(this.Services);
            vars.Game = this;
            vars.Content = Content;
            vars.Graphics = graphics;
            vars.GraphicsDevice = graphics.GraphicsDevice;
            vars.SpriteBatch = new SpriteBatch(GraphicsDevice);
            vars.CurrentScreen = new TestScreen(this);
        }

        protected override void UnloadContent() { }

        protected override void Update(GameTime gameTime)
        {
            vars.CurrentScreen.Update(gameTime);
            base.Update(gameTime);
        }

        protected override void Draw(GameTime gameTime)
        {
            GraphicsDevice.Clear(Color.CornflowerBlue);
            vars.CurrentScreen.Draw(gameTime);
            base.Draw(gameTime);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你需要更多的解释，请留下评论。