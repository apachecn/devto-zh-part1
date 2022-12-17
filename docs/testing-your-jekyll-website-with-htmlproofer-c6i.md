# 用 HTMLProofer 测试你的 Jekyll 网站

> 原文：<https://dev.to/phansch/testing-your-jekyll-website-with-htmlproofer-c6i>

当我为我的 Jekyll 网站添加[水豚测试](http://phansch.net/2017/03/13/testing-jekyll-websites/)时，我也偶然发现了 [HTMLProofer](https://github.com/gjtorikian/html-proofer) 。

HTMLProofer 允许测试您的 HTML 输出。它可以检查图像的 alt 标签，如果链接正在工作，以及其他一些事情。

我决定在我的网站上试一试。最初，我对输出的数量感到惊讶，并认为这太多了，无法一次修复。所以首先，我只让它检查断开的链接。它发现 200 个出站链接中有 30 个不起作用。

他们中的大多数是我的黑客系列的一部分，我有时会链接到一些显然直到今天才成功的本地创业公司。可惜[链接腐烂](https://en.wikipedia.org/wiki/Link_rot)是个东西。

在我处理所有断开的链接之前，我开始通过添加一个定制的 Rake 任务将 HTMLProofer 集成到测试套件中:

```
task :html_proofer do
  build_dir = File.join(File.dirname( __FILE__ ), '_site')
  unless File.directory?('test/_site')
    `jekyll build -d #{build_dir} -V`
  end
  opts = {
    url_ignore: [/localhost/],
    empty_alt_ignore: true,
    file_ignore: [/slides/],
    typhoeus: {
      ssl_verifyhost: 0,
      ssl_verifypeer: false,
      timeout: 30
    }
  }
  HTMLProofer.check_directory(build_dir, opts).run
end 
```

Enter fullscreen mode Exit fullscreen mode

它使用`rake html_proofer`构建站点，并在 Jekyll 输出中使用给定的选项运行 HTMLProofer。可以查看 [script/ci.rb](https://github.com/phansch/phansch.github.com/blob/master/script/ci.rb) 和 [.travis.yml](https://github.com/phansch/phansch.github.com/blob/master/.travis.yml) 中的 Travis CI 集成。

如果你在使用 HTMLProofer 时遇到了 SSL 问题，你可能需要[在 Travis 上安装](https://github.com/typhoeus/typhoeus/issues/568) `libcurl4-openssl-dev`。

我做的最后一件事是修复链接，因为这是任务中最不引人注目的部分。链路断开的原因有很多，几乎每个原因都可以有不同的处理方式。

*   一篇丢失的文章可能是由一个新的 URL 结构和忘记的重定向引起的，所以我在这些网站上四处寻找，如果可能的话，试图找到正确的链接。

*   坏域名在大多数时候都是一个失败的原因，尽管一些初创公司已经更名或被收购，所以在那里使用新域名是有意义的。

*   域名可能只是暂时无法访问，所以我不想删除链接，而是将其列入白名单。

这花了一些时间，但它得到了回报，现在我可以肯定，这个网站上的任何 HTML 都没有断开链接。

正如开始提到的，HTMLProofer 有几个更好的特性，但是我还没有尝试它们。在下一篇文章中，我可能会看看其他功能。