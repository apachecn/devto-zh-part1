# 在 React Native 中构建 Algolia 支持的搜索

> 原文：<https://dev.to/bilalbudhani/building-algolia-powered-search-in-react-native-8ia>

# React Native 中构建 Algolia 支持的搜索

简而言之，Algolia 提供了搜索即服务，如果你认为这是一个非常酷的服务。它消除了构建和维护搜索基础设施的所有繁重工作，为您提供了一个良好的 API 和一个强大的监控功能来检查一切。

Algolia 的团队已经为大多数编程语言开源了 API 客户端&也为一些流行的平台构建了库。 [React-InstantSearch](https://github.com/algolia/react-instantsearch) 是一个
这样的库，我们将在这个例子中使用。

这是我们将在这篇博文中构建的演示

[https://www.youtube.com/embed/YR6Rf7NB-2g](https://www.youtube.com/embed/YR6Rf7NB-2g)

添加我们将要用来构建它的库

```
 yarn add react-instantsearch 
```

让我们在编辑器中打开`App.js`并初始化即时搜索

```
<InstantSearch
   appId="TLCDTR8BIO"
   apiKey="686cce2f5dd3c38130b303e1c842c3e3"
   indexName="repositories"
>
<View style={styles.searchBoxContainer}>
  <SearchBox />
</View>
  <Results />
</InstantSearch> 
```

上面的即时搜索组件将增强我们的搜索能力——把它想象成一个
管道，它以 appId，apiKey & indexName 为道具，把我们连接到
Algolia 服务。这个组件也采取子组件，将提供
我们一个搜索框&提供的结果。

让我们编写搜索框组件，以便能够对数据执行搜索

```
import { connectSearchBox } from "react-instantsearch/connectors";
//...
const SearchBox = connectSearchBox(({ refine, currentRefinement }) => {
return (
        <TextInput
           style={styles.textBox}
           onChangeText={text => refine(text)}
           value={currentRefinement}
           placeholder="Search Something"
           clearButtonMode="always"
           spellCheck={false}
           autoCorrect={false}
           autoCapitalize="none"
         />
       );
}); 
```

InstantSearch 库附带了连接器，它提供了随时可用的
函数，这些函数可以与组件挂钩，以便能够执行定义的
动作——节省了我们编写这些逻辑的所有精力。

在这种情况下，`connectSearchBox`使我们能够连接我们的`TextInput`和
直接在我们的数据集上执行搜索(很酷吧？哼)。

现在让我们添加`Results`组件来处理 Algolia
服务提供的结果集。

```
import { connectInfiniteHits } from "react-instantsearch/connectors";
//...
const Results = connectInfiniteHits(({ hits, hasMore, refine }) => {
const onEndReached = () => {
  if (hasMore) {
    refine();
  }
};
return (
    <FlatList
      data={hits}
      onEndReached={onEndReached}
      keyExtractor={repo => repo.objectID}
      renderItem={({ item }) => <Repository repo={item} />}
    />
   );
}); 
```

类似地，`connectInfiniteHits`函数为我们提供了可以被
用来显示结果集的数据。

现在让我们编写负责显示搜索项
并突出显示搜索项的组件

```
const Repository = ({ repo }) => (
  <View style={styles.repoContainer}>
    <FontAwesome name="github" style={styles.repoIcon} />
    <View style={{ flex: 1 }}>
      <Text style={styles.repoTitle}>

  <Highlight attributeName="name" hit={repo} style={styles.repoTitle} />
      </Text>
      <Text
        ellipsizeMode="tail"
        numberOfLines={2}
        style={styles.repoDescription}
      >
       {repo.description}
     </Text>
   </View>
   <View style={styles.metaContainer}>
     <FontAwesome name="star" style={styles.starIcon} />
     <Text>{repo.stars}</Text>
   </View>
  </View>
); 
```

如果你熟悉 React Native，那么除了组件`Highlight`，你应该对上面的代码
相当熟悉(不要担心，我已经在下面解释了
)。

除了一件事，一切都准备好了。现在，让我们写下拼图的最后一块。我将把组件分解成小块，使
更容易理解。

正如我们所料，库附带了一个连接器来帮助我们解决这个问题

```
import { connectHighlight } from "react-instantsearch/connectors";

//...
const Highlight = connectHighlight(
({ highlight, attributeName, hit }) => {
const parsedHit = highlight({
    attributeName,
    hit,
}); 
```

如果你还记得`Repository`组件代码，我们已经使用了`Highlight`组件
并传递了一些道具(参考上面的)。我们将接受由`connectHighlight`提供的多一个
道具，称为`highlight`

我们将使用`highlight` &其他道具组成`parsedHit`函数。这个
函数将返回给我们一个具有属性
`isHighlighted`的对象数组——它可以用来在我们的
应用程序中构建高亮功能。让我们完成上面的组件代码

```
const highlightedHit = parsedHit.map((part, idx) => {
if (part.isHighlighted) {
   return (
     <Text key={idx} style={{ backgroundColor: "#ffff00" }}>
        {part.value}
     </Text>
   );
}
 return part.value;
}); 
```

*如果*部分内容高亮显示，那么用背景色为黄色的`Text`组件
包装它*否则*什么也不做，只返回内容。

就是这样！你现在应该有一个完整的搜索功能，带有*搜索*术语
高亮功能。您可以更有创意，根据自己的设计需求添加*动画*或任何其他
功能。

为了简洁起见，我将代码片段保持在最少——上面视频中显示的
示例应用程序的全部代码都托管在 Github 上

Github 链接:[https://github . com/code infuse/react-native-github-algolia-search](https://github.com/codeinfuse/react-native-github-algolia-search)

最初发布于:[https://blog . code infuse . com/building-algolia-powered-search-in-react-native-ee 3621775 f 2](https://blog.codeinfuse.com/building-algolia-powered-search-in-react-native-ee3621775f2)