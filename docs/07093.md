# 添加钴的读数时间

> 原文：<https://dev.to/booyaa/add-reading-time-in-cobalt-6gm>

我想给我的每篇博文添加一个大概的阅读时间，就像在 [medium](https://medium.com) 帖子中看到的那样。有很多非常好的 javascript 库，但是我认为这违背了使用静态站点生成器的全部目的
。

我也在找借口使用液体(钴的模板渲染引擎)。

这是一个相当简单的方法，计算字数，然后除以 200，这是每分钟阅读字数的平均值。

```
{%  assign  reading_wpm  =  200  %}
{%  assign  word_count  =  page.content  |  split:  " "  |  size  %}
{%  assign  reading_time  =  word_count  |  divided_by:  200  %}
{%  case  reading_time  %}
  {%  when  0  %}
    {%  assign  phrase  =  "less than a minute."  %}
  {%  when  1  %}
    {%  assign  phrase  =  "about a minute."  %}
  {%  else  %}
    {%  assign  phrase  =  " minutes."  |  prepend:  reading_time  %}
{%  endcase  %}
Reading time: {{  phrase  }} 
```

Enter fullscreen mode Exit fullscreen mode