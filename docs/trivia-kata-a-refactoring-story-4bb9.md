# 琐事卡塔:重构的故事

> 原文：<https://dev.to/rnowif/trivia-kata-a-refactoring-story-4bb9>

重构遗留代码可能是一件棘手的事情。更多的时候，代码没有经过测试，不清楚，包含一些 bug。如果没有计划，重构所需的时间会吃掉分配给该特性的所有时间。这个特性将会很快实现，并且会产生更多的不可维护的代码。

在这篇博文中，我将尝试展示一些工具和方法来安全地重构一段代码。我将使用[琐事形](https://github.com/jbrains/trivia)作为对此的支持。结果代码可以在 [GitHub](https://github.com/rnowif/trivia-refactoring/commits/master) 上找到。

## 人物塑造测试:金主

任何重构的先决条件是构建一个测试工具，以确保重构不会破坏任何东西。在这个例子中，用单元测试来测试所有可能的路径似乎是浪费时间。幸运的是，该代码包含了大量的跟踪信息，这些跟踪信息显示了该代码所做的事情。然后就可以用一组输入运行代码，捕获输出并保存它。这个输出叫做*金主*。每次修改代码时，使用相同的输入来运行代码，并将输出与 golden master 进行比较。正如你可能已经注意到的，这并不能确保代码没有错误，它只是确保代码总是**表现**一样。

在 Java 中，可以使用一个名为 *approval* :
的库来实现金主

```
<dependency>
    <groupId>com.github.nikolavp</groupId>
    <artifactId>approval-core</artifactId>
    <version>0.3</version>
</dependency> 
```

相关的测试如下所示(见[提交](https://github.com/rnowif/trivia-refactoring/commit/0c4255e69cc5395a70de417913de20c82606b0c2) ):

```
@Test
public void should_record_and_verify_golden_master() {
    String result = playGame(1L);

    Approvals.verify(result, Paths.get("src", "main", "resources", "approval", "result.txt")); // 2
}

private String playGame(long seed) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    System.setOut(new PrintStream(outputStream)); // 3

    boolean notAWinner;
    Game aGame = new Game();

    aGame.add("Chet");
    aGame.add("Pat");
    aGame.add("Sue");

    Random rand = new Random(seed); // 1

    do {

        aGame.roll(rand.nextInt(5) + 1);

        if (rand.nextInt(9) == 7) {
            notAWinner = aGame.wrongAnswer();
        } else {
            notAWinner = aGame.wasCorrectlyAnswered();
        }

    } while (notAWinner);

    return new String(outputStream.toByteArray());
} 
```

由于代码使用一个`Random`来掷骰子，我们必须固定`Random`的种子，这样它总是以相同的顺序返回相同的随机掷骰子(`1`)。代码必须用这个种子运行一次，输出必须粘贴到`src/main/resources/approval/result.txt`中，以创建金主(`2`)。之后，测试将捕获输出(`3`)并将其与金主进行比较。如果出现差异，将会提示差异。

有时在我的重构会议中，我喜欢故意出错，只是为了检查我的测试工具的健壮性。我发现这非常有用，可以增加你对测试的信心，并在必要时修改它们。

## 第一步:清理

个人觉得，面对臃肿、不清晰的代码，真的无法思考。因此，我重构这段代码的第一件事是重新安排它，删除未使用的导入和死代码，删除神奇的字符串和神奇的数字，使用泛型并减少可以减少的字段和方法的可见性，以确保我可以在不破坏公共 API 的情况下接触它们。这些重构的主要部分可以由您的 IDE 来完成，所以不要犹豫，因为它降低了出错的风险，而且速度更快。你也可以使用像 [SonarLint](http://www.sonarlint.org/index.html) 这样的插件来直接检测你的 IDE 中的问题。

一旦代码更容易思考，我注意到字符串被用来识别类别。这似乎是创建枚举的好地方(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/1578c0ce9d7da73641e5f4202b1a72f00ba13723))。首先，我将字符串替换为枚举:

```
private void askQuestion() {
    if (currentCategory() == "Pop")
        print(popQuestions.removeFirst());
    if (currentCategory() == "Science")
        print(scienceQuestions.removeFirst());
    if (currentCategory() == "Sports")
        print(sportsQuestions.removeFirst());
    if (currentCategory() == "Rock")
        print(rockQuestions.removeFirst());
}

private String currentCategory() {
    if (places[currentPlayer] == 0) return "Pop";
    if (places[currentPlayer] == 4) return "Pop";
    if (places[currentPlayer] == 8) return "Pop";
    if (places[currentPlayer] == 1) return "Science";
    if (places[currentPlayer] == 5) return "Science";
    if (places[currentPlayer] == 9) return "Science";
    if (places[currentPlayer] == 2) return "Sports";
    if (places[currentPlayer] == 6) return "Sports";
    if (places[currentPlayer] == 10) return "Sports";
    return "Rock";
} 
```

变成了

```
private void askQuestion() {
    if (currentCategory().equals(Category.POP))
        print(popQuestions.removeFirst());
    if (currentCategory().equals(Category.SCIENCE))
        print(scienceQuestions.removeFirst());
    if (currentCategory().equals(Category.SPORTS))
        print(sportsQuestions.removeFirst());
    if (currentCategory().equals(Category.ROCK))
        print(rockQuestions.removeFirst());
}

private Category currentCategory() {
    if (places[currentPlayer] == 0) return Category.POP;
    if (places[currentPlayer] == 4) return Category.POP;
    if (places[currentPlayer] == 8) return Category.POP;
    if (places[currentPlayer] == 1) return Category.SCIENCE;
    if (places[currentPlayer] == 5) return Category.SCIENCE;
    if (places[currentPlayer] == 9) return Category.SCIENCE;
    if (places[currentPlayer] == 2) return Category.SPORTS;
    if (places[currentPlayer] == 6) return Category.SPORTS;
    if (places[currentPlayer] == 10) return Category.SPORTS;
    return Category.ROCK;
} 
```

这种看起来很像`switch`的`if`语句几乎总是可以被`Map`替换，所以我就是这么做的(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/b8780743929eb10ab5f8a90c9fe68c9dd3c310a9))。我创建了两个地图来包含每个类别的问题和每个职位的类别:

```
private void askQuestion() {
    print(questionsByCategory.get(currentCategory()).removeFirst());
}

private Category currentCategory() {
   return categoriesByPosition.get(currentPosition());
} 
```

## 第二步:隔离

我选择遵循的第二步是将特定的逻辑放入私有方法中。再一次，你的 IDE 可以帮助你，使用它！

例如，这段代码:

```
print(players.get(currentPlayer) + " is getting out of the penalty box");

places[currentPlayer] = places[currentPlayer] + roll;
if (places[currentPlayer] >= NB_CELLS) {
    places[currentPlayer] = places[currentPlayer] - NB_CELLS;
}

print(players.get(currentPlayer) + "'s new location is " + places[currentPlayer]); 
```

变成了

```
print(players.get(currentPlayer) + " is getting out of the penalty box");
move(roll);
print(players.get(currentPlayer) + "'s new location is " + currentPosition()); 
```

通过这样做，您可以通过将算法分解成特定的方法来消除重复。此外，您还要准备从该代码中提取对象的字段(参见下一节)。然而，为了正确地做到这一点，私有方法必须是尽可能纯净的形式:它们应该使用或修改尽可能少的私有字段(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/160c52d4a393fef3a58ad062fa9b552f0a9631a0))。考虑到这一点，下面的代码:

```
private Category currentCategory() {
    return categoriesByPosition.get(currentPosition());
} 
```

已被替换为

```
private Category currentCategory(int position) {
    return categoriesByPosition.get(position);
} 
```

## 第三步:各个击破

既然逻辑已经被分离到私有方法中，那么从这些方法中提取对象就非常简单了。

在这个形中，我们看到几个概念浮现出来:`Player`、`Board`和`QuestionDeck`。还增加了一个`PlayerList`职业来处理玩家的轮换。为了不再仅仅依赖金主，所有这些职业都可以单独测试。

例如，下面的代码打印下一个要问的问题:

```
public void roll(int roll) {

  // ...

  Category currentCategory = board.categoryOf(newPosition);
  print("The category is " + currentCategory);
  print(nextQuestionAbout(currentCategory));
}

private String nextQuestionAbout(Category category) {
  return questionsByCategory.get(category).removeFirst();
} 
```

通过创建一个`QuestionDeck`对象，我们可以得到这样的代码(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/6ae3f22923b425d51f7fb657d7236926d2149309) ):

