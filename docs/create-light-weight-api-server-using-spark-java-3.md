# 使用 Spark Java 创建轻量级 api 服务器[3]

> 原文：<https://dev.to/silentsudo/create-light-weight-api-server-using-spark-java-3>

大家好，

继续上一节[https://dev . to/silentsudo/create-light-weight-API-server-using-spark-Java-2](https://dev.to/silentsudo/create-light-weight-api-server-using-spark-java-2)

我们已经完成了 URL 的设置，现在让我们使用 Spark Java 创建路由器，我们将按以下顺序加入 router-service-db

*   数据库ˌ资料库
*   服务
*   路由器

让我们看看数据库:
正如我们所讨论的，我们将使用集合框架创建轻量级内存数据库。
会有一个数据库类和各自的表，我们可以像`User, Tweet, Like`一样称它们为特定类型的数据存储。

下面是我们的数据库和数据存储类。
T2`AppDatabase.java`

```
package in.silentsudo.blabber.database;

public final class AppDatabase {
    private static AppDatabase ourInstance = new AppDatabase();

    public static AppDatabase getInstance() {
        return ourInstance;
    }

    private UsersStore usersStore;
    private TweetsStore tweetsStore;
    private LikesStore likesStore;

    private AppDatabase() {
        usersStore = new UsersStore();
        tweetsStore = new TweetsStore();
        likesStore = new LikesStore();
    }

    public UsersStore getUsersStore() {
        return usersStore;
    }

    public TweetsStore getTweetsStore() {
        return tweetsStore;
    }

    public LikesStore getLikesStore() {
        return likesStore;
    }
} 
```

下面的数据存储类:

`UsersStore.java`

```
package in.silentsudo.blabber.database;

import in.silentsudo.blabber.models.User;

import java.util.LinkedList;
import java.util.List;

public final class UsersStore {

    private List<User> users = new LinkedList<>();

    UsersStore() {
        //Alert, always use some encryption while storing ans distributing password
        //This is dummy data
        users.add(new User("ashish@ashish.com", "qwerty"));
    }

    public User findUser(final User user) {
        return this.users.stream()
                .filter(u ->
                        u.getEmail().equalsIgnoreCase(user.getEmail()) &&
                                u.getPassword().equalsIgnoreCase(user.getPassword())
                )
                .findFirst()
                .orElse(null);
    }
} 
```

`TweetsStore`

```
package in.silentsudo.blabber.database;

import in.silentsudo.blabber.models.Tweet;

import java.util.Collections;
import java.util.LinkedList;
import java.util.List;

public final class TweetsStore {

    private List<Tweet> tweets = new LinkedList<>();

    TweetsStore() {
    }

    public void addTweet(Tweet tweet) {
        tweets.add(tweet);
    }

    public List<Tweet> getAllTweets() {
        return Collections.unmodifiableList(this.tweets);
    }

    public void deleteTweet(Tweet tweet) {
        if (this.tweets.contains(tweet)) {
            this.tweets.remove(tweet);
        }
    }
} 
```

最后是`LikesStore`

```
package in.silentsudo.blabber.database;

import in.silentsudo.blabber.models.Like;

import java.util.Collections;
import java.util.LinkedList;
import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;
import java.util.stream.Collectors;

public final class LikesStore {
    private static final Logger logger = Logger.getLogger(LikesStore.class.getSimpleName());
    private List<Like> likes = new LinkedList<>();

    LikesStore() {
        logger.setLevel(Level.ALL);
    }

    public void addLike(Like like) {
        if (like.getTweetId() == null || like.getUserId() == null) {
            logger.warning("Invalid like details");
            return;
        }
        likes.add(like);
    }

    public void delete(Like like) {
        if (likes.contains(like)) {
            likes.remove(like);
        }
    }

    public List<Like> getAll(Long tweetId) {

        return Collections.unmodifiableList(
                this.likes.stream()
                        .filter(like -> like.getTweetId().equals(tweetId))
                        .collect(Collectors.toList())
        );
    }
} 
```

用户存储的核心功能是查找用户(如果存在)，然后将结果返回给服务类，服务类再将结果返回给路由器，最后返回给客户端。

接下来，我们将看到服务是如何调用这个数据库并对其进行操作:
我将只给出到目前为止的 TweetsService 的例子(整个项目的源代码可以在这个链接中找到)

我们将在 Tweet 和类似数据上执行某些数据库操作，这些操作似乎很常见，因此我们将在一个接口中分离出此契约，为了更好的可读取性，让服务类实现它们并进行操作。

这里是`AbstractService.java`

```
package in.silentsudo.blabber.services;

import java.util.List;

public interface AbstractService<E> {
    Object add(E e);

    void delete(E e);

    List<E> getAll();

    List<E> getAll(Long id);

} 
```

TweetsService.java 是这个接口的实现者，并提供自己的实现。

```
package in.silentsudo.blabber.services;

import in.silentsudo.blabber.database.TweetsStore;
import in.silentsudo.blabber.models.Tweet;

import java.util.List;
import java.util.logging.Level;
import java.util.logging.Logger;

public final class TweetsService implements AbstractService<Tweet> {
    private static final Logger logger = Logger.getLogger(TweetsService.class.getSimpleName());

    private final TweetsStore tweetsStore;

    public TweetsService(TweetsStore tweetsStore) {
        this.tweetsStore = tweetsStore;
        logger.setLevel(Level.ALL);
    }

    @Override
    public Object add(Tweet tweet) {
        if (tweet != null && tweet.getId() != null &&
                tweet.getUserId() != null && tweet.getContent() != null) {
            tweetsStore.addTweet(tweet);
            logger.info("added");
            return new Object();
        } else {
            logger.info("Few values are null");
            return null;
        }
    }

    @Override
    public void delete(Tweet tweet) {
        tweetsStore.deleteTweet(tweet);
    }

    @Override
    public List<Tweet> getAll() {
        return tweetsStore.getAllTweets();
    }

    @Override
    public List<Tweet> getAll(Long id) {
        return tweetsStore.getAllTweets();
    }
} 
```

我们的`services`和`database`已经准备好与路由器一起使用，在下一节中，我们将看到如何将所有这些部分连接在一起并产生 api 响应。

感谢您的阅读！