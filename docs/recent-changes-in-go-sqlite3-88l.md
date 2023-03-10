# go-sqlite3 的最新变化

> 原文：<https://dev.to/mattn/recent-changes-in-go-sqlite3-88l>

简而言之，我在 [go-sqlite3](https://github.com/mattn/go-sqlite3) 中添加了一些重要的特性。

# 挂钩事件

之前推出过，但不是默认。现在你可以默认使用它。要使用这个，你需要注册 hook。

说实话，我并不觉得这种方式不聪明，只是在围棋高手里没办法做。

```
sql.Register("sqlite3_with_hook_example",
    &sqlite3.SQLiteDriver{
        ConnectHook: func(conn *sqlite3.SQLiteConn) error {
            conn.RegisterUpdateHook(func(op int, db string, table string, rowid int64) {
                switch op {
                case sqlite3.SQLITE_INSERT:
                    log.Println("Notified of insert on db", db, "table", table, "rowid", rowid)
                }
            })
            return nil
        },
    }) 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以看到记录何时被插入/删除/更新。我认为类似于触发器，但更容易处理事件。例如，这可能有助于数据库的复制。

# 获取极限

这也需要注册钩子。

```
var sqlite3conn *sqlite3.SQLiteConn
sql.Register("sqlite3_with_limit", &sqlite3.SQLiteDriver{
    ConnectHook: func(conn *sqlite3.SQLiteConn) error {
        sqlite3conn = conn
        return nil
    },
})
db, err := sql.Open("sqlite3_with_limit", "./foo.db")
if err != nil {
    log.Fatal(err)
}
defer db.Close() 
```

Enter fullscreen mode Exit fullscreen mode

连接到您的数据库后，您可以使用`SQLite3Conn#GetLimit()`。

```
varnum := sqlite3conn.GetLimit(sqlite3.SQLITE_LIMIT_VARIABLE_NUMBER) 
```

Enter fullscreen mode Exit fullscreen mode

# 将 Go 函数导出到 SQL 中

将 Go 函数导出到 SQL 中。

```
sql.Register("sqlite3_custom", &sqlite.SQLiteDriver{
    ConnectHook: func(conn *sqlite.SQLiteConn) error {
        if err := conn.RegisterFunc("pow", pow, true); err != nil {
            return err
        }
        return nil
    },
}) 
```

Enter fullscreen mode Exit fullscreen mode

`pow`应该是如下图。就叫`Math.Pow`。注意，对于 func 的参数/返回类型，必须使用 int64 而不是 int。

```
func pow(x, y int64) int64 {
    return int64(math.Pow(float64(x), float64(y)))
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是启用该特性所要做全部工作。所以现在您可以在查询中调用`pow`。

```
err = db.QueryRow("SELECT pow(2,3)").Scan(&i)
if err != nil {
    log.Fatal("POW query error:", err)
} 
```

Enter fullscreen mode Exit fullscreen mode

如果要在查询中使用正则表达式:

```
sql.Register("sqlite3_with_go_func", &sqlite3.SQLiteDriver{
    ConnectHook: func(conn *sqlite3.SQLiteConn) error {
        return conn.RegisterFunc("regexp", func(re, s string) (bool, error) {
                return regexp.MatchString(re, s)
        }, true)
    },
}) 
```

Enter fullscreen mode Exit fullscreen mode