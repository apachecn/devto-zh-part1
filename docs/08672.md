# 特拉维斯·CI 和杰基尔

> 原文：<https://dev.to/the_128keaton/travis-ci-and-jekyll>

## 前言:

当我第一次开始用 Jekyll 写博客时，我的设置是一个废墟。我不太理解液体模板，所以没有太多的重用，大量不必要的 CSS 和大量普通的 HTML 组件。我还是很喜欢哲基尔。它很快，主机很便宜，而且如果你用 GitHub 页面的话还是免费的，但我当时没有用。我不喜欢 Jekyll 的两点是缺乏可移植性和易于发布。

轻松发布其实是两件事。第一，我不喜欢克隆我的回购协议，运行`bundle install`并确保一切顺利进行(旁注:尽管很诱人，但不要对任何目录使用子模块),然后构建并发布。然后，正如我上面所说的，我没有使用 GitHub 页面，所以我基本上编译了这个网站，并将整个内容上传到我的网站。很可怕，对吧？

然后，还有图像的问题。我喜欢在我的帖子里放一些图片，事实上，我的很多帖子都围绕着图片。如果你用 Jekyll 运行自己的主机，这很容易处理，但是用 GitHub 页面，这对于可移植性来说是一个噩梦，即使是子模块。如果你在构建的时候没有所有的图片，为什么 Jekyll 要把这些图片带到你的网站上呢！

在我解释完下一部分之后，这两个花絮将会很重要。

快进到我的博客 2.0，我决定放弃自托管，迁移到 GitHub，这样做很简单，只需“git commit -m blah && git push”并让 GitHub 做脏活。这很有效。还记得我厚重的书页吗？晚安。母马。现在，我在网络服务器上的图片目录是 119MB。119MB 我会扔在 GitHub 上。Bleh，移动博客不好玩。

最近，我有足够多的博客文章，滚动浏览它们是一种痛苦。我认为一个简单的 Jekyll 分页插件就足够了——直到我发现 GitHub 页面不支持它。“不”我愤怒地吼叫着。我快速地搜索了一下，发现有了一点 Ruby，我就可以~滥用~使用 Travis 来帮助完成更繁重的工作，绕过 GitHub 的 building 服务，并利用这种甜蜜的托管服务。

在我开始“如何分手”之前，我想说我仍然使用我的虚拟主机来保存图片。事实上，这是一个关键部分。我想我的脚本可以被改编成其他服务，而不是你自己的服务。

## 操作方法:

### 先决条件:

*   可以与 API 一起使用的图像主机
*   吉基尔博士
*   您的公共 GitHub repo 上的两个分支(gh-pages 和一个源 repo)
*   眼睛
*   个人访问令牌
*   特拉维斯访问你的公共 GitHub 回购

明白这是怎么回事了吗？

基本上，每当您将源分支推送到 GitHub 时，Travis 都会构建站点，然后将其推送到您的 gh-pages repo，并进行预编译。这为更多的可能性打开了大门，比如测试！

当然，构建时间是慢了一点，但它工作得更好！

下面是我的. travis.yml 文件的一个例子:

```
language: ruby
cache: bundler
install:
- bundle install
script: bundle exec rake site:deploy --quiet
rvm:
- 2.1.2
env:
  global:
    secure: /* remove this line */ 
```

Enter fullscreen mode Exit fullscreen mode

对于那个`secure`位，删除整行，运行这个命令:
`gem install travis`
，然后:
`travis encrypt 'GIT_NAME="YOUR_USERNAME" GIT_EMAIL="YOUR_EMAIL" GH_TOKEN=YOUR_TOKEN'`

确保您替换了适当的变量。

现在，从 Rakefile:

```
....
    task :deploy do
        # Detect pull request
        if ENV['TRAVIS_PULL_REQUEST'].to_s.to_i > 0
            puts 'Pull request detected. Not proceeding with deploy.'
            exit
        end

        # Configure git if this is run in Travis CI
        if ENV['TRAVIS']
            sh "git config --global user.name '#{ENV['GIT_NAME']}'"
            sh "git config --global user.email '#{ENV['GIT_EMAIL']}'"
            sh 'git config --global push.default simple'
        end

        # Make sure destination folder exists as git repo
        check_destination

        sh "git checkout #{SOURCE_BRANCH}"
        Dir.chdir(CONFIG['destination']) { sh "git checkout #{DESTINATION_BRANCH}" }

        # Generate the site
        sh 'bundle exec jekyll build'

        # Commit and push to github
        sha = `git log`.match(/[a-z0-9]{40}/)[0]
        Dir.chdir(CONFIG['destination']) do
            sh 'git add --all .'
            sh "git commit -m 'Updating to #{USERNAME}/#{REPO}@#{sha}.'"
            sh "git push --quiet origin #{DESTINATION_BRANCH}"
            puts "Pushed updated branch #{DESTINATION_BRANCH} to GitHub Pages"
        end
    end
.... 
```

