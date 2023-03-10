# 建造一只语音控制的拍打鸟

> 原文：<https://dev.to/burningion/building-a-voice-controlled-flappy-bird-17hm>

[https://www.youtube.com/embed/Rq75xBkWgfQ](https://www.youtube.com/embed/Rq75xBkWgfQ)

Flappy Bird 是一个非常简单的游戏，用户可以点击他们的手机屏幕，让小鸟“扇动”，并避免管道。这是一款出乎意料的成功游戏，在被 YouTube gaming commnunity 评为特别令人沮丧的游戏后，它迅速走红。

在今天的帖子中，我们将重新制作 Flappy Bird，但这一次，我们将使用音高检测和音域来使它成为一个语音控制的唱歌游戏。我们的鸟将在我们唱的当前音符的水平上飞行。

因为我们将处理音符，这篇文章将介绍音乐理论，然后跳转到构建音乐游戏的一些基本工具。

如果你曾经演奏过像 Rocksmith 或 Rock Band 这样的音乐，我们将使用同样的音高检测和音符关联来给我们的玩家反馈，告诉他们已经击中了正确的音符。

[![](img/9a1b797743fbe3bbadbb22a854dc071d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FOu6TUaf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2dutipaqas0t4plwer0d.png)

## 音乐理论的一个(简短)背景

[![](img/10e1898392be7c098ddaa75afe8c3b80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--giBlHJf8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z8z953sg1zfswm64hai9.png)

在我们深入研究代码之前，我们可能应该先谈谈人类对声音的感知，以及音乐在西方是如何工作的(2017)。

总的来说，对于大多数西方音乐，我们从一个完全任意的中心开始。

总的来说，我们决定 A4 等于 440Hz。这意味着笔记的所有其他**位置**都与此相关。用 440 赫兹的 A4，然后我们在它周围创造所有其他的音符。

从这里开始，我们有了所谓的“八度音阶”。每次我们将这个频率加倍或减半(880Hz，220Hz)，我们都会得到相同的音符 A，但要么高一个八度，要么低一个八度。当我们往下走时，我们从第二个数字中减去，所以 220Hz 是 A3，当我们向上走一个八度时，我们增加这个数字。(880Hz 为 A5)。

每个八度音程然后被分解成十二个部分。这 12 个声部中的每一个都应该与下一个声部距离相等，以八度音结束。我们称这些为“半步”。

在下一个八度音阶，整个过程又循环了一遍。

你会注意到有些笔记的结尾有`#`。这些代表“升半音”，或在音符之间。除了 B 和 C，E 和 f，大多数音符都有中间音。这些音符是独特的，因为它们彼此相距半步。

**我们从 C 开始音阶，而不是 a。**这是为什么？事实证明，音乐已经是一个多世纪的发明，而音符是在我们谈论的半音音阶中被发明出来的。所以我们回去把我们的新音阶放在已经存在的音乐符号上。

你可以在这里看到一个很棒的视频，解释这个令人困惑的设置[的原因。](https://www.youtube.com/watch?v=NRDwrKMan_Q)

随着我们进入更高的八度音阶，以赫兹为单位的音符之间的距离变得越来越大。例如，A1 和 A2 之间的距离只有 55 赫兹，但 A5 和 A6 之间的距离是 880 赫兹！如果我们要看音符之间的距离，我们需要适应八度音阶来测量距离。

那么，音乐理论对实际音符之间的所有“间隙”有什么看法呢？我们如何管理它们，我们如何标记来自不完美歌手的“中间”音符？

## 引入分音，或百分之一的半音

[![](img/5a340f8632a007e88d026c70015a2358.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--k7uhniut--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x8p3xgnctx0qsm8dcfsb.png)

“分”是半音之间的度量单位。使用音分，我们可以知道一个音符离“完美音高”或我们音符的理想声音有多远。

一分是半音的百分之一。因此，使用音分，我们可以知道我们离理想音符有多远，不管我们在哪个八度音阶上工作。不管我们的八度音程是多少，每个音都应该是均匀分布的，因为每个半音都是不同的，取决于我们的八度音程。

音分的正确实现应该允许我们测量我们的歌手离正确的音高有多近或多远，不管他们唱的是哪个八度。

## 在音高和音符之间转换

[![](img/420b3bc6be67dab1bb6b563419b76955.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NU_RQe58--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8abx4ewzmaismd1sivn0.png)

[aubio](https://aubio.org/) ，我们在[视频合成器](https://www.makeartwithpython.com/blog/video-synthesizer-in-python/)帖子中介绍的音高检测框架，有一个音高检测方法。此方法返回音频输入检测到的主音频率。

使用这个以赫兹为单位返回的音高，我们就可以使用一个音乐框架(在本例中为 [Music21](http://web.mit.edu/music21/) )将我们的频率转换成一个音符。

有了这个音符，我们就可以测量我们的频率离理想音高有多远，单位是分。我们可以为我们的软件设置一个保持基调的阈值，并在我们的玩家没有唱正确的音符时提供视觉反馈。

将 [Music21](http://web.mit.edu/music21/) 中的频率转换成音符的代码如下所示:

```
import music21

a4 = music21.pitch.Pitch() # create a Pitch object a4.frequency = 440 # set the pitch in Hertz 
print(a4.nameWithOctave) # prints 'A4' 
```

Enter fullscreen mode Exit fullscreen mode

额外的好处是，当我们的频率下降一点时，我们可以直接从新的`Pitch`对象中得到，单位如下:

```
bad_note = music21.pitch.Pitch()
bad_note.frequency = 488
print(bad_note.microtone.cents) # prints -20.74, number of cents from closest note print(bad_note.nameWithOctave)  # in this case, B4 is closest note 
```

Enter fullscreen mode Exit fullscreen mode

## 发现用户的音域

[![](img/6809b0ef4db6d61ccfc214b15d4e7736.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Z8-Di2S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wfuqjxpv7hh73p9t8wxd.png)

每个人都有自己的“音域”。一个人的音域可以在三个到目前最多十个八度之间。

为了让我们的玩家能够舒服地唱歌，我们需要检测他们的音域，让他们在自己的框架内工作，从低音到高音。

为了做到这一点，我们需要让他们尽可能唱出最低的音符，同时检查特定的中心偏差，这样我们就知道他们能够真正把握住他们试图唱的那个音符。

在我们检测到最低的音符后，我们可以抓住他们能唱的最高音符，再次使用我们允许的一组分差的标准。

## 设置音域上的空间

[![](img/2b2d736b6c6c123bc2fe380a504a3217.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--dsUKns1I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e13c45hedp697kdw4j6s.png)

如果我们想让我们的视频游戏音乐化，它需要遵循某种音乐结构。我们能做到的最基本的方法是使用所谓的标尺。

在我们的例子中，我们将使用最基本的音阶，c 大调音阶。我说这很简单，因为它就是我们的一组音符中没有“#”的所有音符。

所以我们可以把我们的管道空间设置在所有主要音符都落下的地方。通过这个，我们将有一个音乐声音的基本开始。

音乐通过一组音程或与它所在的基本音符的距离来工作。我们可以用它来为我们的游戏创造自动音乐。

## 大型项目从何开始

![](img/012b7ae34d1d8a3008283405c381a408.png)

那么，有了这些理论，你该如何开始呢？

对于像这样更大、更复杂的程序，最好的办法是从最小的开始。

忘记所有其他的问题，从你首先需要的事情开始，然后再去做其他的事情。

对我来说，就是找出一个人的音域。所以，让我们先这样做。我们需要从麦克风接收一个人的声音，然后我们需要他们唱一个低音，保持住，然后唱一个高音，保持住。这样，我们将需要两个音符，这样我们就可以把它们发送到我们的游戏中，我们的用户将尝试在两个音符之间唱偶数音符。

一旦我们得到了这些，我们就需要一种方法来辨别一个音符在一个人的音域中的位置。然后，我们可以将屏幕上的位置映射到一个人能够唱的音符上。有了这个，我们就可以把他们的声音变成控制器了。

## 编写音域检测器

[![](img/d26ed64d8359a51ca54ccc6a8f2d4759.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--osJaznl2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/atw0gn605x6e309pxxv1.png)

既然音域检测器是我们需要的第一件东西，我们应该从它开始。

我们要打开麦克风，然后用它来传入 aubio 的音高检测程序。一旦我们得到了赫兹的音高，我们将使用 Music21 的音符功能将其转换成音高。

然后，Music21 的 pitch 对象会自动将音高的名称设置为最近的音符，并使用它的`nameWithOctave`变量。如果音高不完美，它也会有一个 set `microtone.cents`，与完美音高的偏差是多少分。

我们可以使用微音分来检测我们的歌手是否没有发出正确的音符，并确定我们是否应该让他们在他们的音域内使用这个音符。

我们还需要设定他们保持音符的持续时间。这会使程序的使用变得非常困难，所以要明智地选择。在我的例子中，我以一排 20 个样本的`note_hold`结束，它们都是同一个调，分的范围设置得相当宽松。

下面是代码的样子:

```
import aubio
import numpy as np
import pyaudio

import time
import argparse

import music21 # yes! 
parser = argparse.ArgumentParser()
parser.add_argument("-input", required=False, type=int, help="Audio Input Device")
args = parser.parse_args()

if not args.input:
    print("No input device specified. Printing list of input devices now: ")
    p = pyaudio.PyAudio()
    for i in range(p.get_device_count()):
        print("Device number (%i): %s" % (i, p.get_device_info_by_index(i).get('name')))
    print("Run this program with -input 1, or the number of the input you'd like to use.")
    exit()

# PyAudio object. p = pyaudio.PyAudio()

# Open stream. stream = p.open(format=pyaudio.paFloat32,
                channels=1, rate=44100, input=True,
                input_device_index=args.input, frames_per_buffer=4096)
time.sleep(1)
# Aubio's pitch detection. pDetection = aubio.pitch("default", 2048,
    2048//2, 44100)
# Set unit. pDetection.set_unit("Hz")
pDetection.set_silence(-40)

def get_vocal_range(volume_thresh=0.01, cent_range=20, note_hold=20):

    note_curr = 0 # counter for how many consistent samples while recording
    range_low = "" # name of note we achieved at lowest range
    range_high = "" # name of note achieved at highest 

    have_range = False

    previous_note = ""
    current_pitch = music21.pitch.Pitch()

    while not have_range:

        data = stream.read(1024, exception_on_overflow=False)
        samples = np.fromstring(data,
                                dtype=aubio.float_type)
        pitch = pDetection(samples)[0]

        # Compute the energy (volume) of the
        # current frame.
        volume = np.sum(samples**2)/len(samples) * 100

        # You can uncomment the volume below to make sure the threshold matches
        # your microphone's threshold
        #print(volume) 
        if pitch and volume > volume_thresh: # adjust with your mic! .0002 if for my telecaster, .001 for my mic
            current_pitch.frequency = pitch
        else:
            continue

        if current_pitch.microtone.cents > cent_range:
            print("Note %s outside of Cent Range with %i" %
                  (current_pitch.nameWithOctave, current_pitch.microtone.cents))
            previous_note = ""
            continue

        current = current_pitch.nameWithOctave

        if current == previous_note:
            note_curr += 1
            if note_curr == note_hold:
                if range_low != "" and range_low != current:
                    range_high = current
                    have_range = True
                    print("got range of high")
                else:
                    range_low = current
                    print("got range of low")
        else:
            note_curr = 0
            note = current
            previous_note = current
            print(current)

    return range_low, range_high

min_note, max_note = get_vocal_range()
print("total range: %s to %s" % (min_note, max_note)) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，还有一点我还没有提到，你的麦克风的音量阈值。

在我的情况下，我的麦克风有音频进来，音量大约为`.001`。您可能需要通过取消注释`print(volume)`行来调整它以适合您的麦克风，并在您开始对着麦克风唱歌时观察变化。

我们在这里寻找的是足够高的音量阈值，只有当我们实际唱歌时才尝试音高检测，而不是当我们的麦克风拾取背景噪声时。

## 计算当前音符在我们音域上的位置

[![](img/1c905bf15071b9fbac219f57fd19f870.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XE8-r_nI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3q0yq5u4m4b3qgtsk8ip.png)

现在，有几种不同的方法可以计算用户在音域上的位置。

首先，我们可以将整个音域直接映射到屏幕上。如果我们的用户唱了一个音符，我们得到这个音符，并计算他们能唱的最低音符的距离。我们应该有一个以分为单位的距离。

使用这个距离(以分为单位),我们可以用它除以这个人能唱的从最低到最高音符的总音程(以分为单位)。然后我们会有一个从 0 到 1 的数字，每个音符代表中间的一个小数。

然后，我们可以通过乘以屏幕的高度分辨率，将这个数字直接映射到屏幕上。每个音符会直接映射到我们的屏幕上。

但是这里有一个交易，因为这个数字在音乐上并没有真正的意义。它只是代表一个空间，为了让它有音乐感，我们必须让我们的危险代表我们希望用户唱的特定音符在屏幕上的位置。

现在，让我们使用当前音符的位置作为一个从 0 到 1 的数字:

```
# code continues from above 
def position_on_range(low_note, high_note, volume_thresh=.0001, cent_range=5):
    lowNote = music21.note.Note(low_note)
    highNote = music21.note.Note(high_note)

    vocalInterval = music21.interval.notesToInterval(lowNote, highNote)

    current_pitch = music21.pitch.Pitch()

    while True:

        data = stream.read(1024, exception_on_overflow=False)
        samples = np.fromstring(data,
                                dtype=aubio.float_type)
        pitch = pDetection(samples)[0]

        # Compute the energy (volume) of the
        # current frame.
        volume = np.sum(samples**2)/len(samples)

        if pitch and volume > volume_thresh: # adjust with your mic! .0002 if for my telecaster, .001 for my mic
            current_pitch.frequency = pitch
        else:
            continue

        if current_pitch.microtone.cents > cent_range:
            #print("Outside of Cent Range with %i" % current_pitch.microtone.cents)
            previous_note = ""
            continue

        current = current_pitch.nameWithOctave

        cur_interval = music21.interval.notesToInterval(lowNote, current_pitch)
        print(cur_interval.cents / vocalInterval.cents)

position_on_range(min_note, max_note) 
```

Enter fullscreen mode Exit fullscreen mode

计算当前音高位置的代码与检测我们的初始音域基本相同。我们增加的唯一新东西是使用`music21`的`notesToInterval`计算间隔。这给了我们一个区间，这里我们可以使用的度量单位之一是分。

我们并不把这个数字放在任何地方，而是把它打印到屏幕上。稍后，我们将把这个数字放在一个`Queue`上，这样我们的声音检测可以在它自己的线程上运行，我们的游戏可以`pop`把所有唱过的音符放到屏幕上我们的小鸟所在的位置。

## 分离我们的音频和游戏代码

[![](img/e12d900704ff66d1b2c2dc104bd5a56d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tCEaBloA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/riqd0l8uaf8tbkepk2es.png)

如果我们的音频代码要在它自己的线程上运行，我们真的应该把它从我们的游戏代码中分离出来。我们可以很容易地做到这一点，在音频文件中创建一个`Queue`对象，然后将`Queue`导入到我们的主游戏循环中。

从这里，我们还可以添加下面的代码，这样当我们导入我们的`get_vocal_range`和`position_on_range`函数时，我们的函数就不会自动运行。

这可以放在我们程序的末尾，当我们想把用户的声音作为输入时，它会把我们的原型程序变成一个可重用的库:

```
if __name__ == '__main__':
    low_note, high_note = get_vocal_range()
    position_on_range() 
```

Enter fullscreen mode Exit fullscreen mode

最后，我导入`Queue`，并在程序中创建一个名为`q`的`Queue`。然后我确保`q.put`我们的音域间隔，而不是打印出来。

然后，我们可以从主程序中导入 q，并在它们进入自己的线程时获取每个值。

## 编写游戏代码

[![](img/f82e31214891af98a234ba88339afa10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hV6NEORi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7g5efeprm06jq1bc7ps3.png)

因为这篇文章主要是解释如何将声音作为一种输入输入到我们的程序中，所以我不会过多关注游戏代码的细节。

我从伟大的 [FlapPyBird](https://github.com/sourabhv/FlapPyBird) 克隆开始，并取得图像资产。

从这里开始，我用检查队列中是否有任何东西的循环替换了“flap”函数调用。如果有，我们就把鸟放在屏幕上的位置，这个位置与发声范围内的当前位置相匹配。

我还没有添加一个当玩家敲击管道时的处理程序，或者让管道有音乐感。目前，它们只是随机繁殖。

```
from voiceController import get_vocal_range, position_on_range, q, stream

from threading import Thread

import pygame
import random
from itertools import cycle

PIPEGAPSIZE = 100
screenWidth, screenHeight = 288, 512
screen = pygame.display.set_mode((screenWidth, screenHeight)) 

clock = pygame.time.Clock()

bird = ('images/redbird-downflap.png',
        'images/redbird-midflap.png',
        'images/redbird-upflap.png')

background = 'images/background-day.png'
pipe = 'images/pipe-green.png'

IMAGES = {}
HITMASKS = {}
IMAGES['background'] = pygame.image.load(background).convert()
IMAGES['player'] = (
    pygame.image.load(bird[0]).convert_alpha(),
    pygame.image.load(bird[1]).convert_alpha(),
    pygame.image.load(bird[2]).convert_alpha(),
)

IMAGES['pipe'] = (
    pygame.transform.flip(
        pygame.image.load(pipe).convert_alpha(), False, True),
    pygame.image.load(pipe)
)

IMAGES['base'] = pygame.image.load('images/base.png').convert_alpha()
BASEY = screenHeight * 0.89

def checkCrash(player, upperPipes, lowerPipes):
    """returns True if player collders with base or pipes."""
    pi = player['index']
    player['w'] = IMAGES['player'][0].get_width()
    player['h'] = IMAGES['player'][0].get_height()

    # if player crashes into ground
    if player['y'] + player['h'] >= BASEY - 1:
        return [True, True]
    else:

        playerRect = pygame.Rect(player['x'], player['y'],
                      player['w'], player['h'])
        pipeW = IMAGES['pipe'][0].get_width()
        pipeH = IMAGES['pipe'][0].get_height()

        for uPipe, lPipe in zip(upperPipes, lowerPipes):
            # upper and lower pipe rects
            uPipeRect = pygame.Rect(uPipe['x'], uPipe['y'], pipeW, pipeH)
            lPipeRect = pygame.Rect(lPipe['x'], lPipe['y'], pipeW, pipeH)

            # player and upper/lower pipe hitmasks
            pHitMask = HITMASKS['player'][pi]
            uHitmask = HITMASKS['pipe'][0]
            lHitmask = HITMASKS['pipe'][1]

            # if bird collided with upipe or lpipe
            uCollide = pixelCollision(playerRect, uPipeRect, pHitMask, uHitmask)
            lCollide = pixelCollision(playerRect, lPipeRect, pHitMask, lHitmask)

            if uCollide or lCollide:
                return [True, False]

    return [False, False]

def pixelCollision(rect1, rect2, hitmask1, hitmask2):
    """Checks if two objects collide and not just their rects"""
    rect = rect1.clip(rect2)

    if rect.width == 0 or rect.height == 0:
        return False

    x1, y1 = rect.x - rect1.x, rect.y - rect1.y
    x2, y2 = rect.x - rect2.x, rect.y - rect2.y

    for x in range(rect.width):
        for y in range(rect.height):
            if hitmask1[x1+x][y1+y] and hitmask2[x2+x][y2+y]:
                return True
    return False

def getHitmask(image):
    """returns a hitmask using an image's alpha."""
    mask = []
    for x in range(image.get_width()):
        mask.append([])
        for y in range(image.get_height()):
            mask[x].append(bool(image.get_at((x,y))[3]))
    return mask

def getRandomPipe():
    """returns a randomly generated pipe"""
    # y of gap between upper and lower pipe
    gapY = random.randrange(0, int(BASEY * 0.6 - PIPEGAPSIZE))
    gapY += int(BASEY * 0.2)
    pipeHeight = IMAGES['pipe'][0].get_height()
    pipeX = screenWidth + 10

    return [
        {'x': pipeX, 'y': gapY - pipeHeight},  # upper pipe
        {'x': pipeX, 'y': gapY + PIPEGAPSIZE}, # lower pipe
    ]

# hismask for pipes HITMASKS['pipe'] = (
    getHitmask(IMAGES['pipe'][0]),
    getHitmask(IMAGES['pipe'][1]),
)

# hitmask for player HITMASKS['player'] = (
    getHitmask(IMAGES['player'][0]),
    getHitmask(IMAGES['player'][1]),
    getHitmask(IMAGES['player'][2]),
)

def draw_pygame():
    running = True
    playerIndex = 0
    playerIndexGen = cycle([0, 1, 2, 1])
    # iterator used to change playerIndex after every 5th iteration
    loopIter = 0

    basex = 0
    # amount by which base can maximum shift to left
    baseShift = IMAGES['base'].get_width() - IMAGES['background'].get_width()

    playerX = int(screenWidth * .2)
    playerY = screenHeight // 2

    basex = 0
    baseShift = IMAGES['base'].get_width() - IMAGES['background'].get_width()

    # get 2 new pipes to add to upperPipes lowerPipes list
    newPipe1 = getRandomPipe()
    newPipe2 = getRandomPipe()

    # list of upper pipes
    upperPipes = [
        {'x': screenWidth + 200, 'y': newPipe1[0]['y']},
        {'x': screenWidth + 200 + (screenWidth / 2), 'y': newPipe2[0]['y']},
    ]

    # list of lowerpipe
    lowerPipes = [
        {'x': screenWidth + 200, 'y': newPipe1[1]['y']},
        {'x': screenWidth + 200 + (screenWidth / 2), 'y': newPipe2[1]['y']},
    ]

    pipeVelX = -2

    while running:
        key = pygame.key.get_pressed()
        if key[pygame.K_q]:
            running = False
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        screen.fill((0,0,0))

        if not q.empty():
            b = q.get()
            if b > 0 and b < 1:
               playerY = screenHeight - int(screenHeight * b) 
        else:
            playerY = playerY + 2

        crashTest = checkCrash({'x': playerX, 'y': playerY, 'index': playerIndex},
                               upperPipes, lowerPipes)

        for pipe in upperPipes:
            pipeMidPos = pipe['x'] + IMAGES['pipe'][0].get_width() / 2

        # move pipes to left
        for uPipe, lPipe in zip(upperPipes, lowerPipes):
            uPipe['x'] += pipeVelX
            lPipe['x'] += pipeVelX

        # add new pipe when first pipe is about to touch left of screen
        if 0 < upperPipes[0]['x'] < 4:
            newPipe = getRandomPipe()
            upperPipes.append(newPipe[0])
            lowerPipes.append(newPipe[1])

        # remove first pipe if its out of the screen
        if upperPipes[0]['x'] < -IMAGES['pipe'][0].get_width():
            upperPipes.pop(0)
            lowerPipes.pop(0)

        screen.blit(IMAGES['background'], (0,0))

        for uPipe, lPipe in zip(upperPipes, lowerPipes):
            screen.blit(IMAGES['pipe'][0], (uPipe['x'], uPipe['y']))
            screen.blit(IMAGES['pipe'][1], (lPipe['x'], lPipe['y']))

        if (loopIter + 1) % 5 == 0:
            playerIndex = next(playerIndexGen)
        loopIter = (loopIter + 1) % 30
        basex = -((-basex + 4) % baseShift)

        screen.blit(IMAGES['base'], (basex, BASEY))
        screen.blit(IMAGES['player'][playerIndex],
                    (playerX, playerY))

        pygame.display.flip()
        clock.tick(60)

min_note, max_note = get_vocal_range()
t = Thread(target=position_on_range, args=(min_note, max_note))
t.daemon = True
t.start()

draw_pygame()
stream.stop_stream()
stream.close()
pygame.display.quit() 
```

Enter fullscreen mode Exit fullscreen mode

在节目的最后，你会看到我们如何使用导入的`get_vocal_range`函数来首先获得我们的音域。

因为我们在主程序中启动 Pygame，所以当控制台显示我们的控制器时，我们会看到一个空白的窗口。

在下一篇文章中，我们将解决这个问题，并在用户试图建立他们的音域时给他们一个视觉反馈。

一旦完成，我们就开始我们的主要 Flappy Bird 循环，跟踪声音位置并更新 Bird 以匹配。如果演奏者停止敲击音符，鸟就开始下落。

有了这个，我们就有了第一个语音控制的 Flappy Bird 原型。它还远未准备好成为一个产品游戏，但它给了我们一个互动歌唱游戏的基本开始。

## 我们将在下一篇文章中做什么

在下一篇文章中，我们将进一步完善我们的 Flappy Bird 版本，并把它变成一个音乐游戏。我们会通过给他们参考音符来帮助我们的玩家，并在我们建立音域时给他们一些视觉反馈。

我们将使 Singy-Bird 成为一个更具音乐性的游戏，每根管子代表一个有音乐意义的地方，构建一个旋律。

最后，我们将使用 py2exe 扩展将我们的程序捆绑到一个可以与其他人共享的程序中。

## 何去何从

[![](img/381128df9a8e3dd04c78e009e6348f0f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--L0oJCKfB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z1vuzikgofgawdc962ip.png)

这篇文章还在进行中，但我想展示一个更大的程序是如何在一个更小的单个 Python 文件应用程序之外组合在一起的。

你可以在 [Github](https://github.com/burningion/singy-bird) 看到所有正在进行的代码。这个程序应该可以在你的电脑上运行，但是要注意你需要 Music21、Aubio、Pygame、PyAudio 和 NumPy 来运行所有的程序。

如果你对更多类似的项目感兴趣，请注册我的时事通讯，或者在 [Make Art with Python](https://www.makeartwithpython.com/signup) 上创建一个帐户。当你这样做的时候，你会免费得到我新书的前三章，并且你会帮助我继续做这样的项目。