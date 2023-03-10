# 。净异常–系统。Data.SqlTypes.SqlTypeException

> 原文：<https://dev.to/airbrake/net-exceptions--systemdatasqltypessqltypeexception-3bm>

我们目前的[快接近尾声了**。NET 异常处理**](https://airbrake.io/blog/dotnet-exception-handling/exception-class-hierarchy) 系列，今天我们来看看**系统。data . sqltypes . sqltypeexception**。`SqlTypeException`的出现是使用 [`System.Data.SqlTypes`](https://docs.microsoft.com/en-us/dotnet/api/system.data.sqltypes?view=netframework-4.7.1) 名称空间类时出错的结果。

在本文中，我们将通过查看它在整体中的位置来检查`SqlTypeException`。NET 异常层次结构。然后，我们将看一个全功能的 C#代码示例，它将说明一种连接到 ADO.NET 数据源、执行查询的特定技术，以及我们如何在某些情况下遇到`SqlTypeExceptions`，尤其是在处理异常或难以管理的数据类型时。我们开始吧！

## 技术破落

全部。NET 异常是 [`System.Exception`](https://airbrake.io/blog/dotnet-exception-handling/exception-class-hierarchy) 基类的派生类，或者从其中的另一个继承类派生。此错误的完整异常层次结构如下:

*   [T2`System.Object`](https://docs.microsoft.com/en-us/dotnet/api/system.object)
    *   [T2`System.Exception`](https://docs.microsoft.com/en-us/dotnet/api/system.exception)
    *   [T2`System.SystemException`](https://docs.microsoft.com/en-us/dotnet/api/system.systemexception)
        *   `SqlTypeException`

## 完整代码示例

下面是我们将在本文中使用的完整代码示例。如果您想亲自试验代码，看看一切是如何工作的，可以复制并粘贴它。

```
using System;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Utility;

namespace Airbrake.Data.SqlTypes.SqlTypeException
{
    internal class Program
    {
        private const string ConnectionString = @"Data Source=I7\SQLEXPRESS;Initial Catalog=pubs;Integrated Security=True";

        private enum SqlCommandExecutionType
        {
            NonQuery,
            Reader,
            Scalar,
            XmlReader
        }

        private static void Main()
        {
            Logging.LineSeparator("INSERT VALID, PROPER DATE");
            ExecuteQuery(GetQueryStringFromBook(
                new Book("Magician", "Raymond E. Feist", 681, new DateTime(1982, 10, 1))
            ));

            Logging.LineSeparator("GET DATA");
            ExecuteQuery("SELECT * FROM dbo.Book;", SqlCommandExecutionType.Reader);

            Logging.LineSeparator("INSERT INVALID DATE");
            ExecuteQuery(GetQueryStringFromBook(
                new Book("Silverthorn", "Raymond E. Feist", 432, new DateTime(1750, 1, 1))
            ));

            Logging.LineSeparator("INSERT INVALID, CONVERTED DATE");
            ExecuteQuery(GetQueryStringFromBook(
                new Book("A Darkness At Sethanon", "Raymond E. Feist", 527, new DateTime(1750, 1, 1)), true)
            );
        }

        /// <summary>
        /// Executes the passed query string, using the passed SqlCommandExecutionType.
        /// </summary>
        /// <param name="query">Query string to execute.</param>
        /// <param name="type">SqlCommandExecutionType to use, if applicable.</param>
        private static void ExecuteQuery(string query, SqlCommandExecutionType type = SqlCommandExecutionType.NonQuery)
        {
            // Instantiate connection in using block to properly close afterward.
            using (var connection = new SqlConnection(ConnectionString))
            {
                // Instantiate a command.
                var command = new SqlCommand(query, connection);

                try
                {
                    connection.Open();
                    // If no command text, return.
                    if (command.CommandText == "") return;

                    // Check passed execution type.
                    switch (type)
                    {
                        case SqlCommandExecutionType.NonQuery:
                            command.ExecuteNonQuery();
                            break;
                        case SqlCommandExecutionType.Reader:
                            var reader = command.ExecuteReader();
                            while (reader.Read())
                            {
                                var data = new object[reader.FieldCount - 1];
                                reader.GetValues(data);
                                Logging.Log(data);
                            }
                            reader.Close();
                            break;
                        case SqlCommandExecutionType.Scalar:
                            command.ExecuteScalar();
                            break;
                        case SqlCommandExecutionType.XmlReader:
                            var xmlReader = command.ExecuteXmlReader();
                            while (xmlReader.Read())
                            {
                                Logging.Log(xmlReader);
                            }
                            xmlReader.Close();
                            break;
                        default:
                            command.ExecuteNonQuery();
                            break;
                    }
                }
                catch (System.Data.SqlTypes.SqlTypeException exception)
                {
                    // Output expected SqlTypeExceptions.
                    Logging.Log(exception);
                }
                catch (SqlException exception)
                {
                    // Output unexpected SqlExceptions.
                    Logging.Log(exception, false);
                }
                catch (Exception exception)
                {
                    // Output unexpected Exceptions.
                    Logging.Log(exception, false);
                }
            }
        }

        /// <summary>
        /// Create a query string from passed Book.
        /// </summary>
        /// <param name="book">Book from which to create query string.</param>
        /// <param name="shouldChangeDateType">Determines if date values should be converted to compatible type.</param>
        /// <returns>Query string.</returns>
        private static string GetQueryStringFromBook(IBook book, bool shouldChangeDateType = false)
        {
            try
            {
                if (shouldChangeDateType)
                {
                    return "INSERT INTO dbo.Book (Title, Author, PageCount, PublicationDate) " +
                           $"VALUES ('{book.Title}', '{book.Author}', '{book.PageCount}', '{new SqlDateTime(book.PublicationDate.Value)}');";
                }
                return "INSERT INTO dbo.Book (Title, Author, PageCount, PublicationDate) " +
                       $"VALUES ('{book.Title}', '{book.Author}', '{book.PageCount}', '{book.PublicationDate}');";
            }
            catch (System.Data.SqlTypes.SqlTypeException exception)
            {
                // Output expected SqlTypeExceptions.
                Logging.Log(exception);
            }
            catch (SqlException exception)
            {
                // Output unexpected SqlExceptions.
                Logging.Log(exception, false);
            }
            catch (Exception exception)
            {
                // Output unexpected Exceptions.
                Logging.Log(exception, false);
            }
            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个代码示例也使用了 [`Book.cs`](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Book.cs) 类，其完整代码可以通过 GitHub 在这里看到[。](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Book.cs)

该代码示例还使用了 [`Logging.cs`](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Logging.cs) 助手类，其完整代码可以通过 GitHub 在这里看到[。](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Logging.cs)

## 应该什么时候用？

因为`SqlTypeException`只在处理 [`System.Data.SqlTypes`](https://docs.microsoft.com/en-us/dotnet/api/system.data.sqltypes?view=netframework-4.7.1) 命名空间类时出现，所以让我们从一个简单的 SQL 数据库连接和查询开始。我们的`Program`类有一个属性和一个枚举作为开始，我们稍后将使用它来简化我们的连接和查询方法:

```
internal class Program
{
    private const string ConnectionString = @"Data Source=I7\SQLEXPRESS;Initial Catalog=pubs;Integrated Security=True";

    private enum SqlCommandExecutionType
    {
        NonQuery,
        Reader,
        Scalar,
        XmlReader
    }

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的主要查询逻辑发生在`ExecuteQuery(string query, SqlCommandExecutionType type = SqlCommandExecutionType.NonQuery)`方法中:

```
/// <summary>
/// Executes the passed query string, using the passed SqlCommandExecutionType.
/// </summary>
/// <param name="query">Query string to execute.</param>
/// <param name="type">SqlCommandExecutionType to use, if applicable.</param>
private static void ExecuteQuery(string query, SqlCommandExecutionType type = SqlCommandExecutionType.NonQuery)
{
    // Instantiate connection in using block to properly close afterward.
    using (var connection = new SqlConnection(ConnectionString))
    {
        // Instantiate a command.
        var command = new SqlCommand(query, connection);

        try
        {
            connection.Open();
            // If no command text, return.
            if (command.CommandText == "") return;

            // Check passed execution type.
            switch (type)
            {
                case SqlCommandExecutionType.NonQuery:
                    command.ExecuteNonQuery();
                    break;
                case SqlCommandExecutionType.Reader:
                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        var data = new object[reader.FieldCount - 1];
                        reader.GetValues(data);
                        Logging.Log(data);
                    }
                    reader.Close();
                    break;
                case SqlCommandExecutionType.Scalar:
                    command.ExecuteScalar();
                    break;
                case SqlCommandExecutionType.XmlReader:
                    var xmlReader = command.ExecuteXmlReader();
                    while (xmlReader.Read())
                    {
                        Logging.Log(xmlReader);
                    }
                    xmlReader.Close();
                    break;
                default:
                    command.ExecuteNonQuery();
                    break;
            }
        }
        catch (System.Data.SqlTypes.SqlTypeException exception)
        {
            // Output expected SqlTypeExceptions.
            Logging.Log(exception);
        }
        catch (SqlException exception)
        {
            // Output unexpected SqlExceptions.
            Logging.Log(exception, false);
        }
        catch (Exception exception)
        {
            // Output unexpected Exceptions.
            Logging.Log(exception, false);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该方法首先建立到本地`ConnectionString`属性的连接，在本例中，连接到本地`Sql Express`实例(但是可以使用任何有效的连接字符串)。通过在一个`using`块中建立连接，我们确保一旦这个代码块完成执行，连接就会自动关闭。

我们使用连接和传递的`query`字符串来创建一个新的`SqlCommand`实例，然后尝试在我们的`try-catch`块中`Open()`连接。我们确保`CommandText`属性不为空，因为如果我们在代码中的其他地方得到异常，可能会阻止`SqlCommand`正确地形成，就会发生这种情况。

最后，我们对传递的`SqlCommandExecutionType type`参数执行`switch`检查，以确定我们需要如何处理这个特定的`query`字符串。对于像`INSERT`或`DELETE`命令这样的东西，我们通常会使用`ExecuteNonQuery()`，而通过`SELECT`读取通常会使用`ExecuteReader()`。这个基本结构显然可以扩展很多，以正确处理不同类型的传入数据结构，但它将服务于我们在这里的基本目的。如果我们正在执行一个有输出的查询，我们使用`Logging.Log(...)`方法将信息输出到日志中。

我们在这里使用我们的`Book`类来创建一个简单的数据结构，我们可以尝试将它插入到我们的数据库中。我已经通过这个 SQL 查询创建了`Book`数据库表:

```
CREATE TABLE [dbo].[Book] (
    [Id] INT IDENTITY (1, 1) NOT NULL,
    [Title] NCHAR (100) NOT NULL,
    [Author] NCHAR (100) NOT NULL,
    [PageCount] INT NULL,
    [PublicationDate] DATETIME NOT NULL,
    PRIMARY KEY CLUSTERED ([Id] ASC)
); 
```

Enter fullscreen mode Exit fullscreen mode

有了这个基本的数据结构和我们的`Book`类的属性，我们可以创建`GetQueryFromBook(IBook book, bool shouldChangeDataType = false)`方法:

```
/// <summary>
/// Create a query string from passed Book.
/// </summary>
/// <param name="book">Book from which to create query string.</param>
/// <param name="shouldChangeDateType">Determines if date values should be converted to compatible type.</param>
/// <returns>Query string.</returns>
private static string GetQueryStringFromBook(IBook book, bool shouldChangeDateType = false)
{
    try
    {
        if (shouldChangeDateType)
        {
            return "INSERT INTO dbo.Book (Title, Author, PageCount, PublicationDate) " +
                    $"VALUES ('{book.Title}', '{book.Author}', '{book.PageCount}', '{new SqlDateTime(book.PublicationDate.Value)}');";
        }
        return "INSERT INTO dbo.Book (Title, Author, PageCount, PublicationDate) " +
                $"VALUES ('{book.Title}', '{book.Author}', '{book.PageCount}', '{book.PublicationDate}');";
    }
    catch (System.Data.SqlTypes.SqlTypeException exception)
    {
        // Output expected SqlTypeExceptions.
        Logging.Log(exception);
    }
    catch (SqlException exception)
    {
        // Output unexpected SqlExceptions.
        Logging.Log(exception, false);
    }
    catch (Exception exception)
    {
        // Output unexpected Exceptions.
        Logging.Log(exception, false);
    }
    return null;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法试图从传递的`IBook book`参数对象创建一个简单的`INSERT`查询字符串。这里没有太多的逻辑，除了`bool shouldChangeDataType`参数值，它决定了我们是否应该在将`DateTime`参数插入查询字符串之前尝试改变它的数据类型。我们一会儿会看到这是怎么回事。

为了测试一切，我们将从创建一个新的`Book`实例开始，从这个新的`Book's`属性中检索一个`INSERT`查询字符串，然后通过`ExecuteQuery(...)`方法执行查询。为了确认一切正常，我们将尝试一个简单的`SELECT`查询来跟踪我们的插入，看看我们的`Book`是否真的被添加到数据库:

```
private static void Main()
{
    Logging.LineSeparator("INSERT VALID, PROPER DATE");
    ExecuteQuery(GetQueryStringFromBook(
        new Book("Magician", "Raymond E. Feist", 681, new DateTime(1982, 10, 1))
    ));

    Logging.LineSeparator("GET DATA");
    ExecuteQuery("SELECT * FROM dbo.Book;", SqlCommandExecutionType.Reader);

    // ..

} 
```

Enter fullscreen mode Exit fullscreen mode

执行上面的代码会像预期的那样工作，并产生以下输出:

```
------ INSERT VALID, PROPER DATE -------
--------------- GET DATA ---------------
31
"Magician "
"Raymond E. Feist "
681 
```

Enter fullscreen mode Exit fullscreen mode

您必须原谅输出中奇怪的字符串格式。由于`ExecuteQuery(...)`没有优化输出，只是将所有列值推入一个`object[]`数组，我们得到一些奇怪的格式。但是，外表并不重要。重要的是我们已经确认我们的`INSERT`工作正常，我们的`Book`已经被正确地添加到数据库中！

然而，让我们用`January 1st, 1750`的`DateTime`:
尝试另一个`Book`插入

```
Logging.LineSeparator("INSERT INVALID DATE");
ExecuteQuery(GetQueryStringFromBook(
    new Book("Silverthorn", "Raymond E. Feist", 432, new DateTime(1750, 1, 1))
)); 
```

Enter fullscreen mode Exit fullscreen mode

执行这些行会产生一个意外的`SqlException`(不要与`SqlTypeException`混淆):

```
--------- INSERT INVALID DATE ----------
[UNEXPECTED] System.Data.SqlClient.SqlException (0x80131904): The conversion of a varchar data type to a datetime data type resulted in an out-of-range value. 
```

Enter fullscreen mode Exit fullscreen mode

正如错误消息指出的，这里的问题是我们的代码试图将一个`varchar`转换成一个`datetime`类型，这个类型的**超出了预期值的范围**。换句话说，我们的`January 1st, 1750`的`PublicationDate`属性正在被转换成有效的格式(即`1/1/1750 12:00:00 AM`)。然而， [`DATETIME`](https://docs.microsoft.com/en-us/sql/t-sql/data-types/datetime-transact-sql) 的 SQL 列类型，也就是`dbo.Book.PublicationDate`列所设置的类型，只能接受`January 1, 1753, through December 31, 9999`的日期值。因为年份`1750`在这个时期之前，所以我们得到上面看到的`SqlException`。

这个问题有几种解决方案。第一个(也可能是最好的)选择是简单地**避免在 SQL 表中使用`DATETIME`列类型。** `DATETIME`有点过时，应该替换为 [`DATETIME2`](https://docs.microsoft.com/en-us/sql/t-sql/data-types/datetime2-transact-sql) SQL 列类型，其设计目的是既向后兼容所有以前的`DATETIME`值，同时也给出了更大的`January 1,1 CE through December 31, 9999 CE`日期范围。因此，如果我们的`dbo.Book.PublicationDate`列是一个`DATETIME2`类型，我们会很好。

但是，由于更改数据库列是危险的，并且对于现有的数据集来说并不总是可行的，所以在。NET 就是前面提到的 [`System.Data.SqlTypes`](https://docs.microsoft.com/en-us/dotnet/api/system.data.sqltypes?view=netframework-4.7.1) 类。这些特定于 SQL 的数据类型被明确设计为反映数据库中 SQL 列类型的功能。因此，如果你的。NET 类对象使用 [`SqlDateTime`](https://docs.microsoft.com/en-us/dotnet/api/system.data.sqltypes.sqldatetime?view=netframework-4.7.1) 而不是普通的`DateTime`类型，这将确保您不能在与您的 SQL 数据库不兼容的对象中创建日期。

为了说明这一点，我们在这里创建另一个`Book`并生成一个`INSERT`查询:

```
Logging.LineSeparator("INSERT INVALID, CONVERTED DATE");
ExecuteQuery(GetQueryStringFromBook(
    new Book("A Darkness At Sethanon", "Raymond E. Feist", 527, new DateTime(1750, 1, 1)), true)
); 
```

Enter fullscreen mode Exit fullscreen mode

传递给`GetQueryStringFromBook(...)`的第二个`true`参数将返回这个生成的查询字符串:

```
return "INSERT INTO dbo.Book (Title, Author, PageCount, PublicationDate) " +
        $"VALUES ('{book.Title}', '{book.Author}', '{book.PageCount}', '{new SqlDateTime(book.PublicationDate.Value)}');"; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这显式地创建了一个新的值为`book.PublicationDate.Value`的`SqlDateTime`实例。

执行这段代码会产生以下输出:

```
---- INSERT INVALID, CONVERTED DATE ----
[EXPECTED] System.Data.SqlTypes.SqlTypeException: SqlDateTime overflow. Must be between 1/1/1753 12:00:00 AM and 12/31/9999 11:59:59 PM. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们实际上得到的是一个`SqlTypeException`，而不是我们上面看到的`SqlException`。这个错误消息更加明确，通知我们`SqlDateTime`不能接受指定范围之外的日期值。如果我们要重构我们的`Book`类，我们可以将`PublicationDate`类型改为`SqlDateTime`，这将防止这个问题再次出现，因为我们不能使用无效的日期值。

充分利用您自己的应用程序，并全面管理所有应用程序。净异常，检查一下[空气制动。NET Bug Handler](https://airbrake.io/languages/net_bug_tracker?utm_source=dev_to&utm_medium=end-post&utm_campaign=airbrake-net) ，提供实时警报和即时洞察。NET 代码，以及对各种流行的开发集成的内置支持，包括:JIRA、GitHub、Bitbucket 等等。