# 在 Lua/Love2D 中制作射手-音乐和音效

> 原文：<https://dev.to/jeansberg/make-a-shooter-in-lualove2d---music-and-sound-effects>

你好，欢迎来到本教程的最后一部分！在[最后一帖](https://dev.to/jeansberg/make-a-shooter-in-lualove2d---animations-and-particles)中，我们给游戏增加了一些视觉特效。不过，在我们添加一些音频之前，它不会真的感觉完整。请继续阅读，找出方法。

## 加载一些文件

我们将使用 love.audio 模块来播放声音和音乐文件，所以我们要做的第一件事是将一些文件加载到内存中。这与加载图像文件非常相似，只是我们使用“Source”类型的对象来存储数据。

```
musicTrack = love.audio.newSource("resources/audio/Mercury.wav", "static")
musicTrack:setLooping(true)
shootSfx = love.audio.newSource("resources/audio/Explosion.wav", "static")
shootSfx:setVolume(0.5)
enemyDestroySfx = love.audio.newSource("resources/audio/Shoot.wav", "static")
playerDestroySfx = love.audio.newSource("resources/audio/Lightening.wav", "static") 
```

Enter fullscreen mode Exit fullscreen mode

我们调用 newSource()方法并提供声音文件的路径。第二个参数“static”告诉 Love2D 将整个文件读入内存。对于较大的文件，可以省略它，以便一部分一部分地传输它们。因为我使用的文件非常小，所以我决定在启动时加载所有的数据。我们希望音乐能够循环播放，幸运的是有一个名为 setLooping()的函数可以实现这一点。其中一个音频文件的音量高于其他文件，因此我们调用 setVolume()函数使其与其他文件一致。

## 播放音乐

因为音乐曲目只会播放一次，所以处理它不需要太多代码。我们要做的就是在 startGame()方法中调用`musicTrack:play()`，当玩家被击中，游戏结束时调用`musicTrack:stop()`。就是这样！

## 播放音效

我们需要添加更多的逻辑来播放音效，所以我决定创建一个可重用的函数。

```
function playSound(sound)
  sound:rewind(sound)
  pitchMod = 0.8 + love.math.random(0, 10)/25
  sound:setPitch(pitchMod)
  sound:play()
end 
```

Enter fullscreen mode Exit fullscreen mode

playSound()函数将源对象作为参数。它调用源上的 rewind()函数，这是让 Love2D 从开始播放声音效果所需要的。这可能是因为声音的所有实例都使用了相同的源。

给声音效果增加一些变化的技巧是当声音被触发时稍微改变它的频率。我们生成一个介于 0.8 和 1.2 之间的 pitchMod 值，并将其提供给 setPitch()函数。然后我们调用 sound:play()就像开始音乐曲目的时候一样。

之后，只需在代码中适当的位置调用 playSound()即可。

```
playerAlive = false
musicTrack:stop()
playSound(playerDestroySfx) 
```

Enter fullscreen mode Exit fullscreen mode

当玩家被敌人击中时，停止音乐并播放巨大的爆炸声音。

```
table.remove(enemies, index)
table.remove(torpedoes, index2)
playSound(enemyDestroySfx) 
```

Enter fullscreen mode Exit fullscreen mode

当敌人被鱼雷击中时播放类似的声音。

```
torpedoTimer = torpedoTimerMax
playSound(shootSfx) 
```

Enter fullscreen mode Exit fullscreen mode

每当产生鱼雷时发出嗖嗖声。

## 结论

使用 Lua/Love2D 制作射手系列到此结束！我希望这些帖子对你有用。在未来的某个时候，我将使用相同的框架编写一些更高级的教程。如有问题和意见，请随时联系我。

## 链接

[最新来源](https://github.com/jeansberg/GreatDeep)
[love 2d wiki](https://love2d.org/wiki/Main_Page)
[Lua 参考](https://www.lua.org/manual/5.1/)

## 学分

*   这部分用的音乐轨道是由 *SketchyLogic* 制作的，由这里的[主持](https://opengameart.org/content/nes-shooter-music-5-tracks-3-jingles)。
*   音效由*巴特*制作，可以在[这里](https://opengameart.org/content/8-bit-platformer-sfx)找到。