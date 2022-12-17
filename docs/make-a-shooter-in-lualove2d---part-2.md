# 在 Lua/Love2D 中制作射手-发射射弹

> 原文：<https://dev.to/jeansberg/make-a-shooter-in-lualove2d---part-2>

在这个系列的第一部分中，我们写了一些代码来处理玩家的输入和潜艇射击的动作。但是如果你不能开枪，打他们又有什么用呢？一点都不好，就是这样！在这篇文章中，我将向你展示如何让我们的小潜艇发射一些鱼雷。

## 初始化

首先，我们将对 load()方法进行一些添加和更改。

```
function love.load()
  submarineImage = love.graphics.newImage("resourcimg/submarine.png")
  torpedoImage = love.graphics.newImage("resourcimg/torpedo.png")

  player = {xPos = 0, yPos = 0, width = 64, height = 64, speed=200, img=submarineImage}
  torpedoes = {}

  canFire = false
  torpedoTimerMax = 0.2
  torpedoTimer = torpedoTimerMax
  torpedoStartSpeed = 100
  torpedoMaxSpeed = 300
end 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，我们现在正在加载另一个图像，它将在绘制鱼雷时使用。此外，所有与玩家相关的信息现在都被分组到一个叫做表格的东西中。表格是一种很好的数据分组方式，并且很容易创建。创建一个表只需要一对括号，用等号括起一些键和值。为了以后访问其中一个值，您只需键入对象的名称，后跟一个点和相关键的名称。我们还添加了一个空表来跟踪屏幕上的所有鱼雷。最后，我们添加了一些变量，用于控制发射机制和鱼雷本身。

## 让我们再画一些东西

draw()函数的第一个新增功能告诉引擎使用蓝色绘制一个与屏幕大小相同的矩形。第二个 setColor()调用用于在额外调用 draw()函数之前重置颜色。

```
function love.draw()
  love.graphics.setColor(186, 255, 255)
  background = love.graphics.rectangle("fill", 0, 0, love.graphics.getWidth(), love.graphics.getHeight())
  love.graphics.setColor(255, 255, 255)

  love.graphics.draw(player.img, player.xPos, player.yPos, 0, 2, 2)
  for index, torpedo in ipairs(torpedoes) do
    love.graphics.draw(torpedo.img, torpedo.xPos, torpedo.yPos)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在使用我们创建的新“玩家”表来绘制玩家。我们还需要画出鱼雷，这就是另一个 Lua 结构的用武之地，即“for 循环”。这个循环允许我们遍历当前列表中的所有鱼雷，并在它们当前的位置画出它们。Lua 允许您在创建循环时命名两个变量。第一个将保存当前鱼雷的索引，第二个将保存值。我选择简单地称这些为“指数”和“鱼雷”。鱼雷本身是表格，包含对鱼雷图像的引用以及它们的水平和垂直位置的值。我们将很快创建一些！

## 让我们再更新一些东西

在 update()函数中编写所有的游戏逻辑会导致在我们添加一些新功能后，代码变得非常难以维护。这就是为什么我决定将玩家对象和鱼雷的逻辑提取到两个独立的函数中。这些函数将相同的增量时间值作为参数转发。

```
function love.update(dt)
  updatePlayer(dt)
  updateTorpedoes(dt)
end 
```

Enter fullscreen mode Exit fullscreen mode

### 多输入处理

对于 updatePlayer()函数，我稍微重构了键盘输入代码，将按下的方向键存储在单独的变量中。我还使用了新的“玩家”桌。为了简洁起见，我省略了上面代码片段中的球员移动代码。

```
function updatePlayer(dt)
  down = love.keyboard.isDown("down")
  up = love.keyboard.isDown("up")
  left = love.keyboard.isDown("left")
  right = love.keyboard.isDown("right")

  --Player movement--

  if love.keyboard.isDown("space") then
    torpedoSpeed = torpedoStartSpeed
    if(left) then
      torpedoSpeed = torpedoSpeed - player.speed/2
    elseif(right) then
      torpedoSpeed = torpedoSpeed + player.speed/2
    end
    spawnTorpedo(player.xPos + player.width, player.yPos + player.height/2, torpedoSpeed)
  end

  if torpedoTimer > 0 then
    torpedoTimer = torpedoTimer - dt
  else
    canFire = true
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

由于玩家现在可以射击，我们需要检查另一个键。我选择了空格键。当按键被按下时，我们首先检查玩家是向左还是向右移动，如果是，我们允许鱼雷受玩家速度的影响。我认为它给弹丸一个更好的感觉，但这是一个味道的问题。计算完速度后，我们调用另一个新函数 speed 鱼雷()，如下所述。还记得我们在 load()函数末尾定义的那些变量吗？我们现在用它们来控制鱼雷的发射速度。我们让计时器倒计时，直到它到达零。然后，我们将“canFire”变量设置为 true。

### 创建和更新鱼雷

在 spawn 鱼雷()函数中，我们检查变量 canFire，只有当它被设置为 true 时才生成鱼雷。我们简单地创建一个包含位置、大小、速度和图像值的表，并将其添加到鱼雷表中。然后我们重置“canFire”变量和计时器。

```
function spawnTorpedo(x, y, speed)
  if canFire then
    torpedo = {xPos = x, yPos = y, width = 16, height=16, speed=speed, img = torpedoImage}
    table.insert(torpedoes, torpedo)

    canFire = false
    torpedoTimer = torpedoTimerMax
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们要写的最后一件事是 updateTorpedoes()函数。我们编写另一个循环来遍历所有的鱼雷并更新它们的位置。我们让鱼雷从初始速度加速到 load()函数中定义的最大速度。这使得他们的行为在我看来更加真实。一旦鱼雷离开屏幕，我们就不再对它感兴趣，所以我们把它设置为零。在 Lua 中，这基本上意味着表对象不再存在，因此它将不再被更新或绘制。

```
function updateTorpedoes(dt)
  for index, torpedo in ipairs(torpedoes) do
    torpedo.xPos = torpedo.xPos + dt * torpedo.speed
    if torpedo.speed < torpedoMaxSpeed then
      torpedo.speed = torpedo.speed + dt * 100
    end
    if torpedo.xPos > love.graphics.getWidth() then
      --torpedo = nil -does not actually work-
      table.remove(torpedoes, index)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

编辑:我误解了从表中删除项目的事情。要真正删除某个内容，您需要调用 table.remove()函数，提供要删除的内容的表和索引。
[![alt text](img/080ca1bab1838686d4db22be7f0dc897.png "Pew pew pew!")](https://res.cloudinary.com/practicaldev/image/fetch/s--2LQ764W6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12poliuah8c4x29euoux.gif) 
接下来我们将放出一些敌人来射击！
[第三部分](https://dev.to/jeansberg/make-a-shooter-in-lualove2d---part-3)

## 链接

[来源- part 2](https://github.com/jeansberg/GreatDeep/tree/4df335316152a6e3a548929d659de6fd7e0d1a22/main.lua)
[最新来源](https://github.com/jeansberg/GreatDeep)
[love 2d wiki](https://love2d.org/wiki/Main_Page)
[Lua 参考](https://www.lua.org/manual/5.1/)