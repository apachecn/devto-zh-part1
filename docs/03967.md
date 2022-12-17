# 如何在 Python 中修复一个死循环？

> 原文：<https://dev.to/crhodes2/how-to-fix-an-endless-loop-in-python-c3p>

你好，

这是我第一次来 dev.to。我需要一些帮助来修复一个代码。
参见我正在从头开始学习 Python，并在 Visual Studio 2015 中使用 Python 创建了这个随机掷骰子的简单游戏程序。

但是在我的项目中出现了两个问题。第一个问题是骰子并不是随机滚动的。它会给我一个随机数，但这个数字不会改变，直到我关闭程序，只有当我关闭程序，它会给我另一组随机的数字。

我发现的第二个问题是，当我运行代码时，它进入了一个死循环。我不知道如何才能让这个循环永无止境，但我不知道如何停止它。如果有人能帮我改正错误，我会非常感激。

请在这里找到我的代码。

```
import random
min = 1
max = 6
dice = random.randint(1, 6)
dice2 = random.randint(1, 6)

roll_again = "yes"

while roll_again == "yes" or roll_again == "y":
    print "Rolling the dices..."
    print "The values are...."
    print dice
    print dice2

if(dice + dice2 == 6):
    print("You rolled a 6! You have been cursed!")
elif(dice + dice2 == 7):
    print("You rolled a 7! You have been blessed with money")
else:
    print("You don't have any blessings")

roll_again = raw_input("Roll the dices again?") 
```

Enter fullscreen mode Exit fullscreen mode

提前感谢。