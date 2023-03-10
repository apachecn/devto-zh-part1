# 在 Lua/Love2D -敌人和战斗中制作一个射手

> 原文：<https://dev.to/jeansberg/make-a-shooter-in-lualove2d---part-3>

既然我们现在已经实现了射击，是时候给我们的游戏引入一些敌人了。在这篇文章中，我将向你展示如何添加一些具有不同行为的敌人类型。

如果您在这个阶段查看[源代码](https://github.com/jeansberg/GreatDeep/tree/3426d68ace29b3147d61e107deb22cf1c5e7d759)，您可以看到我已经向 load()函数添加了几行代码。这些都是不言自明的，所以我们不会在这里讨论它们。

## 开始新游戏

随着敌人的出现，玩家有可能被摧毁。当这种情况发生时，我们需要重置游戏。这就是为什么将 load()函数中的一些初始化逻辑移到一个名为 startGame()的函数中是一个好主意。这样我们可以随时重新开始游戏，只需调用这个函数。我们将图像加载代码和常量值留在 load()函数中。

```
function startGame()
  player = {xPos = 0, yPos = 0, width = 64, height = 64, speed=200, img=submarineImage}
  torpedoes = {}
  enemies = {}

  canFire = true
  torpedoTimer = torpedoTimerMax
  spawnTimer = 0
end 
```

Enter fullscreen mode Exit fullscreen mode

## 敌人逻辑

敌人处理代码比玩家移动和投射代码包含更多的逻辑，因为我们必须给敌人一些智能的表象。我也花了一点时间来写。请耐心听我解释它的功能。

好吧。首先是 updateEnemies()函数，它是从主 update()函数调用的。它本质上负责添加和删除对象，很像 updateTorpedoes()函数。它使用一个计时器来控制新的敌人何时出现。

```
function updateEnemies(dt)
  if spawnTimer > 0 then
    spawnTimer = spawnTimer - dt
  else
    spawnEnemy()
  end

  for i=table.getn(enemies), 1, -1 do
    enemy=enemies[i]
    enemy.update = enemy:update(dt)
    if enemy.xPos < -enemy.width then
      table.remove(enemies, i)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

> 你会注意到我又一次改变了 remove 循环。我对循环使用了一个[数字，它从头到尾遍历对象。当您需要移除循环中的对象时，这是最方便的循环类型。](https://www.lua.org/pil/4.3.4.html)

### 原型

是时候在我们的代码中引入一些面向对象的编程概念了。因为我们将创造大量的敌人，我们希望他们能够分享一些共同的属性。Lua 使用一种叫做“元表”的东西来实现基于原型的继承。我不会在这里讲述后台发生了什么——只需要知道下面的代码用一些默认值和一个构造函数定义了一个敌人原型。然后可以用所需的参数调用构造函数。如果省略了一个参数，相应的键将使用原型中定义的默认值。

```
Enemy = {xPos = love.graphics.getWidth(), yPos = 0, width = 64, height = 64}

function Enemy:new (o)
  o = o or {}
  setmetatable(o, self)
  self.__index = self
  return o
end 
```

Enter fullscreen mode Exit fullscreen mode

### 产卵的敌人

现在让我们看看如何在 spawnEnemy()函数中使用它。

我们从使用 Love2D 提供的 random()函数开始。我们首先得到一个新敌人的随机 y 位置，它总是在零和屏幕高度减去敌人高度之间。我们还要求一个介于 0 和 2 之间的数字，以便确定要繁殖的敌人的类型。然后我们调用敌人:new()函数，覆盖 yPos 参数并为“速度”和“img”键添加值。如果我们愿意，我们也可以在原型中为这些键定义默认值。我们传入的最后一个参数实际上将是一个函数，而不是一个字段。Lua 将函数视为对象，所以这不是问题。构建敌人后，我们将它添加到敌人表中，并重置产卵计时器。

```
function spawnEnemy()
  y = love.math.random(0, love.graphics.getHeight() - 64)
  enemyType = love.math.random(0, 2)
  if enemyType == 0 then
    enemy = Enemy:new{yPos = y, speed = squidSpeed, img = squidImage, update=moveLeft}
  elseif enemyType == 1 then
    enemy = Enemy:new{yPos = y, speed = sharkSpeed, img = sharkImage, update=moveToPlayer}
  else
    enemy = Enemy:new{yPos = y, speed = swordfishSpeed, img = swordfishImage, update=chargePlayer}
  end
  table.insert(enemies, enemy)

  spawnTimer = spawnTimerMax
end 
```

Enter fullscreen mode Exit fullscreen mode

[![alt text](img/4eef9004f3ec89d29145e984797c0d45.png "I'm a shark!")](https://res.cloudinary.com/practicaldev/image/fetch/s--26s_NXUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ztk2t7art0i09a9nym4r.png)[![alt text](img/5c31fc9d562abdecd651533f0d6b4ba9.png "I'm a squid!")](https://res.cloudinary.com/practicaldev/image/fetch/s--GFjWULyy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4eso2le7zegajpiwt81i.png)[![alt text](img/a327edd190741fe9090cbf012abf227b.png "I'm a swordfish!")](https://res.cloudinary.com/practicaldev/image/fetch/s--1W-fK62D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ldnhdf5bytt8mp8ck6ui.png)

### 敌方行为

我们分配给 spawnEnemy()函数中的“update”参数的三个不同的函数将定义敌人的行为。分配给每个敌人的函数将从 updateEnemies()方法中调用。

```
enemy.update = enemy:update(dt) 
```

Enter fullscreen mode Exit fullscreen mode

我会一一解释他们的逻辑。

```
function moveLeft(obj, dt)
  obj.xPos = obj.xPos - obj.speed * dt
  return moveLeft
end 
```

Enter fullscreen mode Exit fullscreen mode

moveLeft()函数是最简单的行为。它根据其速度值和增量时间更新其所属对象的 xPos 值。请注意，对象是作为第一个参数隐式传入的，我们称之为“obj”。这个功能使敌人沿直线向左移动。

```
function moveToPlayer(obj, dt)
  xSpeed = math.sin(math.rad (60)) * obj.speed
  ySpeed = math.cos(math.rad (60)) * obj.speed
  if (obj.yPos - player.yPos) > 10 then
    obj.yPos = obj.yPos - ySpeed * dt
    obj.xPos = obj.xPos - xSpeed * dt
  elseif (obj.yPos - player.yPos) < -10 then
    obj.yPos = obj.yPos + ySpeed * dt
    obj.xPos = obj.xPos - xSpeed * dt
  else
    obj.xPos = obj.xPos - obj.speed * dt
  end
  return moveToPlayer
end 
```

Enter fullscreen mode Exit fullscreen mode

函数的作用是:比较敌人和玩家的垂直位置。如果敌人的位置比玩家的位置高或低超过十个像素，敌人就会斜向玩家移动。否则它会保持直线运动。我们没有比较确切位置的原因是它会导致非常抖动的运动。我还使用了一些三角函数，以便让敌人以不太陡的角度向玩家移动。我认为这使得运动看起来不那么机械，但这是一个品味问题。

```
function chargePlayer(obj, dt)
  xDistance = math.abs(obj.xPos - player.xPos)
  yDistance = math.abs(obj.yPos - player.yPos)
  distance = math.sqrt(yDistance^2 + xDistance^2)
  if distance < 150 then
    obj.speed = chargeSpeed
    return moveLeft
  end 
  moveToPlayer(obj, dt)
  return chargePlayer
end 
```

Enter fullscreen mode Exit fullscreen mode

我敢肯定你注意到了 update()函数的结果本身就是赋给敌人的更新键，每次在 update enemy()中调用它。这个技巧的目的是允许行为函数根据某些条件返回它们自己或者另一个函数。这在 chargePlayer()中使用。我们首先计算到玩家的距离，如果小于 200，我们在增加敌人的速度后返回 moveLeft()函数。如果对象更远，我们调用 moveToPlayer()函数，然后返回 chargePlayer()函数。这会导致任何有此行为的敌人向玩家移动，并在接近时直线冲锋！

## 碰撞

我们现在已经实现了射击和敌人繁殖，但是这些机制还没有相互作用。我们需要添加一些碰撞检查代码，以便检测敌人何时被射弹击中，或者玩家何时被敌人攻击。

```
function checkCollisions()
  for index, enemy in ipairs(enemies) do
    if intersects(player, enemy) or intersects(enemy, player) then
      startGame()
    end

    for index2, torpedo in ipairs(torpedoes) do
      if intersects(enemy, torpedo) then
        table.remove(enemies, index)
        table.remove(torpedoes, index2)
        break
      end
    end
  end
end

function intersects(rect1, rect2)
  if rect1.xPos < rect2.xPos and rect1.xPos + rect1.width > rect2.xPos and
     rect1.yPos < rect2.yPos and rect1.yPos + rect1.height > rect2.yPos then
    return true
  else
    return false
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

checkCollisions()函数在主 update()循环中调用。它调用 intersects()函数来检查两个对象是否重叠。我们首先检查玩家和所有敌人之间的冲突。如果检测到碰撞，我们重新开始游戏。然后我们循环检查每一枚鱼雷，看它是否接触到敌人。如果是这样，我们既摧毁了抛射体又摧毁了敌人。
[![alt text](img/f6cb41ffb6ce7f9b82dbe7ad624009fa.png "We're under attack!")T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--SQYB7o6A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1u20p4ktiusi7doe8fh.gif)

本教程的下一部分，也可能是最后一部分，将着重于以动画和粒子效果的形式添加更多的视觉效果。如果你有任何问题或者我能解释得更好，请让我知道！

## 链接

[来源- part 3](https://github.com/jeansberg/GreatDeep/tree/3426d68ace29b3147d61e107deb22cf1c5e7d759/main.lua)
[最新来源](https://github.com/jeansberg/GreatDeep)
[love 2d wiki](https://love2d.org/wiki/Main_Page)
[Lua 参考](https://www.lua.org/manual/5.1/)