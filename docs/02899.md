# 我最好的蟒蛇像 Ruby 的一样

> 原文：<https://dev.to/quanon/my-best-python-heredoc-like-rubys-one-2bc>

# Ruby(我想用 Python 这样做)

```
sayaka = '美樹 さやか'
kyoko = '佐倉 杏子'

str = <<~HEREDOC <div>
    <ul>
      <li>#{sayaka}</li>
      <li>#{kyoko}</li>
    </ul>
  </div> HEREDOC

puts(str) 
```

Enter fullscreen mode Exit fullscreen mode

```
<div>
  <ul>
    <li>美樹 さやか</li>
    <li>佐倉 杏子</li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

像上面那样，我想做的是:

*   忽略每行的前导空格
*   忽略第一个`'''`后的断行

# Python(怎么做的)

```
def heredoc(str):
  from textwrap import dedent

  return dedent(str).strip()

if __name__ == '__main__':
  sayaka = '美樹 さやか'
  kyoko = '佐倉 杏子'

  str = heredoc(f'''
    <div>
      <ul>
        <li>{sayaka}</li>
        <li>{kyoko}</li>
      </ul>
    </div>
  ''')

  print(str) 
```

Enter fullscreen mode Exit fullscreen mode

```
<div>
  <ul>
    <li>美樹 さやか</li>
    <li>佐倉 杏子</li>
  </ul>
</div> 
```

Enter fullscreen mode Exit fullscreen mode