# 面向 Java 开发人员的 Loops A La Kotlin

> 原文：<https://dev.to/chrisvasqm/loops-a-la-kotlin-2m4k>

每当我们发现某件事必须重复做，或者我们想在一组价值观中找到某件事，我们就依赖这些我们可以支配的好朋友:

### 为循环

```
// Java

for (condition) {
    statement;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 为-个

```
// Java

for (TYPE item : items) {
    statement;
} 
```

Enter fullscreen mode Exit fullscreen mode

### While 循环

```
// Java

while (condition) {
    statement;
} 
```

Enter fullscreen mode Exit fullscreen mode

### Do-while 循环

```
// Java

do {
    statement;
} while (condition) 
```

Enter fullscreen mode Exit fullscreen mode

Kotlin 标准库为我们提供了一些方法和类，让我们的生活变得更加简单。

## 例子:

#### 免责声明

*   Java 示例来自版本< 8\. The new [流](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html)库，提供了与 Kotlin 类似的高阶函数(感谢 [@robertbutacu](https://dev.to/robertbutacu) 的输入)。
*   这些是场景的简化版本。

### 1)想象你在亚马逊购物，你想`filter``products`，这样你就只能看到有 3 个或 3 个以上的`rating`:

首先，我们需要一个*产品* `class`，它将拥有`name`和`rating`属性。

```
// Java

public class Product {
    private final int rating;
    private final String name;

    public Product(String name, int rating) {
        this.rating = rating;
        this.name = name;
    }

    public int getRating() {
        return rating;
    }

    public String getName() {
        return name;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我将创建一个函数，根据我们传递给它的`rating`来过滤我们的`Product`中的`List`。

```
// Java

private static List<Product> filterByRating(List<Product> products, int rating) {
    List<Product> filteredProducts = new ArrayList<>();
    for (Product product : products) {
        if (product.getRating() >= rating) {
            filteredProducts.add(product);
        }
    }

    return filteredProducts;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了另一个`Products`的`List`，它将被`for-each`块填充，但前提是它们的`rating`等于或大于 3。

我们将这个功能添加到*主*中`class`。现在我们可以这样使用它:

```
// Java

public class Main {

    public static void main(String[] args) {
        List<Product> products = new ArrayList<>();
        products.add(new Product("Pants", 2));
        products.add(new Product("Laptop", 4));
        products.add(new Product("Smartphone", 5));

        List<Product> productsWithThreeOrHigherRating = filterByRating(products, 3);

        // ...
    }

    // filteredByRating method would be here
} 
```

Enter fullscreen mode Exit fullscreen mode

太好了，现在让我们做同样的事情*一拉科特林:*T2】

```
// Kotlin

class Product(val name: String, val rating: Int) 
```

Enter fullscreen mode Exit fullscreen mode

我们不需要`filterByRating()`函数，我们将使用从 *Iterable* 继承的每个`class`免费获得的 *filter()* 方法(就像本例中的 *List < >* )。

```
// Kotlin

fun main(args: Array<String>) {
    val products = listOf(
            Product("Pants", 2),
            Product("Laptop", 4),
            Product("Smartphone", 5)
    )

    val productsWithThreeOrHigherRating = products.filter { it.rating >= 3 }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `listOf()`是来自`kotlin.collection`包的一个`public`函数，它允许任意数量的参数，可以自动推断出参数的类型(它们甚至可以是不同的类型)`returns`是一个*列表*。
*   我们添加到我们的`products` *列表*中的每一个*产品*实例都不需要我们使用`new`关键字，就像在 Java 中一样，事实上，`new`关键字在 Kotlin 中并不存在。
*   我们对`products.filter`方法使用了一个*λ*表达式，它需要使用`{`和`}`。
*   每当我们写一个只有一个参数的 lambda 表达式时，`it`关键字总是存在的。它类似于`this`，但是当我们迭代它时，它引用我们的`products` *列表*中的单个`product`，就像在一个`for-each`循环中一样。
*   整个`it.rating >= 3`称为一个`predicate`。

> 一个`predicate`是一个布尔值表达式，当这个表达式为`true`时，它被传递给另一个函数作为参数来执行另一个动作*。*

### 2)假设我们想打印我们的`productsWithThreeOrHigherRating`的所有名称，以检查哪些是我们得到的*产品*:

```
// Java

public class Main {

    public static void main(String[] args) {
        // ...

        List<Product> productsWithThreeOrHigherRating = filterByRating(products, 3);

        for (Product product : productsWithThreeOrHigherRating) {
            System.out.println(product.getName());
        }

        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在*一拉·科特林:*T2】

```
// Kotlin

fun main(args: Array<String>) {
    // ...

    val productsWithThreeOrHigherRating = products.filter { it.rating >= 3 }

    productsWithThreeOrHigherRating.forEach { println(it.name) }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

*   `forEach`是来自 *Iterable* 的另一个函数(就像我们之前使用的`filter`),它接受任意参数，只要它的返回类型是 [Unit](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/index.html) (类似于 Java 中的`void`)。
*   在这种情况下，我们使用了`println()`函数，它是`System.out.println()`的简写。
*   我们在表达式中再次使用关键字`it`,我们访问属性`name`,以便`println`它。

### 3)现在，如果我们想知道在我们的`productsWithThreeOrHigherRating`中是否至少有 1 个*产品*的`rating`值为 5 呢？

我将创建一个函数，并将其命名为`hasAnyFiveRating` :

```
// Java

public boolean hasAnyFiveRating(List<Product> products) {
    for (Product product : products) {
        if (product.rating == 5) {
            return true;
        }
    }

    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用它在我们的主`class` :
中进行任何形式的验证

```
// Java

public class Main {

    public static void main(String[] args) {
        // ...

        List<Product> productsWithThreeOrHigherRating = filterByStars(products, 3);

        if (hasAnyFiveRating(productsWithThreeOrHigherRating)) {
            // ...
        else {
            // ...
        }

        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在*一拉·科特林:*T2】

```
// Kotlin

fun main(args: Array<String>) {
    // ...

    val productsWithThreeOrHigherRating = products.filter { it.rating >= 3 }

    val hasAnyFiveRating = productsWithThreeOrHigherRating.any { it.rating == 5 }
    if (hasAnyFiveRating) {
        // ...
    } else {
        // ...
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

很酷的东西，是吧？

这种语言中甚至包含了更多有用的函数，您可以在自己的项目中使用。但这会让这篇文章更大，所以我会让你研究剩下的部分。

希望你从这篇文章中学到了一些新东西，如果你没有，恭喜你！你只是浪费了生命中的几分钟👏。

但是不要担心，你可能现在正在玩[player unknown Battlegrounds](http://store.steampowered.com/app/578080/PLAYERUNKNOWNS_BATTLEGROUNDS/)就像全球其他[300 万人](https://www.eteknix.com/playerunknowns-battlegrounds-reaches-3m-concurrent-players/)一样(包括我)...反正你也不会拯救世界。

我就说这些。

## 克里斯出局！✌️