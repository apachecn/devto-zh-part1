# 如何用 Ruby on Rails 获得 innodb_lock_wait_timeout

> 原文：<https://dev.to/toyotarone/how-to-get-innodblockwaittimeout-with-ruby-on-rails-28bl>

【会话】
`ActiveRecord::Base.connection.select_all(“SELECT @@SESSION.innodb_lock_wait_timeout”).to_hash`

【全局】
`ActiveRecord::Base.connection.select_all(“SELECT @@GLOBAL.innodb_lock_wait_timeout”).to_hash`