# 用 Ruby on Rails 处理 Excel 文件

> 原文：<https://dev.to/ikeryo1182/handling-excel-file-with-ruby-on-rails-2i4>

大家好！

## Excel 与 Ruby on Rails 的情况

当你处理 Excel 文件时，有两个操作。

1.  读取 Excel 文件
2.  编写 Excel 文件

今天，在 Ruby on Rails 中有几个处理 Excel 文件的 gem，例如 roo，axlsx，rubyXL。但是，这并不总是意味着它们都有用。
也就是说上面的一些宝石并不是所有操作都配备的。

比如‘ax lsx’gem 不能读 Excel 文件，‘roo’gem 不能写。因此，我在寻找具有所有操作和用途的宝石。然后我发现了' rubyXL '宝石，它非常有用，比其他宝石更容易使用。下面，我将介绍使用 rubyXL 的标准方法。

## rubyXL 实现了什么

在你有格式化文件的情况下，我实现如下。

1.  读取 Excel 文件
2.  编写 Excel 文件
3.  导出 Excel 文件

## 准备实施

### 安装 rubyXL

将以下代码添加到 Gemfile 中。

```
gem 'rubyXL' 
```

Enter fullscreen mode Exit fullscreen mode

然后你应该进行“捆绑安装”

```
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

## 如何用 rubyXL 实现

### 读取 Excel 文件

```
workbook = RubyXL::Parser.parse('ExcelFilePath') 
```

Enter fullscreen mode Exit fullscreen mode

您应该用要读取文件的路径替换“ExcelFilePath”。

### 采集工作表

```
worksheet = workbook['WorkSheetName'] 
```

Enter fullscreen mode Exit fullscreen mode

如下所示，您可以通过索引指定工作表。

```
worksheet = workbook[０]
worksheet = workbook.first 
```

Enter fullscreen mode Exit fullscreen mode

### 获取单元格值

```
cell_value = worksheet[y][x].value 
```

Enter fullscreen mode Exit fullscreen mode

你应该注意 x 和 y 的位置。

### 添加单元格

```
worksheet.add_cell(y, x ,value) 
```

Enter fullscreen mode Exit fullscreen mode

同样，您应该注意“x”和“y”的位置。

```
cell_value = worksheet.add_cell(y, x ,value) 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，你也可以获得附加细胞的价值。

### 写 Excel 文件

```
workbook.write('ExcelFilePath') 
```

Enter fullscreen mode Exit fullscreen mode

您应该将“ExcelFilePath”替换为您想要放置写入文件的路径。

### 导出 Excel 文件

```
send_data(
      workbook.stream.read,　
      :disposition => 'attachment',
      :type => 'application/excel',
      :filename => "FileName.xlsx"
    ) 
```

Enter fullscreen mode Exit fullscreen mode

如果需要二进制数据，可以从下面的。

```
workbook.stream.read 
```

Enter fullscreen mode Exit fullscreen mode

## 用于复制&粘贴

```
#Read Excel File
workbook = RubyXL::Parser.parse('ExcelFilePath')

#Acquire WorkSheet
worksheet = workbook['SheetName']

#Acquire Cell Value
cell_value = worksheet[y][x].value

#Write Excel File
workbook.write('ExcelFilePath')

#Export File
send_data(
      workbook.stream.read,　
      :disposition => 'attachment',
      :type => 'application/excel',
      :filename => "FileName.xlsx"
    ) 
```

Enter fullscreen mode Exit fullscreen mode

## 感谢阅读

这次，我展示了在有格式化文件的情况下 rubyXL 的实现。如果你想改变单元格的宽度和文本的颜色，你应该阅读 rubyXL 的文档。

非常感谢。祝你有一个美好的红宝石人生！