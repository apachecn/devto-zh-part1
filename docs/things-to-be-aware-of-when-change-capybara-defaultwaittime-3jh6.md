# 更换水豚时需要注意的事项 default_wait_time

> 原文：<https://dev.to/tomoyukikashiro/things-to-be-aware-of-when-change-capybara-defaultwaittime-3jh6>

本帖最初发表于[更换水豚时需要注意的事项 default_wait_time](https://blog.tomoyukikashiro.me/post/things-to-be-aware-of-when-change-capybara_default_wait_time)

# 关于`Capybara.default_wait_time`

根据 [api 文件](http://www.rubydoc.info/github/jnicklas/capybara/Capybara/Node/Finders#find-instance_method) …

> find 将等待一段设定的时间，并不断重试查找元素，直到找到元素或时间到期。find 将等待的时间长度由 Capybara.default_wait_time 控制，默认为 2 秒。

## 注意

你最好不要给`Capybara_default_wait_time`设定太多时间。

例如，如果你设置 10 秒为`Capybara.default_wait_time`时，你想检查元素不存在。测试时间可能会很长。因为水豚等待检查的元素在每次测试中都不存在。

```
# in rspec.rb
Capybara.default_wait_time = 10

# in feature test
expect(page).to have_no_selector(:css, "p a#doesnotexist") # wait 10 seconds 
```

## 解

如果您需要超过 2 秒(Capybara.default_wait_time 中的默认值)来等待检查，您可以设置`wait`选项来查找。

```
expect(page).to have_selector(:css, "p a#doesnotexist", wait: 3) 
```