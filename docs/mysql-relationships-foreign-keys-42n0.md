# MySQL 关系和外键

> 原文：<https://dev.to/adamkdean/mysql-relationships-foreign-keys-42n0>

只是另一段我会忘记的代码...

```
ALTER TABLE `accounts`
  ADD CONSTRAINT `FK_myKey` FOREIGN KEY (`customer_id`) 
  REFERENCES `customers` (`customer_id`) 
  ON DELETE CASCADE ON UPDATE CASCADE; 
```

Enter fullscreen mode Exit fullscreen mode