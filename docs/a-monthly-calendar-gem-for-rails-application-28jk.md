# 用于 Rails 应用程序的月历 gem

> 原文：<https://dev.to/zenizh/a-monthly-calendar-gem-for-rails-application-28jk>

Carender 是 Ruby on Rails 应用程序的月历宝石。

## 用法

这个 gem 通过获取视图上下文中的`params[:year]`和`params[:month]`自动将月历呈现为 HTML 表格。
下面的代码呈现了一个简单的月历:

```
<%= carender do |date| %>
  <%= date.day %>
<% end %> 
```

作为块传递的内容呈现在每个单元格中。例如，上面的代码呈现如下的 HTML 表格。

```
<table>
  <tr><th>Sun</th><th>Mon</th><th>Tue</th><th>Wed</th><th>Thu</th><th>Fri</th><th>Sat</th></tr>
  <tr><td></td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td></tr>
  <tr><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td></tr>
  ...
</table> 
```

### 收藏

要呈现包含按指定列分组的集合的日历，您可以传递如下的`:collection`和`:column`参数:

```
<%= carender collection: @posts, column: :posted_on do |date, posts| %>
  <% posts.each do |post| %>
    <%= link_to post.title, post %>
  <% end %>
<% end %> 
```

## 安装

将这一行添加到应用程序的 Gemfile:

```
gem 'carender' 
```

然后执行:

```
$ bundle 
```