```
public void roll(int roll) {

  // QuestionDeck deck = new QuestionDeck(NB_QUESTIONS, CATEGORIES);

  Category currentCategory = board.categoryOf(newPosition);
  print("The category is " + currentCategory);
  print(deck.nextQuestionAbout(currentCategory));
} 
```

私有方法`nextQuestionAbout`和字段`questionsByCategory`不再属于`Game`类。

当您从主代码中提取类时，不要破坏公共 API 是非常重要的。不过也可以用`@Deprecated`来注释一些方法(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/a7c4d85c50395a0a66908eb15e3926b6d8d822de))。在这个[视频](https://www.crafties.fr/videos/2017-02-27-episode-9-refactoring-dependances-statiques.html)中可以看到一个很好的例子。

## 第四步:分手

最后一步是删除重构过程中引入的所有不推荐使用的方法。由于我们并不总是能够访问使用我们的公共 API 的客户端代码，所以并不总是能够在重构会话之后立即完成。在这种情况下，我们需要，所以我移除了这些方法(参见[提交](https://github.com/rnowif/trivia-refactoring/commit/eb7ab019f62808b589de0f6a4fcf904163fd667d))。

客户端代码如下所示:

```
Game aGame = new Game();

aGame.add("Chet");
aGame.add("Pat");
aGame.add("Sue");

// aGame.roll() 
```

现在`Game`类不再负责问题、面板、类别和玩家，所以我们必须将它们注入到构造函数中。

```
private static final int NB_CELLS = 12;
private static final int NB_QUESTIONS = 50;
private static final List<Category> CATEGORIES = asList(Category.POP, Category.SCIENCE, Category.SPORTS, Category.ROCK);
private static final List<String> PLAYERS = asList("Chet", "Pat", "Sue");

// ...
Game aGame = new Game(
        System.out,
        new Board(NB_CELLS, CATEGORIES),
        new QuestionDeck(NB_QUESTIONS, CATEGORIES),
        new PlayerList(PLAYERS)
);

// aGame.roll() 
```

## 包装完毕

当然，重构总是这样，我本可以做得更多。主要的风险是更多的 T1 并不总是意味着 T2 更好的 T3。重构会话必须有目的，知道何时停止非常重要。它可能是当你可以无缝地添加你的新功能或修复一个 bug 时，或者仅仅是因为你没有更多的时间分配给这项任务。

我希望这篇文章给了你一些工具和方法来重构你的代码，而不用担心会破坏一些东西。