Enter fullscreen mode Exit fullscreen mode

(整个文件可在[这里](https://gist.github.com/128keaton/c7a2bb21ca225fe24b7b1beb4836ff4b)获得)

有一个巨大的混乱。

基本上，当 Travis 运行`bundle exec rake site:deploy`时，它检查它是否是 PR，设置 git，确保目标文件夹存在，拉站点，检查源分支，构建站点，然后提交并最终推送站点。唷！

下面是您需要添加的适当的 _config.yml 行:

```
 username: your-github-username
repo: repo-name
branch: source-branch
destination: ../site/ 
```

Enter fullscreen mode Exit fullscreen mode

如果设置正确，推送至 GitHub 应该会导致成功的 Travis 构建和部署。

现在，进入有趣的部分:图像！

在我的 Rakefile 中，我有两个助手任务:

```
...
    task :correct_posts do
        puts 'Correcting blog posts'
        Dir.entries(__dir__ + '/_posts/').each do |file_name|
            next unless File.extname(file_name) == '.md' || File.extname(file_name) == '.markdown'
            text = File.read(__dir__ + '/_posts/' + file_name)
            fixed = text.gsub('img/', '](http://images.128keaton.com/')
            fixed = fixed.gsub('_images/', '')
            File.open(__dir__ + '/_posts/' + file_name, 'w') { |file| file.puts fixed }
        end
        Rake::Task["site:upload"].execute
    end
    task :upload_images do
        puts 'Uploading images..'
        options = { recursive: true, password: SSH_PASSWORD }
        Net::SCP.upload!(SSH_HOST, SSH_USERNAME, __dir__ +img/', '/home/12/128keaton.com/html/', options)
        puts 'All images have been upload and removed in ' + IMAGES_DIR
        FileUtils.rm_rf(Dir.glob(IMAGES_DIR + '*'))
    end
... 
```

Enter fullscreen mode Exit fullscreen mode

第一个任务是检查我所有的帖子中是否有“陈旧”的图片链接，或者是旧博客时代的，或者是之前没有我的“CDN”的。它只是预先考虑它们，然后继续前进。我的第二个任务实际上是上传我的图片并从 repo 中删除它们，这样它们就不会被 git 吸收了。它只是使用 Net::SCP 将它们 SCP 到我的远程服务器。不错吧。我想象任何人有时间和一些经验可以返工的任务，以支持他们想要的任何东西，我只是用我自己的主机，因为它很容易！

## 最后的想法

虽然我没有我想要的完全可移植的 Jekyll 设置，但我仍然认为这是一个非常健壮的方法来保持你的站点按照你想要的方式构建(用日志！)并将您的图像放在您想要的位置。我可以做的一件有趣的事情是随时随地 SSH 到我的家用机器(或者用 SSHFS 挂载 site 文件夹)，写一篇文章，然后运行另一个 helper 方法`bundle exec rake site:upload`，它是这样的:

```
 task :upload do
      puts 'Uploading site'
      sha = `git log`.match(/[a-z0-9]{40}/)[0]
      sh 'git add --all .'
      sh "git commit -m 'Updating to #{USERNAME}/#{REPO}@#{sha}.'"
      sh "git push --quiet origin #{SOURCE_BRANCH}"
      puts "Pushed updated branch #{SOURCE_BRANCH} to GitHub Pages"
  end 
```

Enter fullscreen mode Exit fullscreen mode

再加上一些 Atom Jekyll 插件，每天使用 Jekyll 会更加愉快。

## 学分:

*   [为我指明正确方向的原始回购](https://github.com/mfenner/jekyll-travis)
*   [有助于产生这种想法的中等职位](https://medium.com/@nthgergo/publishing-gh-pages-with-travis-ci-53a8270e87db)