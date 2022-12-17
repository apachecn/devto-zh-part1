# 当在 GAE 中使用祖先查询时，您不必使用已经存在实体。

> 原文：<https://dev.to/arvelt/when-use-ancestor-query-in-gae-you-dont-have-to-be-use-a-existed-entity-2kb>

但是最好使用一个已存在的实体。

```
from google.appengine.ext import ndb

class Customer(ndb.Model):
    name = ndb.StringProperty()

class Purchase(ndb.Model):
   price = ndb.IntegerProperty()

customer_entity = Customer(name='me', id='aaa@example.com')
purchase1 = Purchase(parent=customer_entity.key, price=100)
purchase2 = Purchase(parent=customer_entity.key, price=200)
purchase1.put()
purchase2.put()
print Purchase.query(ancestor=customer_entity.key).fetch()
#[Purchase(key=Key('Customer', 'aaa@example.com', 'Purchase', 5414888608366592), price=200), Purchase(key=Key('Customer', 'aaa@example.com', 'Purchase', 5977838561787904), price=100)]

key = ndb.Key('user', 'bbb@example.com')
purchase3 = Purchase(parent=key, price=100)
purchase4 = Purchase(parent=key, price=200)
purchase3.put()
purchase4.put()
print Purchase.query(ancestor=key).fetch()
#[Purchase(key=Key('user', 'bbb@example.com', 'Purchase', 4711201166589952), price=200), Purchase(key=Key('user', 'bbb@example.com', 'Purchase', 6540788515209216), price=100)] 
```

Enter fullscreen mode Exit fullscreen mode