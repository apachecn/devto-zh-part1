# 暴力破解 NPR 周日拼图

> 原文：<https://dev.to/mobbsdev/brute-forcing-the-npr-sunday-puzzle>

几周前，我在看 [NPR 周日拼图](http://www.npr.org/2017/07/16/537225382/sunday-puzzle-wehn-wrods-get-rearearngd)，和我的女朋友一起头脑风暴寻找答案。谜题是:

> 说出一个美国城市及其州名——总共 12 个字母。改变州名中的两个字母。其结果将是一部经典小说的两个字的标题。这是什么？

一段时间后，我们试图列出所有我们知道的书籍，我们谷歌了一些类似“经典书籍列表”的东西，并浏览列表检查是否有符合标准的书籍。几分钟后，我意识到一件事——我们只是在强行通过这个难题。那么，为什么我不能把它自动化呢？

## 它有什么作用

我写了一个简单的脚本，它遍历一个图书列表，并检查它是否符合谜题给出的标准。我从 [OpenLibrary](http://openlibrary.org) 获取书名数据，OpenLibrary 在任何给定时间为[提供目录中所有信息的数据转储](https://openlibrary.org/developers/dumps)。脚本本身非常简单。我写了一个函数，它接受一个单词，并返回与它相距两个字符的任何状态

```
# Define a function that takes a word and checks to see if it's two characters away from a state
# If it is, it returns the state
# If it's not, it returns False def word_to_state(word):
    # Make a list of every state
    states = ['Alaska', 'Alabama', 'Arkansas', 'American Samoa', 'Arizona', 'California', 'Colorado', 'Connecticut', 'District of Columbia', 'Delaware', 'Florida', 'Georgia', 'Guam', 'Hawaii', 'Iowa', 'Idaho', 'Illinois', 'Indiana', 'Kansas', 'Kentucky', 'Louisiana', 'Massachusetts', 'Maryland', 'Maine', 'Michigan', 'Minnesota', 'Missouri', 'Northern Mariana Islands', 'Mississippi', 'Montana', 'National', 'North Carolina', 'North Dakota', 'Nebraska', 'New Hampshire', 'New Jersey', 'New Mexico', 'Nevada', 'New York', 'Ohio', 'Oklahoma', 'Oregon', 'Pennsylvania', 'Puerto Rico', 'Rhode Island', 'South Carolina', 'South Dakota', 'Tennessee', 'Texas', 'Utah', 'Virginia', 'Virgin Islands', 'Vermont', 'Washington', 'Wisconsin', 'West Virginia', 'Wyoming']
    # Iterate over every state to check it against the word
    for state in states:
        # If the length of the state is the same as the length of the word...
        if len(state) == len(word):
            # Convert the word and the state to all lowercase to make it easier to compare them
            word, state = word.lower(), state.lower()

            # Create a counter to check the amount of changes that you make to the word
            changes = 0

            # Iterate over the index and character in each state (Texas -> (0, T) (1, E) (2, X) (3, A), (4, S))
            for index, character in enumerate(state):

                # Check to see if the letter in the state is the same as the letter in that position of the word
                if character == word[index]:
                    # If it is, you don't need to do anything! Keep going
                    pass
                else:
                    # If it's not, increment the amount of changes we need to turn the state into the word
                    changes += 1

            # After we're done checking to see how many changes it takes to turn a word 
            # into a state, we check to see if it's greater than 2
            if changes > 2:
                # If it is, we continue on to the next state
                continue
            else:
                # If it's not, this is the state we're looking for - we return the state
                # The function will end here if a state can be found
                return state

    # If you make it here, no state was found - return False
    return False 
```

Enter fullscreen mode Exit fullscreen mode

写完这个函数后，剩下的就简单了。我们简单地迭代 OpenLibrary 中的图书列表，检查每本书是否满足初始(非状态)标准，然后通过我们的函数传递状态并返回结果。

```
# Iterate over every book in the file for book in books:
    # Figure out how many words are in the books title by splitting the title 
    words_in_book_title = book.split(' ')
    if len(words_in_book_title) == 2:
        # Find the amount of characters in the book title
        characters_in_book_title = ''.join([letter for letter in book if letter.isalpha()]) # 'Infinite Jest' -> 'InfiniteJest'
        if len(characters_in_book_title) == 12:
            # Get the second word in the book title, pass it to word_to_state
            second_word = book.split(' ')[1]
            # word_to_state('TexZZ') -> 'Texas'
            # word_to_state('gibberish1029831') -> False
            if word_to_state(second_word):
                # Print the title of the book and the state if there's a match
                print(book, word_to_state(second_word)) 
```

Enter fullscreen mode Exit fullscreen mode

在清理了 OpenLibrary works 转储之后，我将它用作脚本的输入。几秒钟后，结果出来了！可能的标题有:

```
Vergine Madre Maine
Painful Tears Texas
Inuit Indians Indiana
Settling Down Iowa
Savage Dragon Oregon
Problem Texts Texas
...(1259 more lines) 
```

Enter fullscreen mode Exit fullscreen mode

超过 1200 个匹配？我不可能手工解析所有这些。

## 怎样才能把这个修剪下来？

这是一个很好的开始，但是我们需要把它减少到可以用的书的数量。为此，我使用了很棒的 Python 库 [pyzipcode](https://github.com/fdintino/pyzipcode) ，它让我可以通过邮政编码查找城市和州。如果一个城市/州组合没有邮政编码，这不是答案。我将书名保存到一个文本文件中，并编写了一个新的脚本:

```
for book in potential_books:
    # Book format: "Painful Tears Texas"
    city, state = book.split(' ')[0], book.split(' ')[2]
    results = zcdb.find_zip(city=city, state=state)
    if results is not None:
        print(title + ' - ' + city + ', ' + state) 
```

Enter fullscreen mode Exit fullscreen mode

这导致了这个(更合理的)输出:

```
Miami Indians - Miami, Indiana
Lake Michigan - Lake, Michigan
Raymond Hains - Raymond, Maine
Moon Virginia - Moon, Virginia
Joseph Crugon - Joseph, Oregon
Eugene Onegin - Eugene, Oregon
Richmond Whig - Richmond, Ohio
Columbus Ohio - Columbus, Ohio
Garrison Town - Garrison, Iowa
Joseph Gregor - Joseph, Oregon 
```

Enter fullscreen mode Exit fullscreen mode

稍加谷歌搜索(只是为了确认哪个书名是经典书籍)，我们就有了答案——[叶甫盖尼·奥涅金](https://en.wikipedia.org/wiki/Eugene_Onegin)，一本在 1825 年至 1832 年间以连载形式出版的经典俄罗斯文学作品。奥涅金离俄勒冈州只有两个字母的距离。我们做到了！

提交了我们的答案后，我们耐心等待，直到下一周的结果。我们是对的！虽然我们的电子邮件没有被选为获奖答案，但祝贺俄亥俄州代顿市的罗伯·哈迪得到它——你可能应该得到更多；)

## 接下来是什么

实际上，`word_to_state`并不是一个有效的函数。我把它写得尽可能详细，试图帮助我正在学习编码的女朋友理解它。这里有一个快速改进:

```
def word_to_state(word):
    states = ['Alaska', 'Alabama', 'Arkansas', 'American Samoa', 'Arizona', 'California', 'Colorado', 'Connecticut', 'District of Columbia', 'Delaware', 'Florida', 'Georgia', 'Guam', 'Hawaii', 'Iowa', 'Idaho', 'Illinois', 'Indiana', 'Kansas', 'Kentucky', 'Louisiana', 'Massachusetts', 'Maryland', 'Maine', 'Michigan', 'Minnesota', 'Missouri', 'Northern Mariana Islands', 'Mississippi', 'Montana', 'National', 'North Carolina', 'North Dakota', 'Nebraska', 'New Hampshire', 'New Jersey', 'New Mexico', 'Nevada', 'New York', 'Ohio', 'Oklahoma', 'Oregon', 'Pennsylvania', 'Puerto Rico', 'Rhode Island', 'South Carolina', 'South Dakota', 'Tennessee', 'Texas', 'Utah', 'Virginia', 'Virgin Islands', 'Vermont', 'Washington', 'Wisconsin', 'West Virginia', 'Wyoming']
    for state in states:
        if len(word) == len(state):
            word, state = word.lower(), state.lower()
            changed_characters = [letter for index, letter in enumerate(word) if state[index] != letter]
            if len(changed_characters) == 2:
                return state.title()
    return False 
```

Enter fullscreen mode Exit fullscreen mode

我最初认为集合比较是可行的，但是对于这个挑战，**字母的顺序很重要**。奥涅金的挑战答案使用集合比较会失败，因为我们将字母“r”改为单词“Oregon”中已经存在的字母。虽然肯定会有改进，但那是以后的事了。现在回想起来，我最初的函数甚至没有正确处理这个问题——看看一些输出就知道了！幸运的是，我是在这么小的范围内工作的，这没什么大不了的。

*这篇文章最初发表在[我的博客](http://ianmobbs.com/projects/npr-sunday-puzzle)上——来玩吧！*