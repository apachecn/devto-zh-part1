# %$#@ This:梳理 1.83 亿条 git 提交，找出开发人员何时以及如何使用 f-word

> 原文：<https://dev.to/walker/-this-combing-through-183-million-commits-to-find-out-when-and-how-developers-us-the-f-bomb>

编程可能会令人沮丧。没完没了的调试、含糊不清的错误消息、糟糕的文档——有很多事情会让你崩溃到流泪、跺脚或拔毛...

...或者说“操！”

然而，程序员什么时候以及为什么放弃 F-bomb？多亏了 BigQuery 上的[公共 GitHub 数据集，其中包含了数以亿计的提交及其对应的消息，我们可以尝试回答这个问题。正如 Ramiro Gomez 在去年对同一数据集的分析中所展示的那样，大多数提交消息都是乏味或无用的](https://cloud.google.com/bigquery/public-data/github)[。但是偶尔程序员在记录他们的更改时会借机表达自己，有时他们会用脏话来强调。](http://ramiro.org/blog/most-frequent-github-commit-messages/)

在数据集中的 1.83 亿条提交消息中，约有 33000 条包含单词“fuck”的某种版本，或者约每五六千条提交消息中有一条(少于谢尔盖·阿巴库莫夫在[的 F-bomb 分析](https://medium.com/@sAbakumoff/157-million-github-commits-48-thousand-f-bombs-a84cb9fab680)中发现的 48000 条，其中还包括 git 主题行)。比纯粹的总计更有趣的是程序员认为首先值得诅咒的到底是什么。此 SQL 查询选择提交消息中紧接 F-word 后面的单词:

```
SELECT next_word, count(next_word) as n FROM (
  SELECT commit, LOWER(REGEXP_EXTRACT(FIRST(message),r'(?:\w*fuck\S*\s)(\w+)')) AS next_word
  FROM [bigquery-public-data:github_repos.commits]
  WHERE
  REGEXP_MATCH(message, r'\w*fuck\S*\s\w+')
  GROUP BY
  commit)
GROUP BY next_word
ORDER BY n DESC
LIMIT 100; 
```

生成的表格可以不太麻烦的下载并可视化:
[![](img/10c7c39074a89cf778ca9f24db9ac225.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yNqo4Lzu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/twjgek1b186ncj5cxclk.png)

引用数据集中的实际提交消息使查看这些结果变得更有趣，因此示例将包含在括号中。

### “上”在上面

胜出者是“up”，你可以用它来承担责任(*我把 ui.js 移动到 lib/* 搞砸了一些事情)，把它分发给同事(*彼得*把事情搞砸了)，家庭成员(*爸爸*把事情搞砸了)，甚至无意识的存在(*机器人现在不会搞砸无声回顾问候*

### 完成短语

同样受欢迎的是完成恼怒短语的一般单词，如“it”(*fuck it 我们将使用子进程*)，“this”(*fuck it，statemanager 在异步环境中太复杂了，去掉它*)，“that”(*CSV 解析器？去他妈的:regex* )，还有“关”(*滚蛋火狐没人喜欢你*)。

### 脏话

git 的过程经常出现在“提交”、“合并”、“Git”和“github”等术语中。某些语言经常引起用户的愤怒，比如“php”(*操他妈的 PHP 狗屎:把布尔值转换成整数，因为操你这就是为什么*)“c++”(*initializer _ list 可以被强制转换成原始类型？什么他妈的 c++，什么实际的，合法的他妈的*)和“css”(*他妈的 CSS 修改*)。

广泛使用的服务也是如此，比如“heroku”(*去他妈的 heroku port* )和“travis”(*他妈的 Travis 测试失败了，在本地很管用，去他妈的这个*)。

### 赎回

当然，我们不应该只考虑消极的一面。用户用“是”(*他妈的是，终于弄明白了如何做配置的东西*)和“是”(*他妈的是以正确登录/注册为中心*)，以及“修复”(*终于他妈的修复了 bootstrap carousel bug，是由平滑滚动的 JS 代码*)和“工作”(*它他妈的工作了)来庆祝成功。像嗑药的圣诞老人一样效率低下，但却很有效。*

从这个意义上说，我们的结果准确地描绘了编程经历:大量的挫折，但如果你坚持下去，也会有一些值得的胜利。