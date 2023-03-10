# 用 Scrapy 简单地爬行

> 原文：<https://dev.to/georgeoffley/crawling-simply-with-scrapy>

*这篇文章最初出现在[我的博客](https://www.georgeoffley.com/new_dev/2017/04/22/crawling-simply-scrapy/)T3 上*

我最近一直在构建一个项目，使用 Python 和网络工具。我自制的爬虫遇到了一连串的问题。从进入我们的页面，到解析出链接，我不断遇到问题。幸运的是，对于我和我们作为软件开发人员和有抱负的开发人员试图解决的许多其他问题来说，有一个这样的库。

我选择放弃使用家庭酿造的解决方案，这种方案对一些经过检验的真实的东西不起作用。类似于[刺儿头](https://scrapy.org/)的东西。这在互联网上随处可见，被很多公司和其他项目使用。这总是一个伟大图书馆的标志，它是开源的，周围有一个伟大的社区。谷歌一下，你会看到许多关于 Scrapy 的 [Stackoverflow](https://stackoverflow.com/search?q=scrapy) 问题和答案(包括我的)。

目标很简单，获取一个输入并通过一个爬虫脚本运行它。如果是完整的爬虫，我选择使用单独的爬行脚本。对于我的情况来说，处理起来要容易得多，因为它将被集成到一个更大的工具集中。下面你可以看到我的代码

```
from scrapy.contrib.spiders import CrawlSpider, Rule
from scrapy.contrib.linkextractors.sgml import SgmlLinkExtractor
from scrapy.selector import HtmlXPathSelector
from scrapy.item import Item
from scrapy.spiders import BaseSpider
from scrapy import Request
from scrapy.http import Request
from scrapy.utils.httpobj import urlparse

class InputSpider(CrawlSpider):
        name = "Input"

        def __init__(self, url=None, *args, **kwargs):
            super(InputSpider, self).__init__(*args, **kwargs)
            self.allowed_domains = [url]
            self.start_urls = ["http://" + url]

        rules = [
        Rule(SgmlLinkExtractor(allow=()), follow=True, callback='parse_item')
        ]

        def parse_item(self, response):
            x = HtmlXPathSelector(response)
            filename = "output.txt"
            open(filename, 'ab').write(response.url + "\n")

print "Crawling site..."
print "See output.txt for links" 
```

Enter fullscreen mode Exit fullscreen mode

我使用以下命令运行蜘蛛:

```
scrapy runspider -a url=quotes.toscrape.com crawler.py 
```

Enter fullscreen mode Exit fullscreen mode

爬行非常简单，使用 CrawlSpider 参数创建爬行类。我想设置它，让用户可以把他们想要抓取的网址。为此，我们必须创建自己的 **init** 函数，并设置 args 和 kwargs 来查找我们从终端运行脚本时放入的 url 参数。在这里，我们将 start_urls 设置为 url 参数。对于那些从未使用过 Scrapy 的人来说，start_urls 是蜘蛛开始爬行的地方。您可能还注意到，我们将 allowed_domains 设置为相同的值。我们这样做是为了使唯一被抓取的 URL 是来自指定域的 URL

我们为我们的爬虫设置规则，以便我们可以提取我们的 URL 并把它们放在某个地方。这里我使用的是 SgmlLinkExtractor。这是值得注意的，因为这在 Scrapy 的当前版本中是被贬低的。我用它是因为我有点了解它，它能满足我的需要。将来我会把这个切换到[lxmlinkextractor](https://doc.scrapy.org/en/latest/topics/link-extractors.html)中。它被淘汰的原因是 Sgml 使用了 [sgmllib。SGMLParser](https://docs.python.org/2/library/sgmllib.html) 来完成它的工作。这个库在 Python 2.6 中已经过时，并且与 Python 3 不兼容。因此，如果你想了解这个例子，请查看 LxmlLinkExtractor。

也就是说这个规则对我们来说确实很酷。如果你注意到其中一个参数是 follow=True，那是用来跟随所有我们抓取到下一页的链接，并抓取那些页面上的链接。使浏览整个网站变得容易。我们设置了 callback='parse_item ',它使用底部的解析函数从响应中提取链接，并将它们记录在输出文件中。

就是这样，非常简单。我只是想要一些简单的东西，可以集成到我的工具集中。我们用了不到 30 行就完成了这项工作。我建议你通读一下 [Scrapy docs](https://docs.scrapy.org/en/latest/) ，因为它们很有用。感谢阅读！