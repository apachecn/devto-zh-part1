# 用 Bash 自动化你的键盘背光

> 原文：<https://dev.to/raymelon/automating-your-keyboards-backlit-with-bash>

你有一个键盘，它在晚上有背光，但在白天没有。你节省了一点能量，而且你的键盘在晚上看起来很酷。太好了！

### 但是...等一下。

我最近在学习 Bash，我想与你分享我的学习经验也会对我的写作经验有所帮助。请原谅我。

### 要回去了...

所以我使用了一个简单的算法，让我的键盘在 Bash 上变得更加智能。Bash 的语法对我来说有点陌生，我花了一天时间才习惯。但是当我可以用一种我熟悉的语言(比如 Python)来做的时候，为什么要 Bash 呢？嗯，时不时地让自己接触新习惯可以锻炼大脑的神经可塑性，这很好。

这是背光的简单算法的工作原理，

生活在赤道附近，我们每天有整整 12 个小时的白天/黑夜。因此，我的结论是，我的键盘在下午 6 点到早上 6 点之间必须是凉爽的，在早上 6 点到下午 6 点之间必须是环保的。

因此，给我们这个逻辑流程:

| 一天中的时间 | 将背光切换到 | 什么时候再切换背光？ |
| --- | --- | --- |
| 上午 6 时至下午 5 时 59 分 | 离开 | 接下来的 6 小时 |
| 下午 6 点至早上 5 点 59 分 | 在…上 | 接下来的 6 小时 |

好吧，那我们怎么计算接下来的 6 个小时？

为了简化事情，我们需要使用军事时间。计算到第 24 小时更容易。但是由于我的电脑里时间是 12 小时制，我调整了我的公式...

| 一天中的时间 | 下一个背光开关 | 下一个背光开关的公式(其中`h`是一天中的时间) |
| --- | --- | --- |
| 上午 6 时至下午 5 时 59 分 | 下午 6 点 | 18 - `h` |
| 晚上 6 点至 11 点 59 分 | 早上 6 点 | (12 - `h` ) + 6 |
| 12 点至 6 点 | 早上 6 点 | 6 - `h` |

但是等等，为什么是那些公式？

因为下午 6 点是一天中的第 18 个小时。第二个公式与第一个公式相同，我只是把它放在时间在 12 点重置回 0 的上下文中。我想，为了更清楚地了解算法。

好了，现在键盘知道什么时候该切换了。太好了。

但是等等...

它应该如何切换...卷轴锁？

嗯，对于某些键盘，按下滚动锁定键会触发背光。

对于大多数 Linux 机器，你可以使用`xset`命令来控制你的键盘 led。

来自 [`xset`手册页](//ftp://www.x.org/pub/X11R7.5/doc/man/man1/xset.1.html):

> **led**
> 
> led 选项控制键盘 led。这控制一个或所有 led 的打开或关闭。它接受可选的整数、前导破折号(-)或“开/关”标志...
> 
> ...例如，打开滚动锁定 LED:
> `xset led named "Scroll Lock"`

酷毙了。

现在我们有了所有需要的东西，我们可以写代码了。

```
while true; do current_hr=$((10#$(date +'%H')))
    current_min=$((10#$(date +'%M')))
    six_am=6
    six_pm=18

    # sunrise
    if [[ current_hr -eq six_am ]]; then next_check=43200 # 12 hrs in s
        xset -led named 'Scroll Lock'

        echo 'Next check at' $(date -d "now + $next_check seconds")',' 'approx' $(($next_check/60/60)) 'hrs from now.'
        sleep $next_check

    # sunset
    elif [[ current_hr -eq six_pm ]]; then next_check=43200 # 12 hrs in s
        xset led named 'Scroll Lock'

        echo 'Next check at' $(date -d "now + $next_check seconds")',' 'approx' $(($next_check/60/60)) 'hrs from now.'
        sleep $next_check

    # midnight till before sunrise
    elif [[ current_hr -lt six_am ]]; then min_to_six=$(($((10#$six_am*60 + 10#0)) - $((10#$current_hr*60 + 10#$current_min))))
        next_check=$(($min_to_six * 60))
        xset led named 'Scroll Lock'

        echo 'Next check at' $(date -d "now + $next_check seconds")',' 'approx' $(($next_check/60/60)) 'hrs from now.'
        sleep $next_check

    # after sunrise till sunset
    elif [[ current_hr -lt six_pm ]]; then min_to_six=$(($((10#$six_pm*60 + 10#0)) - $((10#$current_hr*60 + 10#$current_min))))
        next_check=$(($min_to_six * 60))
        xset -led named 'Scroll Lock'

        echo 'Next check at' $(date -d "now + $next_check seconds")',' 'approx' $(($next_check/60/60)) 'hrs from now.'
        sleep $next_check

    # after sunset till before sunrise
    elif [[ current_hr -gt six_pm ]]; then hrs_to_twelve_am=$(($((10#24*60 + 10#0)) - $((10#$current_hr*60 + 10#$current_min))))
        min_to_six=$(($((10#$hrs_to_twelve_am + 10#0)) + $((10#6*60 + 10#0))))

        next_check=$(($min_to_six * 60))
        xset led named 'Scroll Lock'

        echo 'Next check at' $(date -d "now + $next_check seconds")',' 'approx' $(($next_check/60/60)) 'hrs from now.'
        sleep $next_check
    fi

done 
```

Enter fullscreen mode Exit fullscreen mode

请随时纠正我或建议更简单的方法。我会很高兴和感激向你学习。；)

这个脚本也托管在 Github 中，作为我个人和个性化 Bash 脚本的一部分。
[https://github . com/ray melon/bash-wrappers/blob/master/auto-scrck](https://github.com/raymelon/bash-wrappers/blob/master/auto-scrlck)

干杯！

*这篇文章也发表在[媒体](https://medium.com/@raymelon/automating-your-keyboards-backlit-with-bash-e232cb100150)上。*