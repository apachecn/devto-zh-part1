# 谷歌电子表格和 Ruby

> 原文：<https://dev.to/twilio/google-spreadsheets-and-ruby>

有时候你不需要数据库。也许你正在构建一个原型或者一个简单的内部应用程序。使用谷歌电子表格可以更快更容易地设置和开始。

在本教程中，我们将使用 [Hiroshi Ichikawa 的](http://gimite.net/en/) [`google_drive` Ruby gem](https://github.com/gimite/google-drive-ruby) 来读取、写入和删除谷歌电子表格中的数据，只需几行代码。

### 访问您的电子表格

首先，你需要一个可以读写的电子表格。如果你没有自己的电子表格，为美国立法者制作一份[联系信息电子表格](https://docs.google.com/spreadsheets/d/1KK432FFgQ18lP-juSq10ae1ZX1lfa7FTELQFY9bDC38/edit#gid=152787366)。(如果你想让[像伊恩·韦伯斯特](https://www.twilio.com/blog/2016/06/call-congress-using-one-phone-number.html)一样给国会打电话变得容易，这是一组有用的数据。)

[![You can make a copy of a spreadsheet by selecting the file menu then choosing 'Make a copy...'](img/d98cd6bf5c38e4314bb2b7525d34a684.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--iwdTrmxo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2017/02/Screen-Shot-2017-02-16-at-11.48.00-AM-300x247.png)

创建一个目录来构建这个项目。

```
$ mkdir spreadsheet_fun
$ cd spreadsheet_fun 
```

您还需要获得 OAuth2 凭证来从 Ruby 访问电子表格。您可以通过以下步骤创建一个服务帐户来完成此操作:

1.  转到[谷歌 API 控制台](https://console.developers.google.com/)。
2.  创建新项目。
3.  单击启用 API。搜索并启用 Google Drive API。
4.  为 Web 服务器创建访问应用程序数据的凭据。
5.  命名服务帐户，并授予它 Editor 项目角色。
6.  下载 JSON 文件。
7.  将 JSON 文件复制到您的`spreadsheet_fun`目录，并将其重命名为 client_secret.json

[![](img/625cb4911cf817fadec46c8349479e28.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--02uemAAI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2017/02/google-developer-console.gif)

这些是您的应用程序将需要的凭据。他们代表可以代表您更新电子表格的用户。尽管如此，我们仍然需要让这个用户访问我们想要使用的电子表格。打开`client_secret.json`，找到并复制`client_email`。在您的电子表格中，点击右上角的“共享”按钮并粘贴电子邮件，赋予您的服务帐户编辑权限。

这就是你授权访问你的电子表格所需要做的一切。让我们继续阅读 Ruby 中的数据。

### 用 Ruby 从谷歌电子表格中读取数据

在你的`spreadsheet_fun`目录中创建一个`Gemfile`，并将以下内容粘贴到其中:

```
source "https://rubygems.org"

gem "google_drive" 
```

在目录中，运行`bundle install`。现在我们准备开始与电子表格交互。创建一个名为`spreadsheet.rb`的新文件，并复制以下代码:

```
require 'bundler'
Bundler.require

# Authenticate a session with your Service Account
session = GoogleDrive::Session.from_service_account_key("client_secret.json")

# Get the spreadsheet by its title
spreadsheet = session.spreadsheet_by_title("Copy of Legislators 2017")
# Get the first worksheet
worksheet = spreadsheet.worksheets.first
# Print out the first 6 columns of each row
worksheet.rows.each { |row| puts row.first(6).join(" | ") } 
```

用`bundle exec ruby spreadsheet.rb`运行这个，你会看到电子表格中的数据。

[![A terminal window showing the results of running the above script. The first six columns of each row are printed.](img/1f36ae934511a6d384f18cccc1955fc0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--v2TH9bzy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://twilioinc.wpengine.com/wp-content/uploads/2017/03/avtYQbIgC2F7TeRjE5_kyExulALckAcirCSp_dcNDoG2IX6hVHz520gft83vBxJGL3S_F35r5lWAv6_2ckV5vUt8yQ9NrLV1J5-QOn-hJtXcDWO2ifzwtc19dPYDAYRGBvMUNTc.png)

现在我们已经从中读取了数据，让我们看看如何编辑电子表格。

### 用 Ruby 从电子表格中插入、更新和删除

现在我们有了对工作表的引用，插入一行数据很简单。`insert_rows`方法在您提供的行号上方插入新行。这将在第一行数据的上方插入一行。

```
worksheet.insert_rows(2, [["Hello!", "This", "was", "inserted", "via", "Ruby"]])
worksheet.save 
```

您总是需要保存工作表，以便将更改保存回电子表格。

如果您想在所有现有行的底部插入一行，使用`num_rows`方法。

```
worksheet.insert_rows(worksheet.num_rows + 1, [["Hello!", "This", "was", "inserted", "at", "the", "bottom"]])
worksheet.save 
```

要更新单元格，您可以通过行号和列号引用单元格:

```
worksheet[2, 1] = "Updated!"
worksheet.save 
```

您也可以使用单元格名称:

```
worksheet["A2"] = "Updated again!"
worksheet.save 
```

删除行和插入行一样，你需要提供行号和你想删除多少行:

```
worksheet.delete_rows(2, 1)
worksheet.save 
```

### 创建、读取、更新、删除、完成

现在您已经看到了如何使用`google_drive` gem 访问和更改 Google 电子表格中的数据。有关可用方法的更多信息，请查看 gem 的 [`Worksheet`类文档](http://www.rubydoc.info/gems/google_drive/2.1.2/GoogleDrive/Worksheet)。

下次你创建一个需要一些存储空间或者只需要加载和处理一些数据的 Sinatra 应用程序时，Google 电子表格可能是一个不错的选择。别忘了，对于非技术用户来说，这也是一个查看数据的好界面。这里有一个如何用谷歌电子表格和 Sinatra 建立登陆页面的例子。

如果你用 Google 电子表格和 Ruby 构建了什么有趣的东西，请告诉我。你可以在 philnash@twilio.com 的 [联系我，或者在推特上打](mailto:philnash@twilio.com)[@菲尔纳什](https://twitter.com/philnash)联系我。

* * *

*[谷歌电子表格和 Ruby](https://www.twilio.com/blog/2017/03/google-spreadsheets-ruby.html) 最初发表于 2017 年 3 月 14 日 [Twilio 博客](https://www.twilio.com/blog/)。*