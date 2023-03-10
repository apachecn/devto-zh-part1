# 查找地名的变位词(以 GB 为单位)

> 原文：<https://dev.to/ianturton/finding-anagrams-of-place-names-in-gb-3o38>

不久前，阿拉斯代尔·雷(Alasdair Rae)询问是否有人将变位词引擎与地名列表结合起来。

嗯，没有人站出来，所以我认为这可能是一个有趣的项目。事实证明，这很有趣，虽然我开始考虑数据结构而不是地理，但这对我可能是件好事。

我假设阿拉斯代尔可能对字母的排列不感兴趣，而是想要实际的单词(比如在纵横字谜中使用的单词)。我还把我的搜索限制在单个单词的字谜上，因为我找不到一个简单的方法来找到多个单词的答案。

首先，我把英国国家测绘局的 OpenNames 数据集塞进了 PostGIS(因为谁想扫描数百个小 csv 文件)。

然后我建立了一个 [GeoTool 的](http://geotools.org) [PostGIS datastore](http://docs.geotools.org/stable/userguide/library/jdbc/postgis.html) 并抓取了人口密集的地方。

```
Map<String, Object> params = new HashMap<String, Object>();
    params.put(PostgisNGDataStoreFactory.DBTYPE.key, PostgisNGDataStoreFactory.DBTYPE.sample);
    params.put(PostgisNGDataStoreFactory.USER.key, "username");
    params.put(PostgisNGDataStoreFactory.PASSWD.key, "password");
    params.put(PostgisNGDataStoreFactory.SCHEMA.key, "opennames");
    params.put(PostgisNGDataStoreFactory.DATABASE.key, "osdata");
    params.put(PostgisNGDataStoreFactory.HOST.key, "127.0.0.1");
    params.put(PostgisNGDataStoreFactory.PORT.key, "5432");

      DataStore ds = DataStoreFinder.getDataStore(params);
    if (ds == null) {
      throw new RuntimeException("No datastore");
    }
    SimpleFeatureSource fs = ds.getFeatureSource("opennames");
    SimpleFeatureCollection features = fs.getFeatures(CQL.toFilter("type = 'populatedPlace'")); 
```

我尝试了一种简单的方法，递归地从名字中找到每一个可能的变位词，并在英语单词的`HashMap`中查找每一个。奇怪的是，这花了很长时间，所以我想了很多(并且谷歌了一下),想出了一个更有效的方法来排序一个单词中的字母，并使用它作为包含这些字母的所有单词的关键字。然后，我可以对每个地名的字母进行排序，并进行一次查找，以找到所有可能由这些字母组成的单词。这大大加快了速度。

为了构建查找表，我使用了谷歌的`HashMultimap`(来自[的番石榴](https://github.com/google/guava))，它允许你创建一个`Collections`的`Map`并键入一个`String`。

```
private Map<String, Collection<String>> dict;

  public AnagramLookup() throws FileNotFoundException, IOException {
    //change this to point to your dictionary (one word per line)
    File f = new File("/usr/share/dict/british-english");
    HashMultimap<String, String> indexedDictionary = HashMultimap.create();
    try (BufferedReader buf = new BufferedReader(new FileReader(f))) {
      String line;
      // read each word in the dictionary
      while ((line = buf.readLine()) != null) {
        //strip out non letters
        String word = line.toLowerCase().replaceAll("\\W", "");
        //store the word against the sorted key
        indexedDictionary.put(sort(word), word);
      }
    }
    dict = indexedDictionary.asMap();
  } 
```

然后剩下要做的就是迭代每个居住的地方，获取它的名字，然后删除所有的非字母，对它的字母进行排序，并在`HashMap`中查找变位词。最后一个技巧是，如果名字本身出现在变位词列表中，就将其删除(即名字本身是一个英语单词)。

```
try (SimpleFeatureIterator itr = features.features()) {

      while (itr.hasNext()) {

        SimpleFeature f = itr.next();
        String name = (String) f.getAttribute("name1");

        current = name.toLowerCase().replaceAll("\\W", "");
        Collection<String> anagrams = getAnagrams(current);

        if(anagrams!=null && !anagrams.isEmpty()) {
          //remove the name itself if it happens to be a word
          anagrams.remove(current);
          if(!anagrams.isEmpty()) {
            results.put(name, new TreeSet<String>(anagrams));
          }
        }
      }
    } 
```

## 结果

原来 GB 地名列表有 6 个 11 个字母的变位词。

*   巴尔纳德尔松-贝拉多纳斯
*   富通绿色再造
*   吉林东部-立法
*   绿色平原-斯宾格勒里安
*   晨兴-现代化
*   沙林顿-哈林顿
*   石头角-基石

斯宾格勒学派是“奥斯瓦尔德·斯宾格勒发展的世界历史理论的或与之相关的，该理论认为所有主要文化都经历了从诞生到成熟再到衰落的类似循环发展”。而[哈林顿](https://en.oxforddictionaries.com/definition/harrington)则是“一种男式短轻便夹克，有衣领和拉链前襟。”

填字游戏的其他亮点包括《动物园》中的艾米斯·格林和《好莱坞》中的韦斯特林顿。

我已经发布了[完整的字谜列表](https://gitlab.com/snippets/1689724)和生成列表的[代码。](https://gitlab.com/snippets/1689722)

查看此[跟进](https://blog.ianturton.com/geotools/fun/anagram/2017/12/20/anagrams-world.html)世界名称。