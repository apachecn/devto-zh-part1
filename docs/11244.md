# 昏暗跳跃、lve 和 Lua

> 原文：<https://dev.to/buntine/dim-jump-lve-and-lua>

我最近调查了一些 2D 游戏开发框架，其中有一个[字面 shittonne](https://en.wikipedia.org/wiki/List_of_game_engines) 。我偶然发现了一个叫[的，看起来很有前途。快速浏览文档可以清楚地看到，它没有遇到 Pygame 遇到的问题，因为它对声音管理的支持有限。主要语言是 Lua，我很熟悉它，但从未实际使用过。](https://love2d.org/)

我见过一个简单的游戏(名为[方块跳跃](http://www.lessmilk.com/games/3/))，玩家以恒定的速度在屏幕上移动，并需要跳过障碍物。我决定做一个稍微修改的版本，这样玩家也需要“闪开”一些障碍。我还决定增加难度系数的赌注！

完成后的结果是: [DIM 跳转](https://github.com/buntine/Dim-Jump)

让我知道你是否能战胜它！我的最好成绩是 150 次死亡。

创作非常有趣。我也收到了一些积极的反馈，这促使我开始计划一个更高级的游戏版本。(用于命令)等待下面发表的消息...

Love2D 框架当然得到了我的“赞许”。这是将流分成**负载**的一般模式。**拉拢**、**更新**和**退出**合作愉快。困扰我的一件事是图形变化的“全球性”。例如，为了以 16px 绘制一些红色文本，然后以 12px 绘制一些绿色文本，需要:

```
local oldR, oldG, oldB = love.graphics.getColor()
local oldFont = love.graphics.getFont()
local smallFont = love.graphics.newFont("Arial", 12)
local largeFont = love.graphics.newFont("Arial", 16)

love.graphics.setColor(255, 0, 0) -- red, green, blue
love.graphics.setFont(smallFont)

love.graphics.print("Hello", 10, 10) -- string, x, y

love.graphics.setColor(0, 255, 0)
love.graphics.setFont(largeFont)

love.graphics.print("World", 100, 10)

love.graphics.setColor(oldR, oldG, oldB)
love.graphics.setFont(oldFont) 
```

Enter fullscreen mode Exit fullscreen mode

不用说，这变得很乏味。Lua 确实支持一级函数，所以抽象混乱相对容易:

```
function withColour(r, g, b, a, f)
  local _r, _g, _b, _a = love.graphics.getColor()

  love.graphics.setColor(r, g, b, a)
  f()
  love.graphics.setColor(_r, _g, _b, _a)
end

function withFont(name, f)
  local _f = love.graphics.getFont()

  love.graphics.setFont(fonts[name])
  f()
  love.graphics.setFont(_f)
end 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以把程序重写为:

```
local smallFont = love.graphics.newFont("Arial", 12)
local largeFont = love.graphics.newFont("Arial", 16)

withColor(255, 0, 0, 0, function ()
  withFont(largeFont, function ()
    love.graphics.print("Hello", 10, 10)
  end
end)

withColor(0, 255, 0, 0, function ()
  withFont(smallFont, function ()
    love.graphics.print("World", 100, 10)
  end
end) 
```

Enter fullscreen mode Exit fullscreen mode

这让我想到了 Lua。你从盒子里得不到很多。它主要被设计成嵌入到更大的程序中。捡起它并不是太大的挑战，因为它的原型 oo 模型、一级函数和完整的词法范围与 Javascript 的相似。

真正体现 Lua 设计简单性的一点是，除了明显的原子数据结构(布尔、数字等)，Lua 只支持关联数组(或者 Lua 行话中的“表”)。数组、集合和其他集合是根据表来定义的。因此，实际上，数组实际上只是一个表:

```
{[1] = "a", [2] = "b", [3] = "c"}

-- Lua also provides syntactic sugar for this:
{"a", "b", "c"} 
```

Enter fullscreen mode Exit fullscreen mode

Lua 也非常“宽容”,有一些奇怪的语义(也像 JS 一样)。以下情况都不会导致异常:

```
coolness = {bunts=100, erik=101}

coolness.brendan -- nil
"45" + 1 -- 46
print(madeupname) -- nil

a,b,c = 1,2
print(a) -- 1
print(b) -- 2
print(c) -- nil 
```

Enter fullscreen mode Exit fullscreen mode

你想怎么做就怎么做！快乐编码...