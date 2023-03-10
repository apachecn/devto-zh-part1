# 哪个 for 循环？

> 原文：<https://dev.to/lankydandev/which-for-loop-pa1>

Java 8 引入了另一种类型的`for`循环。这给了我们使用它们的第三种方式。你可能会认为发布另一种使用循环的方式会暗示它一定比其他方式更好。但事实并非如此，每一个都有细微的差别，这意味着在你决定使用哪一个之前，你应该稍微考虑一下。

for 循环的类型:

*   正常`for`循环
*   `for-each`循环
*   Java 8 `for-each`循环

## 正常为循环

现在我要直截了当地说出来。这些循环看起来很丑，我尽量远离它们。无论如何，现在我已经说过，让我们回到解释它们是什么。这种类型的循环使用计数器来遍历列表或数组的元素。最简单的解释方法是通过一个例子。

```
List<Integer> list = Arrays.asList(1,2,3,4,5,6);
for(int index=0; index<list.size(); index++) {
    System.out.println("I have read the number: " + list.get(index));
}

int[] array = new int[]{ 1,2,3,4,5,6};
for(int index=0; index<array.length; index++) {
    System.out.println("I have read the number: " + array[index]);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面显示了如何遍历一个列表和一个数组。它通过增加计数器值`index`并使用`list.get(index)`或`array[index]`检索该位置的元素来实现。有了元素之后，它就可以随心所欲了。使用这种类型的循环的优点是，与使用其他类型的循环相比，您可以更好地控制如何读取元素。如果你想很容易地获取位置`index-1`的元素，只要记住处理`index = 0`的场景或者你手上有一个`IndexOutOfBoundsException`就可以了。这种控制的优点也是它的缺点，因为您需要手动检索您需要的元素，这使得代码看起来很混乱。这种循环的丑陋在其他类型的循环中找不到。

## for-each 循环

这个循环的名字很好地概括了它的功能。对集合或数组中的每个元素做一些事情。不需要使用计数器来递增列表，因为这都是在后台完成的。简单地给出你想要检索的对象，输入并命名，你就可以开始了。

```
List<Integer> list = Arrays.asList(1,2,3,4,5,6);
for(int number : list) {
    System.out.println("I have read the number:" + number);
}

int[] array = new int[]{ 1,2,3,4,5,6};
for(int number : array) {
    System.out.println("I have read the number: " + number);
}

Set<Integer> set = new HashSet<>(Arrays.asList(1,2,3,4,5,6));
for(int number : set) {
    System.out.println("I have read the number: " + number);
} 
```

Enter fullscreen mode Exit fullscreen mode

看它们多简单漂亮，比上面那些难看的圈圈好看多了。无论如何，循环将遍历元素，并在每次迭代中从集合或数组中为定义的变量提供一个值。检索值的顺序与元素在集合或数组中的顺序相同。`for-each`循环的另一个优点是它可以遍历`Set`的元素，因为它没有自己的`get()`方法。

## Java 8 for-one 循环

这是随着 Java 8 的发布而增加的新的`for`循环。它具有普通`for-each`循环的所有特征，但使用 Lambda 表达式来定义它对集合中迭代的每个元素做什么，尽管这种形式不能用于数组。下面的例子非常简单明了。

```
List<Integer> list = Arrays.asList(1,2,3,4,5,6);
list.forEach(number -> System.out.println("I have read the number:" + number)); 
```

Enter fullscreen mode Exit fullscreen mode

使用这种形式的循环`for`可以代替普通的`for-each`循环，因为它们在功能上是相同的，但语法不同。对于简单的 lambda 表达式来说，这种形式看起来更好，如果表达式足够小，可以允许在一行中定义一个循环。虽然对于包含更多语句的循环，用户更喜欢使用哪一个`for-each`循环，因为这样可以减少代码量

那么应该使用哪个循环呢？如果您需要完全控制所读取的元素，或者需要在列表中添加和删除元素，那么一个普通的`for`循环是一个不错的选择。如果你只是需要对列表元素执行一些语句，而不需要添加或删除元素，那么普通的`for-each`和 Java 8 `for-each`循环就足够了。决定使用哪一个`for-each`循环取决于你，就个人而言，对于单数或更小的语句，我会使用 Java 8 `for-each`循环，但是对于包含更长语句的循环，我会选择传统的`for-each`循环。