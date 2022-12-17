# 在 GAE 使用 ndb.tasklet

> 原文：<https://dev.to/arvelt/use-ndbtasklet-in-gae-89m>

```
from time import sleep
from google.appengine.ext import ndb

@ndb.tasklet
def do_some(time):
  yield ndb.sleep(time)
  raise ndb.Return(time)

@ndb.tasklet
def foo():
  a, b = yield (do_some(3), do_some(3))
  raise ndb.Return(a + b)

def main():
  f = foo()
  x = f.get_result()
  print x

main() #Execution time is 3 not 6 
```

Enter fullscreen mode Exit fullscreen mode