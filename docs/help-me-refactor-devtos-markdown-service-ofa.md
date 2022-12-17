# 帮我重构 dev.to 的 Markdown 服务！

> 原文：<https://dev.to/andy/help-me-refactor-devtos-markdown-service-ofa>

因此，我正在尝试重构我们的一个服务，在进一步清理之前检查降价。

下面是原文:

```
# instance method in article.rb
def fix_markdown
  fixed_body_markdown = MarkdownFixer.new(body_markdown).fix_all
  fixed_body_markdown.gsub!("\r\n", "\n")
  fixed_body_markdown.gsub!(/\ntags:.*\n/) do |tags|
    tags.split(" #").join(",").gsub("#", "").gsub(":,", ": ")
  end
  fixed_body_markdown
end

# markdown_fixer.rb service

class MarkdownFixer
  attr_accessor :markdown

  def initialize(markdown)
    @markdown = markdown
  end

  # This turns --- into ------- after the first two,
  # because --- messes with front matter
  def modify_hr_tags
    markdown.gsub(/^---/).with_index { |m, i| i > 1 ? "#{m}-----" : m }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

目标是将`article.rb`中的`fix_markdown`方法放到`MarkdownFixer.rb`中，并添加一个额外的步骤，在标题`add_quotes_to_title`周围添加引号。棘手的部分是我需要每个`.gsub`方法调用的返回值，我个人认为一遍又一遍地链接方法是不可读的。

```
class MarkdownFixer
  attr_accessor :markdown

  def initialize(markdown)
    @markdown = markdown
  end

  def fix_all
    # this part is my pain point
    markdown = add_quotes_to_title
    markdown = modify_hr_tags
    markdown = markdown.gsub!("\r\n", "\n")
    markdown.gsub!(/\ntags:.*\n/) do |tags|
      tags.split(" #").join(",").gsub("#", "").gsub(":,", ": ")
    end
  end

  def add_quotes_to_title
    quoted_beginning = markdown.gsub("title: ", "title: \"")
    quoted_beginning.gsub("\r\npublished: ", "\"\r\npublished: ")
  end

  # This turns --- into ------- after the first two,
  # because --- messes with front matter
  def modify_hr_tags
    markdown.gsub(/^---/).with_index { |m, i| i > 1 ? "#{m}-----" : m }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，我不太喜欢一遍又一遍地设置`markdown`，但是我不确定清理`fix_all`方法的更好的方法。很想听听你的想法。