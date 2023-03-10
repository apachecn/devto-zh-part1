# 用你的数据库！

> 原文：<https://dev.to/livioribeiro/use-your-database-5in>

如果你正在使用数据库，使用关系数据库的可能性相当高。我已经使用关系数据库很长时间了，但是从来没有真正使用过这些数据库提供的所有功能，因为我只把它们看作保存数据的仓库，还因为我认为 ORM 可以解决我所有的问题。

让我们以具有两个日期字段的实体为例:

```
import java.time.LocalDateTime
import javax.persistence.Entity
import javax.persistence.Column

@Entity
data class MyEntity(
    // other fields
) {
    @Column(name = "created_at", updatable = false)
    val createdAt: LocalDateTime = LocalDateTime.now()

    @Column(name = "updated_at")
    var updatedAt: LocalDateTime = LocalDateTime.now()
} 
```

当我们实例化该类的一个对象时，这两个日期字段将具有当前日期和时间，但是我们仍然需要在每次对对象进行更改时手动更新`updatedAt`字段:

```
val myInstance = MyEntity(/* fields */)
myInstance.updateAt = LocalDateTime.now() 
```

虽然这种方法可行，但它容易出错，因为无论我们在哪里对其对象进行更改，我们都必须更新`updatedAt`字段。我们可以隔离逻辑，将实体更改为单个服务类，但我们可以做得更好。

为什么不让数据库为我们做这项工作呢？

首先，让我们对我们的实体进行一些更改:

```
import java.time.LocalDateTime
import javax.persistence.Entity
import javax.persistence.Column

@Entity
data class MyEntity(
    // other fields
) {
    @Column(name = "created_at", insertable = false, updatable = false)
    val createdAt: LocalDateTime = LocalDateTime.MIN

    @Column(name = "updated_at", insertable = false, updatable = false)
    var updatedAt: LocalDateTime = LocalDateTime.MIN
} 
```

这两个日期字段既不能插入也不能更新，但这是可以的，因为数据库会为我们做这件事，我们只需将其模式定义如下:

```
CREATE TABLE my_table(
    -- other columns
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE FUNCTION my_table_updated() RETURNS trigger AS $$
BEGIN
    NEW.updated_at := CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER my_table_updated BEFORE UPDATE ON my_table
    FOR EACH ROW EXECUTE my_table_updated(); 
```

每次更新一行时，使用一个触发器来更新`updated_at`列。这样，我们不必在代码中做任何事情来获得我们想要的行为，并且我们也不必担心其他应用程序是否需要对数据进行更改，因为规则是在数据库级别定义的。

下一篇文章我将展示一个更复杂的例子。