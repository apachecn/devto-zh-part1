# 开始 web 抓取的公共 Lisp

> 原文：<https://dev.to/taroyanaka/begginnig-common-lisp-for-web-scraping-22f>

嘿，书呆子，你喜欢网络吗？兴趣口齿不清？
没有？我不在乎
好吧，我在乎

第一步:安装 sbcl
$ brew 更新
$ brew 安装 sbcl
(现在 clisp 停止更新)

第二步:安装 quick lisp
$ curl-O[http://beta.quicklisp.org/quicklisp.lisp](http://beta.quicklisp.org/quicklisp.lisp)T3】$ sbcl-load quick lisp . lisp

- ↓↓启动 sbcl 控制台↓↓↓ -

(quick lisp-quick start:install)
(QL:add-to-init-file)

第三步:通过 quick lisp
(QL:quick load:drakma)
(QL:quick load:plump)
(QL:quick load:clss)将库安装到 sbcl

第四步:喝咖啡

第五步:编写代码并 eval
(defvar*html*(drakma:http-request "[http://www.paulgraham.com/hp.html")](http://www.paulgraham.com/hp.html%22)))
(defvar*parse-html*(丰满:parse*html*)
(丰满:text(car(coefit(clss:select " title "*parse-html*)‘list)))