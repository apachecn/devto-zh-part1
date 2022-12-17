# 转到:保持数据库结构

> 原文：<https://dev.to/dnnrly/go-persist-db-structs-fg2>

所以这个例子的原因是我正在努力写一个结构到数据库。具体来说，它是来自`github.com/golang/geo`的`r2.Point`结构。这是一个简单的 2d 坐标类型，带有`float64` X 和 Y 值。就是这样。我想把它存储在数据库里。

这些例子的代码可以在[这里](https://github.com/dnnrly/go-examples/tree/master/dbvalues)找到。

查看错误示例:

```
go run broken.go 
```

您应该会看到以下结果:

```
2018/01/24 20:55:10 sql: converting argument $2 type: unsupported type r2.Point, a struct
exit status 1 
```

这是因为 DB 驱动程序的实现者需要支持以下类型:

*   `int64`
*   `float64`
*   `bool`
*   `[]byte`
*   `string`
*   `time.Time`

注意到`r2.Point`不是其中之一吗？

那么我们该怎么办呢？嗯，`database/sql`包背后的人考虑了一下这个问题，并提供了[估价师](https://godoc.org/database/sql/driver#Valuer)接口来帮助这个转换。

我们需要确保我们尝试编写一个符合这个接口的类型，这样驱动程序就可以转换成它能理解的类型。

看一看`write.go`看看我们是如何做到这一点的。如果您是新手，请注意我们已经使用 composition 编写了一个类型来为`r2.Point`添加更多的行为。以下是所做的:

```
// Location allows us to implement the Value() function for writing r2.Point to the database
type Location struct {
    r2.Point
}

// Value does the conversion to a string
func (p *Location) Value() (driver.Value, error) {
    data := p.String()
    log.Printf("Converted r2.Point to a string: %s", data)
    return data, nil
} 
```

使用
运行文件

```
go run write.go 
```

现在我们得到*这个*错误:

```
2018/01/24 21:33:12 Converted r2.Point to a string: (1.000000000000, 2.000000000000)
2018/01/24 21:33:12 sql: Scan error on column index 1: unsupported Scan, storing driver.Value type []uint8 into type *main.Location
exit status 1 
```

再次，包实现者来拯救我们——我们可以使用[扫描仪](https://godoc.org/database/sql#Scanner)。DB 驱动程序将使用这个接口让我们的结构从 DB 保存数据的类型转换成我们想要的类型。奇怪的是，当我们使用`sqlite3`时，结果是`[]uint8`。最后一个文件，`writeread.go`实现了`Scan`功能:

```
// Scan converts from a string to a Location
func (p *Location) Scan(v interface{}) error {
    var s string
    switch v.(type) {
    case string:
        s = v.(string)
    case []uint8:
        s = string(v.([]uint8))
    default:
        return errors.Errorf("Don't understand type %T", v)
    }

    _, err := fmt.Sscanf(s, "(%f,%f)", &(p.X), &(p.Y))
    if err != nil {
        return err
    }

    return nil
} 
```

运行时使用:

```
go run writeread.go 
```