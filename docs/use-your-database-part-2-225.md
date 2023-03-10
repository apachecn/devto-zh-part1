# 用你的数据库！(第二部分)

> 原文：<https://dev.to/livioribeiro/use-your-database-part-2-225>

在上一篇文章中，我展示了一个简单的例子，当数据改变时，使用数据库触发器来设置“updated”字段。现在我将展示一个更复杂的例子。

让我们以任务列表的模式为例:

```
CREATE TABLE IF NOT EXISTS tasklist (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100)
);

CREATE TABLE IF NOT EXISTS task (
    tasklist_id INTEGER NOT NULL,
    name VARCHAR(100) NOT NULL,
    "order" INTEGER NOT NULL CHECK ("order" > 0),

    PRIMARY KEY (tasklist_id, name),
    FOREIGN KEY (tasklist_id) REFERENCES tasklist (id)
); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有两个表，`tasklist`和`task`，后者包含一个名为`order`的列，这显然是一个特定列表的任务的顺序号。

现在开始我们的问题:要插入一个新任务，我们需要获取列表的最大顺序号，增加 1，然后插入新任务。但是两个人同时尝试插入一个任务会怎么样呢？

另一个问题是改变任务的顺序号，因为你需要改变其他任务的顺序。删除任务也有同样的问题。

为了解决这些问题，我们可以告诉数据库在插入新任务时设置顺序号，并在更新或删除任务时改变任务的顺序。

首先，让我们创建一个数据库函数来帮助我们:

```
CREATE OR REPLACE FUNCTION task_shift_order(
    list_id INTEGER,
    new_order INTEGER,
    old_order INTEGER
    ) RETURNS INTEGER AS $$
DECLARE
    max_order INTEGER;
BEGIN
    -- if the task table is empty, max("order") will return null
    -- use coalesce to return 0 instead of null
    SELECT coalesce(max("order"), 0) INTO max_order
    FROM task
    WHERE tasklist_id = list_id;

    -- do not allow order number greater then max order
    IF new_order > max_order THEN
        RAISE EXCEPTION 'New order (%) exceeds number of tasks (%) in tasklist(id = %)', new_order, max_order, list_id;
    END IF;

    IF new_order IS NULL THEN
    -- task is being inserted without order
        new_order := max_order + 1;
    ELSIF old_order IS NULL THEN
    -- task is being inserted at specific order
        UPDATE task SET "order" = "order" + 1
        WHERE tasklist_id = list_id
        AND "order" >= new_order;
    ELSIF new_order > old_order THEN
    -- task is assigned a higher order
        UPDATE task SET "order" = "order" - 1
        WHERE tasklist_id = list_id
        AND "order" > old_order AND "order" <= new_order;
    ELSE
    -- task is assigned a lower order
        UPDATE task SET "order" = "order" + 1
        WHERE tasklist_id = list_id
        AND "order" >= new_order AND "order" < old_order;
    END IF;

    -- return new order number when inserting without specifying the order
    RETURN new_order;
END;
$$ LANGUAGE plpgsql; 
```

Enter fullscreen mode Exit fullscreen mode

该函数将更新`task`表上的订单，并在插入新任务时返回新的订单编号。

现在我们只需要在插入任务之前、更新任务订单之前和删除任务之后设置三个触发器:

插入前:

```
CREATE FUNCTION task_insert_order() RETURNS TRIGGER AS $$
BEGIN
    NEW."order" := task_shift_order(NEW.tasklist_id, NEW."order", NULL);
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER task_insert_order BEFORE INSERT ON task
    FOR EACH ROW
    EXECUTE PROCEDURE task_insert_order(); 
```

Enter fullscreen mode Exit fullscreen mode

更新前:

```
CREATE FUNCTION task_update_order() RETURNS TRIGGER AS $$
BEGIN
    NEW."order" := task_shift_order(NEW.tasklist_id, NEW."order", OLD."order");
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER task_update_order BEFORE UPDATE ON task
    FOR EACH ROW
    WHEN (pg_trigger_depth() = 0 -- prevent trigger recursion
        AND NEW."order" <> OLD."order") -- only execute when order changes
    EXECUTE PROCEDURE task_update_order(); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要设置条件`WHEN (pg_trigger_depth() = 0 AND NEW."order" <> OLD."order")`来避免递归，因为触发器会更新`task`表本身。

对于 after delete 触发器，我们不需要调用`task_shift_order()`，因为逻辑更简单:

```
CREATE FUNCTION task_delete_order() RETURNS TRIGGER AS $$
BEGIN
    UPDATE task SET "order" = "order" - 1
        WHERE tasklist_id = OLD.tasklist_id
        AND "order" > OLD."order";
    RETURN OLD;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER task_delete_order AFTER DELETE ON task
    FOR EACH ROW
    EXECUTE PROCEDURE task_delete_order(); 
```

Enter fullscreen mode Exit fullscreen mode

您可能已经注意到，我没有在`tasklist_id`和`order`列上使用 unique 约束。这是因为我无法在不引起冲突的情况下更新订单。如果你知道怎么做，请在评论中告诉我。