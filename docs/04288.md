# 快速提示:一厢情愿地编程

> 原文：<https://dev.to/_bigblind/quick-tip-programming-by-wishful-thinking-3hn>

你也可以在我的博客上阅读这篇文章。

你可能听过这样的建议:把你的工作分成容易处理的小块，或者把一个复杂的问题分成简单的部分。这也是编程时使用的一种方便的技术

你写一个函数来解决一个特殊的问题，你写它就好像你想要的任何复杂的功能都已经写好了。然后，你去填充这些函数，并应用同样的技术。

# 举个例子

假设你正在写一个井字游戏，你需要写一个叫做`getWinner`的函数。它把棋盘当作一个 2D 阵列。数组中的每个单元格都包含字符串“X”、“O”或”("."是一个空单元格)。如果相应的玩家有一行 3，它返回“X”或“O ”,否则返回`null`。这是检查的过程

*   检查水平行。如果没有赢家，...
*   检查垂直行。如果没有赢家，...
*   返回对角线上是否有赢家。

所以你应该这样用 JavaScript 写这个函数:

```
 function getWinner(board) {
    return getHorizontalWinner(board) ||
           getVerticalWinner(board) ||
           getDiagonalWinner(board);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们只是希望`getHorizontalWinner`和它的朋友们存在，所以我们的问题就这么简单。然而，向星星许愿并不能让你走得很远，所以我们仍然必须实现这些函数中的每一个，但至少它们是需要解决的小问题。

让我们深入研究，先写`getHorizontalWinner`再写

```
function getHorizontalWinner(board) {
  for(var i=0; i<3; i++) {
    var winner = getLineWinner(board[i])
    if(winner !== null) {
      return winner;
    }
  }
  return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们只是编写代码，就好像我们已经有了`getLineWinner`，它接受一个由 3 个棋盘单元格组成的数组，并返回在那里赢了一局的玩家，如果有的话，否则返回 null。

`getVerticalWinner`看起来会非常相似:

```
function getVerticalWinner(board) {
  for(var i=0; i<3; i++) {
    var winner = getLineWinner([board[0][i], board[1][i], board[2][i]])
    if(winner !== null) {
      return winner;
    }
  }
  return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意的话，你可以添加一个`getColumn`函数，抽象出一个数组，用于给定的棋盘列，但是在这种情况下，我认为没有必要。

现在为`getDiagonalWinner` :

```
function getDiagonalWinner(board) {
  return getLineWinner([board[0][0], board[1][1], board[2, 2]]) ||
         getLineWinner([board[0][2], board[1][1], board[2, 0]]);
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？现在我们唯一剩下要写的是`getLineWinner` :

```
function getLineWinner(line) {
  if (line[0] !== "." && line[0] === line[1] && line[1] === line[2]) {
    return line[0]
  }
  return null
} 
```

Enter fullscreen mode Exit fullscreen mode

我们完事了。您可以用类似于`isEmpty`的函数来代替对`line[0] !== "."`的检查，然后由它来进行检查。这个函数在实现游戏的其他部分时也非常方便，例如检查玩家在特定单元格中放置符号是否有效。但是对于这个例子，我保持原样。

这里要注意的最重要的一点是，你通过调用你希望存在的函数来抽象细节。通过这种方式，您可以自然地将代码分割成大小合理的函数，处理不同层次的抽象。

希望这对你有帮助。