# 修复死循环的方法

> 原文：<https://dev.to/qkrgksals17/i-created-an-endless-loop-at-parts-but-i-dont-know-how-to-fix-it-2ic>

下面是我创建无限循环的代码，有*的那一行。请让我知道如何解决这个问题。

```
# Ask to choice coffee
coffee_choice = input("Enter 1 for jonestown brew and 2 for plymouth jolt: ")
*while coffee_choice != 1 and coffee_choice!= 2:
    print ("Please enter a valid option.\n\n")
    coffee_choice = input("Enter 1 for jonestown brew and 2 for plymouth jolt: ")

# Ask pounds of coffee
pounds = input("Enter the amount of coffee you want to order in pounds: ")
*if type(pounds) != float and type(pounds) != int:
    print ("Please enter a valid option.\n\n")
    pounds = input("Enter the amount of coffee you want to order in pounds: ")

# Ask for States
state = input("Please enter your state: ") 
```

Enter fullscreen mode Exit fullscreen mode