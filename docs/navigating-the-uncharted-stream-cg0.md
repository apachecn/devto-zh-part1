# 在未知的河流中航行

> 原文：<https://dev.to/eldroskandar/navigating-the-uncharted-stream-cg0>

# 错误的河湾

当我离开学校时，Java 6 刚刚发布，还没有真正融入学校课程。但是我很满意地认为，我可以简单地继续做一名 web 开发人员，忘记 Java 是什么(没怎么注意)，忘记 IDE、编译器和所有那些废话。当你有了值得信赖的文本编辑器来增强你的网络魔力时，谁还需要这样的东西呢？伙计，我错了。

大约两年后，我需要再次使用 Java，我首先用我的基本编程语言知识，然后重新发现了面向对象编程，最近我又了解了 Java 8 的特性。但是每当我遇到新的障碍时，我更多地使用新的或重新熟悉的知识作为创可贴，导致代码质量下降。最近，在一些重构会议之后，我意识到我需要改变策略。因此，我决定深入挖掘，而不是总是触及表面。

# 未知的溪流

当我想在文件中找到特定的信息，看看内容在测试过程中是否没有受到影响时，它就开始了。如果我没有发现这个关于如何在使用 Java 8 中引入的流对象的文件中[找到模式的问题，我会使用`reader = new BufferedReader(new FileReader(file));`来检查每一行的特定模式，以及每个文件的模式。结果我想出了下面这段代码:](https://stackoverflow.com/questions/34791138/find-pattern-in-files-with-java-8) 

```
// Searching for <filename:"…"> and extract its value.
Pattern pattern = Pattern.compile("filename:\"(.+)\"");

// List of objects containing information on the files
// generated at the beginning of the test.
List<GeneratedFile> generatedFileList = myTest.getFileList();

For (File file : fileList){
    Optional<String> message = Files.lines(Paths.get(file.toURI()))
                                    .filter(pattern.asPredicate())
                                    .findFirst();
    if (message.isPresent())
    {
        String fileContent = message.get();
        Matcher matcher = pattern.matcher(fileContent);
        matcher.find();
        String value = matcher.group(1);
        GeneratedFile generatedFile = findFileByOriginalName(value, generatedFileList);
        if(generatedFileList == null){
            Assert.fail("File "
                        + originalFilename
                        + " wasn't found.");
        }
    }
}

private static GeneratedFile findFileByOriginalName(String filename,
                                List<GeneratedFile> generatedFileList){
    for (GeneratedFile generatedFile : generatedFileList){
        if(generatedFile.getFileName().equals(filename)){
            return generatedFile;
        }
    }
    return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

当我想从文件内容中提取另一个数据点时，情况变得更加复杂，它抛出了一个`IllegalStateException`。然后我发现了`Supplier`，它允许我获得每次搜索的文件内容，而不用担心流自动关闭:

```
Supplier<Stream<String>> linesSupplier = null;
try
{
    linesSupplier = () -> getFileStream(tempfile);
}
catch (Exception e)
{
    log.error(e);
}

//Replace the pattern for each piece of information searched.
Optional<String> message = linesSupplier.get()
                                        .filter(filenamePattern.asPredicate())
                                        .findFirst();

private static Stream<String> getFileStream(File file)
{
    try
    {
        return Files.lines(Paths.get(file.toURI()));
    }
    catch (IOException e)
    {
    log.error("Exception while trying to supply file "
              + file.getName()
              + " content"
              , e);
    }
    return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 洛基骑

我很感兴趣，并决定在我认为适用的任何地方使用我的新知识。我认为它非常适合处理任何文件操作，并且可以去掉那些讨厌的`finally`子句。我以为这个崭新闪亮的东西会更好。原来，`Stream`是[并不总是可自动关闭的](https://stackoverflow.com/questions/22125169/why-is-java-8-stream-class-autocloseable)，而且它完全破坏了我的代码，变成了一团乱麻...

不用说，我对`Stream`的理解不如对`Optional`的理解，因为我已经发现[是我最同意](https://blog.codefx.org/techniques/intention-revealing-code-java-8-optional/)的用法。在那一点上，我决定我应该茁壮成长，更好地理解我使用的东西，这样我就可以充分利用它。

那么什么时候使用`Stream`？让我引用一下这个问题的答案，因为我问的是堆栈溢出:

> 如果您可以轻松地分离出不同的步骤来应用于每个数据集成员，那么流似乎是一个可靠的选择，因为 API 可以在任何可能的时候组合步骤。

这意味着你需要有一个具体的想法，在这个点上需要发生什么，你可以用离散的步骤来描述它。

此外，我发现应该注意你操作的顺序:首先，你需要过滤，然后操作，最后收集。即使大多数时候看起来没什么关系，但在某些情况下它可能是相关的，从故事的角度来看更有意义。
T3】

# 轰然巨浪

我们来说一个具体的例子，就是我在上一节开头提到的那个例子。

我有一个模板文件，我想通过用需要的值替换占位符字符串来填充它。为此，我给我的方法提供了两个列表，一个包含占位符键，另一个包含所需的值。

```
private static void populateTemplateFile(ArrayList<String> sKeys, ArrayList<String> sVals){
    String line="";
    String newline = System.getProperty("line.separator");
    BufferedReader reader = null;
    FileWriter writer = null;
    StringBuilder storeLine = new StringBuilder("");

    try{
        File file = new File(TEMPLATE_FILE);
        reader = new BufferedReader(new FileReader(file));
        while((line = reader.readLine()) != null){
            storeLineBuilder.append(line + newline);
        }
        reader.close();

        String storeLine = storeLineBuilder.toString();
        for (int iCtr=0; iCtr<sKeys.size();iCtr++){
            storeLine=storeLine.replaceAll(sKeys.get(iCtr), sVals.get(iCtr));
        }

        writer = new FileWriter(TARGET_FILE);
        writer.write(storeLine);
        writer.close();

    } catch(FileNotFoundException e) {
        log.error("The file " + TARGET_FILE + " could not be found!", e);
    } finally {
        if(reader != null) {
            reader.close();
        }
        if(writer != null) {
            writer.close();
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我第一次尝试使用`Stream` :
来修改这个方法

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

我弄得一团糟...在这一点上，我本以为会有一个过滤、处理、收集的三拍故事，但这就是我所拥有的:

1.  获取我之前初始化的流。
2.  对于每个键，用它对应的值替换它。对每一行重复。
3.  利润？

对我来说它看起来很笨拙，有两个原因:没有清晰的收集步骤，我们在这里有一个双重迭代，在进一步研究这个主题后，似乎在大多数情况下不建议使用 Stream。

最后，我找不到一种比目前使用的方法做得更好的方法。

# 离别的思念

我仍处于旅程的开始，但现在我对何时使用`Stream`有了更好的理解。总结一下:

*   关于你想做什么，应该有一个清晰的故事。
*   一般来说，故事应该遵循过滤->处理->收集的模式。
*   双重循环是不行的。

最后，我现在觉得我已经理解了`Stream`的用途，并且从现在开始能够更好地使用它。

直到河流的下一个转弯处。