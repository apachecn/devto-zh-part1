# XNA 使用变量服务的示例对象

> 原文：<https://dev.to/adamkdean/xna-an-example-object-using-variableservice-mjd>

这只是一个你如何设计一个对象类的简单例子，你可以用它来设计游戏对象，玩家对象，任何你需要独立更新和绘制的东西。

```
using System;
using Microsoft.Xna.Framework;
using Microsoft.Xna.Framework.Graphics;

namespace SpaceRocks
{
    public class ExampleObject
    {
        #region Variables
        private VariableService vars;

        public Texture2D Texture { get; set; }
        public int Width { get { return Texture.Width; } }
        public int Height { get { return Texture.Height; } }

        public Vector2 Position { get; set; }
        #endregion

        #region Constructor Methods
        public ExampleObject(Game game)
        {
            vars = ServiceExtensionMethods
                .GetService<variableservice>(game.Services);
        }

        public void Initialize(Texture2D texture, Vector2 position)
        {
            Texture = texture;
            Position = position;
        }
        #endregion

        #region Update Methods
        public void Update(GameTime gameTime)
        {
            //
        }
        #endregion

        #region Draw Methods
        public void Draw(GameTime gameTime)
        {
            //
        }
        #endregion
    }
} 
```

Enter fullscreen mode Exit fullscreen mode