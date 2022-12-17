# 游戏屏幕管理的替代方案-示例

> 原文：<https://dev.to/adamkdean/alternative-to-game-screen-management-an-example-318m>

为了给你一个例子来说明你能让你的代码变得多么干净，让我们看一个非常简单的菜单屏幕，它简单地要求你按下任何一个键。如你所见，它非常干净。

我也喜欢#region 的，这是自行号以来最好的东西。

更多关于我的游戏太空岩石的信息，请访问我新设计的(我正在努力！)网站位于[www.adamkdean.co.uk](http://www.adamkdean.co.uk)

(更新:游戏已不在，此贴为历史。)

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

namespace SpaceRocks
{
    class MenuScreen : IScreen
    {
        #region Variables
        private VariableService vars;

        private SpriteFont font;
        private Texture2D mainBackground;
        #endregion

        #region Constructor Methods
        public MenuScreen(Game game)
        {
            vars = ServiceExtensionMethods
                .GetService<variableservice>(game.Services);

            LoadContent();
        }

        private void LoadContent()
        {
            mainBackground = vars.Content.Load<texture2d>("Images/background");
            font = vars.Content.Load<spritefont>("Fonts/Menu");
        }
        #endregion

        #region Update Methods
        public void Update(GameTime gameTime)
        {
            UpdateInput();
        }

        private void UpdateInput()
        {
            if (Keyboard.GetState().GetPressedKeys().Length > 0)
                vars.CurrentScreen = new GameScreen(vars.Game);
        }
        #endregion

        #region Draw Methods
        public void Draw(GameTime gameTime)
        {
            vars.GraphicsDevice.Clear(Color.Black);

            vars.SpriteBatch.Begin();

            DrawBackground();
            DrawText();

            vars.SpriteBatch.End();
        }

        private void DrawBackground()
        {
            vars.SpriteBatch.Draw(mainBackground, Vector2.Zero, Color.White);
        }

        private void DrawText()
        {
            string text = "Press any key to begin..";
            Vector2 textSize = font.MeasureString(text);

            int x = vars.GraphicsDevice.Viewport.Width / 2 - (int)textSize.X / 2;
            int y = vars.GraphicsDevice.Viewport.Height / 2 - (int)textSize.Y / 2;

            vars.SpriteBatch.DrawString(font, text,
                new Vector2(x, y), Color.White);
        }
        #endregion
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于任何想知道为什么在某些代码文章的结尾有一些标签的人来说，这是因为我使用的这个很棒的脚本不能处理泛型:(