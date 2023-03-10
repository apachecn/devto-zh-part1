# 在 MongoDB 文档中操作数组

> 原文：<https://dev.to/damcosset/manipulating-arrays-in-mongodb-documents>

*还在[我的博客](https://wp.me/p8TnsH-9Z)上发表了这篇 T3*

## 介绍

在本文中，我们将研究 MongoDB 中的更新操作符。具体来说，我们将扩展数组更新操作符。

我将使用 mongo shell 来展示示例。如果你想在你的终端上学习，你可以在这里找到安装指南

一旦所有东西都安装好了，您就可以用 *mongod* 启动一个 MongoDB 实例。然后，在一个新的终端窗口中，键入*mongo*(windows 上的*mongo.exe*)启动 shell。

我们通过使用 updateOne()或 updateMany()方法来更新 MongoDB 中的文档。这两种方法都有两个参数。第一个是一个过滤器，返回匹配这个过滤器的文档。第二个指定我们希望应用于返回文档的更改。

每个更新操作符都以一个$开始。先说*$推*。

### $推送

这个可能很容易掌握，push 是一种流行的跨编程语言的列表方法。它在数组末尾添加一个元素。如果数组不存在，它将创建一个新的并添加元素。

让我们从这个代表用户的文档开始:

```
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany"
}
```

We want to allow the user to add his favorite colors. At the moment, there is no field favoriteColors. Not to worry, *$push* will create it for us. Here is the syntax:

```
> db.user.updateOne(
    {"name": "Joe"}, 
    {$push: {"favoriteColors": "orange"}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "favoriteColors" : [
                "orange"
        ]
}
```

*Note*: the pretty() function can be used to nicely format the mongo shell output.
The second parameter of our updateOne function is an object that starts with our update operator $push. We give it as a value what we want to update in our document. We are basically saying:
*Push the string orange at the end of the array favoriteColors*
Let's push another color now:

```
> db.user.updateOne(
    {"name": "Joe"}, 
    {$push: {"favoriteColors": "blue"}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "favoriteColors" : [
                "orange",
                "blue"
        ]
}
```

Nice! Now, this is very simple form of push, one element at a time. MongoDB allows you to use several $-modifiers to perform more complex array operations. For example, if we want to push several items, we can use $each.

### 每个美元

Here is how you would add several items in one query:

```
> db.user.updateOne(
    {"name": "Joe"}, 
    {$push: {"siblings": { $each: ["Joey", "Sara", "Ben"]}}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "siblings" : [
                "Joey",
                "Sara",
                "Ben"
        ]
}
```

There you go, MongoDB mapped through the array specified in the $each modifier to add them one by one.

### $切片

Let's add another modifier, *$slice*. Say we want our user to list 3 books. The array must not be bigger than 3\. We can combine *$push* with *$slice* to achieve this:

```
> db.user.updateOne(
    {"name": "Joe"}, 
    {$push: 
        {"top3Books": 
            {$each: ["Book 1", "Book 2", "Book 3", "Book 4", "Book 5"], 
            $slice: -3 }}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "favoriteColors" : [
                "orange",
                "blue"
        ],
        "top3Books" : [
                "Book 3",
                "Book 4",
                "Book 5"
        ]
}
```

Our array will never be bigger than 3 elements. *$slice* keeps our three last entries because I gave it a value of -3\. If I gave it a value of 3, we would have this document:

```
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "favoriteColors" : [
                "orange",
                "blue"
        ],
        "top3Books" : [
                "Book 1",
                "Book 2",
                "Book 3"
        ]
}
```

You can control if new entries override the old ones or not. Of course, if we entered less than 3 items or 3 items, all of them would be in our array.
Next, let's say that we ask our user to rate the movies he saw. We want to keep only the ones with the higher ratings. To do this, we can combine our *$push* with an *$each*, then a *$slice* and finally a *$sort*. Like so:

```
> db.user.updateOne(
    {"name": "Joe"}, 
    {$push: {"top3Movies":{ $each: [
        {"name": "Star Wars VI", "rating": 5.3}, 
        {"name": "Her", "rating": 3.1},
        {"name": "John Wick 1", "rating": 7.8}, 
        {"name": "Jaws", "rating": 6.4}, 
        {"name": "Saw", "rating": 2.3}, 
        {"name": "The Lord of the rings", "rating": 8.4}, 
        {"name": "The Hobbit", "rating": 4.3}], 
        $slice: -3, 
        $sort: {"rating": 1 }}}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "top3Movies" : [
                {
                        "name" : "Jaws",
                        "rating" : 6.4
                },
                {
                        "name" : "John Wick 1",
                        "rating" : 7.8
                },
                {
                        "name" : "The Lord of the rings",
                        "rating" : 8.4
                }
        ]
}
```

Let's break this down a bit. As always, we started with the *$push* operator. We inform MongoDB that the field name is "top3Movies". We specify to our *$each* operator an array of documents we want to push to our field. Each document has a name and a rating field. Next, we use *$slice: -3* to indicates that we wan't to keep only 3 documents in this array. Finally, we use *$sort* on the rating field to keep the 3 movies with the higher ratings.
*Note*: If you gave -1 as a value for the sort field, we would have the 3 lowest ratings in our array.

### 集合数组

Maybe you want to treat your array as a set. A set is a list where there are no duplicates. There are two ways you could do this, by using *$ne* or *$addToSet*.

### 一美元

*$ne* is used in the first parameter, the filter query, like this:

```
> db.user.updateOne(
    {"favoriteColors": {$ne : "orange"}},
    {$push: {"favoriteColors": "orange"}} )
{ "acknowledged" : true, "matchedCount" : 0, "modifiedCount" : 0 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "favoriteColors" : [
                "orange",
                "blue"
        ]
}

```

我们对 MongoDB 说，检查 favoriteColors 是否包含值“orange”。如果没有，按“橙色”否则什么也不做。因为我们之前已经将 orange 添加到该文档中，所以该文档没有更改(modifiedCount: 0)。

### $addToSet

当 *$ne* 不能用，或者不能描述你想要的东西时，你可能要用 *$addToSet* 。我的用户有几个电子邮件地址，但是我不想确保它们在我的文档中都是唯一的。

```
> db.user.updateOne(
    {"name":"Joe"}, 
    {$addToSet: { "emails" : {$each: ["joe@hotmail.com", "joe@yahoo.com", "joe@gmail.com"]}}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 1 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "emails" : [
                "joe@hotmail.com",
                "joe@yahoo.com",
                "joe@gmail.com"
        ]
}
```

*$addToSet* 的功能类似于 *$push* ，但是检查该值是否已经在数组中。让我们尝试添加一个已经存在的电子邮件地址:

```
> db.user.updateOne({"name": "Joe"}, {$addToSet: {"emails" : "joe@hotmail.com"}})
{ "acknowledged" : true, "matchedCount" : 1, "modifiedCount" : 0 }
> db.user.find().pretty()
{
        "_id" : ObjectId("595fee69e331f76e40447a33"),
        "name" : "Joe",
        "age" : 35,
        "country" : "Germany",
        "emails" : [
                "joe@hotmail.com",
                "joe@yahoo.com",
                "joe@gmail.com"
        ]
}
```

耶！没有重复项(修改计数:0)。正如我们所见，您可以将 *$addToSet* 与 *$each* 结合使用。你也可以使用 *$slice* 或者 *$sort* 和 *$addToSet* 。

第一部分到此为止。在下一篇文章中，我们将探索从数组中移除元素并修改元素的位置。