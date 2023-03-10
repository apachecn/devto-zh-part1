# 停止使用。htaccess 文件！不，真的。

> 原文：<https://dev.to/gaijinity/stop-using-htaccess-files-no-really-2pf>

# 停止使用。htaccess 文件！不，真的。

### 如果你用的是 Apache，那么世界上的每一个 CMS 都有一个. htac cess 文件。不要这样做！

安德鲁·韦尔奇

[![997321 Code](img/a2fe623729fbe2ffe4407eddcbb4efc1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--afUNuzW4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/997321-code.jpg)

停止使用`.htaccess`文件！这似乎是异端邪说。文件已经成为 web 开发世界的标准部分很长时间了，它就像一个老朋友。然而，是时候说再见了。

文件诞生于共享主机很普遍的时代:系统管理员需要一种方法，允许多个客户在不同的帐户下访问他们的服务器，为他们的网站提供不同的配置。`.htaccess`文件允许他们在不访问整个服务器的情况下修改 Apache 的工作方式。

虚拟专用服务器(VPS)已经让这成为过去。Excel 借给主持人的公司有[林颂](https://www.linode.com/)、[迪特勒海洋](https://www.digitalocean.com/)、 [Vul tr](https://www.vultr.com/) 等。为您提供完整的虚拟化服务器，它完全独立且自成一体。然而，像一个 appen 迪克斯，`.htaccess`持续存在。

事实上，像 Nginx(T2)这样的现代网络服务器甚至没有文件的概念，因为时代已经变了。

你说，这有什么关系？总之: **per for mance** 。不要相信我的话，下面是[阿帕奇基金会](https://httpd.apache.org/docs/current/howto/htaccess.html)(阿帕奇的创始人)要说的话:

<aside>You should avoid using .htac­cess files com­plete­ly if you have access to httpd main serv­er con­fig file. Using .htac­cess files slows down your Apache http serv­er. Any direc­tive that you can include in a .htac­cess file is bet­ter set in a Direc­to­ry block, as it will have the same effect with bet­ter performance.</aside>

备受关注的 HTML5 锅炉板也对此提出了警告:

<aside>(!) Using ‘.htac­cess‘ files slows down Apache, there­fore, if you have access to the main serv­er con­fig­u­ra­tion file (which is usu­al­ly called ​‘httpd.conf‘), you should add this log­ic there.</aside>

他们甚至有一整节的时间留给了 T4 何时(不)使用。htac cess 文件。简而言之，如果您使用的是`.htaccess`文件，那么每个请求 web 服务器处理的请求——即使是最低级的`.png`或`.css`文件——都会导致 Apache:

*   在当前请求的目录中查找一个`.htaccess`文件
*   然后在每个目录中查找一个`.htaccess`文件，从那里一直到服务器根目录
*   把所有这些文件放在一起
*   使用新设置重新配置 web 服务器
*   最后，把文件交出来

每一个。单身。请求。而且每个网页都会收到几十个请求。这是你不需要的，更重要的是，这是完全不必要的，除非你是在一个老式的共享主机环境中(如果你是，停止阅读这篇文章，立即切换到一个现代的 VPS)。

添加到`.htaccess`文件中的东西越多，问题就越严重。通常，它是重写规则之类的事情的转储场，并且您的`.htaccess`文件越复杂，Apache 必须为每个请求解析它时增加的开销就越多。

*Tan gent:* 对于 301 热地 rect，一个更好的解决方案是，只有在发生 404 excep tion 之后，让 CMS 系统处理热地 rect，然后再让[重新接入 Craft CMS。比较而言，对于大多数正常请求，最好让 web 服务器为 mant 服务，而不是只接受热地的直接请求。当然，也有例外:热地矩形应该总是留在服务器端，因为它发生的频率很高。](https://github.com/nystudio107/retour)

以下是 Craft CMS 附带的默认`.htaccess`文件:

```
 <IfModule mod_rewrite.c>
    RewriteEngine On

    # Send would-be 404 requests to Craft
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_URI} !^/(favicon\.ico|apple-touch-icon.*\.png)$ [NC]
    RewriteRule (.+) index.php?p=$1 [QSA,L]
</IfModule> 
```

Enter fullscreen mode Exit fullscreen mode

看起来很简单。它只是做一些风扇 cy 的东西，以确保您可以有 pret ty 期待网址没有`index.php`在其中，并确保 404 错误是通过工艺路由。这是你放入一个`.htaccess`文件中的 CMS 系统的典型例子。

对于 web 服务器上的每个域，都有一个虚拟主机文件，告诉 web 服务器如何为该域提供网页服务。我们所要做的就是将我们以前放在`.htaccess`文件中的内容转移到虚拟主机文件中，这样我们就完成了。真的很简单。

从你的`http` - > `https`重写规则到过期标题，gzip 压缩设置，以及其他性能增强设置的每一件事情都将工作。

<aside>If it worked in .htac­cess, it will work in your vir­tu­al host .conf file as well. Both are just ways to con­fig­ure your web­serv­er, but doing it once when the web­serv­er starts up vs. every request is more performant.</aside>

## 完事了

既然你实际上已经读到这里，我假设你已经同意不使用`.htaccess`文件了。但是我们怎么做呢？简单。我们只需要获取`.htaccess`文件中的目录，并将它们放入我们的虚拟主机`.conf`文件中。这些文件通常位于大多数 Lin ux 发行版的`/etc/httpd/conf.d`或`/etc/httpd/sites-available`中。

还有一件小事我们也必须做。即使我们的服务器上甚至没有一个`.htaccess`文件，如果`AllowOverride`指令告诉 Apache 查找它们，Apache 仍然会查找它们。所以我们只是在我们的虚拟主机文件中设置了`AllowOverride none`，这告诉 Apache 它不再需要寻找`.htaccess`文件。

这是一个完整的虚拟主机文件看起来像什么。其他 CMS 系统应该看起来非常相似:

```
 <VirtualHost *:80>
  ServerName BradsForMen.com
  ServerAlias *.BradsForMen.com
  DocumentRoot /var/www/BradsForMen/public
  CustomLog /var/log/httpd/BradsForMen.com-access.log combined
  ErrorLog /var/log/httpd/BradsForMen.com-error.log

## Canonical domain rewrite ##
  <If "%{HTTP_HOST} != 'BradsForMen.com'">
    Redirect "/" "http://BradsForMen.com/"
  </If>

  <Directory /var/www/BradsForMen>
      Options FollowSymLinks
      AllowOverride None

## Removes index.php from Craft URLs ##
      <IfModule mod_rewrite.c>
        RewriteEngine On
        # Send would-be 404 requests to Craft
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteCond %{REQUEST_URI} !^/(favicon\.ico|apple-touch-icon.*\.png)$ [NC]
        RewriteRule ^(.+) /index.php?p=$1 [QSA,L]
      </IfModule>
  </Directory>
</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们将正则表达式从`RewriteRule (.+) index.php?p=$1 [QSA,L]`修改为`RewriteRule ^(.+) /index.php?p=$1 [QSA,L]`

就是这样。保存它，用`sudo apachectl restart`重启 Apache，然后就可以走了！

你可能会问这有多大的不同。事实上，这并不是一个巨大的收获。你可以期待在[单挖其](http://www.fubra.com/blog/2008/01/07/htaccess-vs-httpdconf/)的收益，这将成为一个更大的影响作为[交通增加](http://www.eschrade.com/page/why-you-should-not-use-htaccess-allowoverride-all-in-production/)。然而，考虑到使用`.htaccess`文件是多么的不必要，没有理由不实施避免使用它的最佳实践。

即使看似很小的性能收益，在一起实施时也会累积起来。

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计