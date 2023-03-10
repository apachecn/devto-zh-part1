# 构建一个简单的 python 网络爬虫

> 原文：<https://dev.to/pranay749254/build-a-simple-python-web-crawler>

## 什么是网络爬虫？

网络爬虫是一个互联网机器人，用于万维网的网络索引。所有类型的搜索引擎都使用网络爬虫来提供高效的结果。实际上，它从其他网站收集所有或一些特定的超链接和 HTML 内容，并以适当的方式预览它们。当有大量链接需要爬行时，即使最大的爬虫也会失败。由于这个原因，2000 年初的搜索引擎在提供相关结果方面表现不佳，但现在这一过程已经有了很大的改进，可以在瞬间给出正确的结果

## Python 网络爬虫

这里的网络爬虫是用 python3 创建的。Python 是一种高级编程语言，包括面向对象、命令式、函数式编程和大型标准库。
对于网络爬虫，使用了两个标准库- `requests`和`BeautfulSoup4`。`requests`提供了一种连接万维网的简单方法，`BeautifulSoup4`用于一些特殊的字符串操作。

## 示例代码

```
import requests
from bs4 import BeautifulSoup
def web(page,WebUrl):
    if(page>0):
        url = WebUrl
        code = requests.get(url)
        plain = code.text
        s = BeautifulSoup(plain, "html.parser")
        for link in s.findAll('a', {'class':'s-access-detail-page'}):
            tet = link.get('title')
            print(tet)
            tet_2 = link.get('href')
            print(tet_2)
web(1,'http://www.amazon.in/s/ref=s9_acss_bw_cts_VodooFS_T4_w?rh=i%3Aelectronics%2Cn%3A976419031%2Cn%3A%21976420031%2Cn%3A1389401031%2Cn%3A1389432031%2Cn%3A1805560031%2Cp_98%3A10440597031%2Cp_36%3A1500000-99999999&bbn=1805560031&rw_html_to_wsrp=1&pf_rd_m=A1K21FY43GMZF8&pf_rd_s=merchandised-search-3&pf_rd_r=2EKZMFFDEXJ5HE8RVV6E&pf_rd_t=101&pf_rd_p=c92c2f88-469b-4b56-936e-0e65f92eebac&pf_rd_i=1389432031') 
```

Enter fullscreen mode Exit fullscreen mode

### 输出:

```
C:\Python34\python.exe C:/Users/Babuya/PycharmProjects/Youtube/web_cr.py
Apple iPhone 6 (Gold, 32GB)
http://www.amazon.in/Apple-iPhone-6-Gold-32GB/dp/B0725RBY9V
OnePlus 5 (Slate Gray 6GB RAM + 64GB memory)
http://www.amazon.in/OnePlus-Slate-Gray-64GB-memory/dp/B01NAKTR2H
OnePlus 5 (Midnight Black 8GB RAM + 128GB memory)
http://www.amazon.in/OnePlus-Midnight-Black-128GB-memory/dp/B01MXZW51M
Apple iPhone 6 (Space Grey, 32GB)
http://www.amazon.in/Apple-iPhone-Space-Grey-32GB/dp/B01NCN4ICO
OnePlus 5 (Soft Gold, 6GB RAM + 64GB memory)
http://www.amazon.in/OnePlus-Soft-Gold-64GB-memory/dp/B01N1TYZR2
Mi Max 2 (Black, 64 GB)
http://www.amazon.in/Mi-Max-Black-64-GB/dp/B073VLGL5Y
Moto G5 Plus (32GB, Fine Gold)
http://www.amazon.in/Moto-Plus-32GB-Fine-Gold/dp/B071ZZ8N5Y
Apple iPhone SE (Space Grey, 32GB)
http://www.amazon.in/Apple-iPhone-SE-Space-Grey/dp/B071DF166C
Honor 8 Pro (Blue, 6GB RAM + 128GB Memory)
http://www.amazon.in/Honor-Pro-Blue-128GB-Memory/dp/B01N4FMUFH
Apple iPhone 7 (Black, 32GB)
http://www.amazon.in/Apple-iPhone-7-Black-32GB/dp/B01LZKSVRB
BlackBerry KEYone (LIMITED EDITION BLACK)
http://www.amazon.in/BlackBerry-KEYone-LIMITED-EDITION-BLACK/dp/B073ZLLVQ9
Apple iPhone SE (Gold, 32GB)
http://www.amazon.in/Apple-iPhone-SE-Gold-32GB/dp/B071RC52N6
Apple iPhone SE (Rose Gold, 32GB)
http://www.amazon.in/Apple-iPhone-SE-Rose-Gold/dp/B06ZXWWD6R
Apple iPhone 6s (Space Grey, 32GB)
http://www.amazon.in/Apple-iPhone-Space-Grey-32GB/dp/B01LX3A7CC
Samsung Galaxy J7 Max (Gold, 32GB)
http://www.amazon.in/Samsung-Galaxy-J7-Max-Gold/dp/B073PWKTRS
Honor 8 Pro (Black, 6GB RAM + 128GB Memory)
http://www.amazon.in/Honor-Pro-Black-128GB-Memory/dp/B01MQXNY1L
Samsung Galaxy J7 Max (Black, 32GB)
http://www.amazon.in/Samsung-Galaxy-J7-Max-Black/dp/B073PWDMHD
OnePlus 3T (Soft Gold, 6GB RAM + 64GB memory)
http://www.amazon.in/OnePlus-3T-Soft-Gold-memory/dp/B01FM7J3NA
Apple iPhone 6s (Gold, 32GB)
http://www.amazon.in/Apple-iPhone-6s-Gold-32GB/dp/B01M0CJNVL
Apple iPhone 6s (Rose Gold, 32GB)
http://www.amazon.in/Apple-iPhone-Rose-Gold-32GB/dp/B01LXF3SP9
Samsung Galaxy C7 Pro (Navy Blue, 64GB)
http://www.amazon.in/Samsung-Galaxy-Navy-Blue-64GB/dp/B01LXMHNMQ
Samsung J7 Prime 32GB ( Gold ) 4G VoLTE
http://www.amazon.in/Samsung-J7-Prime-32GB-VoLTE/dp/B06Y3HFZBQ
Vivo V5s (Matte Black) with Offers
http://www.amazon.in/Vivo-V5s-Matte-Black-Offers/dp/B071P2FNF2
Vivo V5s (Crown Gold) with Offers
http://www.amazon.in/Vivo-V5s-Crown-Gold-Offers/dp/B071VT6RG2 
```

Enter fullscreen mode Exit fullscreen mode

在这里，这个爬虫从 amazon.in 的一个页面收集所有产品标题和产品页面各自的链接。用户只需要指定什么样的数据或链接被抓取。虽然网络爬虫的主要用途是在搜索引擎中，但是这种方式也可以用来收集一些有用的信息。
在这里，页面的所有 HTML 都是使用`requests`以纯文本形式获取的。然后转换成一个`BeautifulSoup`对象。从这个对象中，所有标题和 href 以及类`s-access-detail-page`都被访问。这就是这个基本的网络爬虫的工作原理。