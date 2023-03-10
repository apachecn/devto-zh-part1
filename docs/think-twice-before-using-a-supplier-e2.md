# 在使用供应商之前要三思

> 原文：<https://dev.to/eldroskandar/think-twice-before-using-a-supplier-e2>

# 供求关系

在我上一篇文章的结尾，我们有一段不起作用的代码，它试图用第二个列表中的字符串替换第一个列表中的占位符字符串:

```
private static void populateTemplateFile(ArrayList<String> sKeys, ArrayList<String> sVals) {
    File file = new File(TEMPLATE_FILE);
    File trgFile = new File(TARGET_FILE);
    Stream<String> lines = null;

    try
    {
        lines = Files.lines(Paths.get(file.toURI()));

        //Newline for readability, this is only one line.
        Stream<String> replaced = lines
            .map(line -> {
                for(int iCtr=0; iCtr<sKeys.size(); iCtr++)
                    line.replaceAll(sKeys.get(iCtr), sVals.get(iCtr));
                    return line;});
        lines.close();
        Files.write(Paths.get(trgFile.toURI()), replaced.toString().getBytes());
    }
    catch (IOException e)
    {
       log.error("Exception while trying to create " + TARGET_FILE, e);
    }
    finally {
        lines.close();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在文章中总结道，虽然我尝试使用了`Stream`，但它是一个双循环的事实会使这个案例成为我想要做的一个糟糕的候选。

但是我突然想到，在我的第一个例子中，我使用了一个供应商来访问流两次，为什么不是 n 次呢？这将使我能够从`map`调用中提取 for 循环，取而代之的是，我将替换 keys 列表中每个元素的值。

这听起来很简单，直到我意识到这是行不通的。你看，当`Supplier`提供给你文件的行时，它提供给你的是未被改动的行。当您不打算修改这些值时，这没问题，但是当您进行替换时，这就是另一回事了。最后，只有一个人会被替换:最后一个。

# 成功的关键

然而，并非所有的希望都落空了。你看，与此同时，我徒劳地试图使用一个`Supplier`，[llus Josep Martinez](https://dev.to/lluismf)给我指了一条路[把输出文件写成一个流](https://stackoverflow.com/questions/28504504/modify-file-using-files-lines/28617603#28617603)。

起初我持怀疑态度，但他似乎确信这将是解决办法。于是我再次寻找解决办法。

首先，正如 llu as 所建议的，使用一个`Map`而不是两个`List`作为方法的参数，并使参数名称更加明确，这似乎是明智的。

```
private static void populateTemplateFile(Map<String,String> substitutionDictionary) { 
```

Enter fullscreen mode Exit fullscreen mode

但是它仍然没有帮助我解决我的问题。所以我试着换个角度。你看，我的问题是，我似乎可以找到一个适当的解决方案来循环遍历这些键，并尝试在行中替换它们。

所以如果我先把台词循环一遍呢？但是我会再次回到`map`调用中 for 循环的问题。除非我去掉第二个循环。你看，作为一名 PHP 开发人员，我经常使用`in_array()`函数来查看一个字符串是否包含在一个`Array`中。当然，包含信息的行不仅仅包含我正在搜索的关键信息(即占位符)，但是以类似的方式，我可以检查文件行中是否包含任何关键信息。

```
Function<String,String> replacePlaceholders = (line) -> {
    Set<String> keySet = substitutionDictionary.keySet();
    Optional<String> key = keySet.stream()
                                 .filter(line::contains)
                                 .findAny();

    if(key.isPresent()) {
        return line.replace(key.get(), substitutionDictionary.get(key.get()));
    }

    return line;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我只需要应用 llu as .
提供的链接上的代码

```
try (Stream<String> lines = Files.lines(Paths.get(file.toURI()));
     PrintWriter output = new PrintWriter(TARGET_FILE, "UTF-8"))
{

    lines.map(s -> replacePlaceholders.apply(s))
         .forEachOrdered(output::println);
    output.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

事实上，使用函数进行数据操作是如此有效，以至于我为其他人的问题找到了类似的解决方案。

# 漏供应商

回到启发我写第一篇文章的那段代码，我很高兴，因为它正在工作，并得到了我想要的结果。直到我收到一条`java.nio.file.FileSystemException`消息，告诉我有[太多打开的文件](http://www.rationaljava.com/2015/02/java-8-pitfall-beware-of-fileslines.html)...结果是我泄漏了文件句柄，我必须确保那些句柄会被关闭。所以我按照建议使用了 try-with-resource。

```
Supplier<Stream<String>> linesSupplier = () -> {
    try(Stream<String> lines = Files.lines(Paths.get(file.toURI())))
    {
        return lines;
    }
    catch (IOException e)
    {
        log.error("Error while supplying file " + file.getName(), e);
    }
    return null;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我很快就失望了，因为它扔给我一个`IllegalStateException`，就像我使用`Supplier`之前一样。理所当然地，当[`Supplier`关闭`Stream`时，它在我可以使用它](https://stackoverflow.com/questions/47304161/should-supplier-be-used-to-supply-file-stream/)之前返回。

我不得不再次改变视角。如果我一次存储所有的值。如果我将`Predicate`存储在一个列表中，并在`Stream`关闭之前依次进行所有的过滤，这是可能的。

```
List<String> values = null;
List<Predicate<String>> filters = Arrays.asList(sourcePattern.asPredicate(), contentPattern.asPredicate());
String source = "";
String content = "";

try (Stream<String> lines = Files.lines(Paths.get(file.toURI())))
{
    values = lines.filter(line -> filters.stream()
                                      .anyMatch(f -> f.test(line)))
                  .collect(Collectors.toList());
    source = values.get(0);
    content = values.get(1);
}
catch (IOException e)
{
    log.error("Error while filtering " + file.getName(), e);
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我可以使用我得到的值来满足我的心意。是的，我说过我可以将关闭`Stream`的责任转移到调用`Supplier`的代码行，但是我认为最终我找到了一个更好的方法。

# 离别的思念

*   一个`Supplier`毫无疑问有很好的用途，但是直到现在，我都没有找到一个。所以下一次你试图使用一个，因为你想不止一次地访问你的`Stream`，问问你自己:“我不能从`Stream`一次得到我需要的所有东西吗？”
*   如果你被一个问题难住了，试着换个角度看问题？通常情况下，这是有帮助的。

直到下一次我需要改变我的观点。