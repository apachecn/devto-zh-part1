# 新 Jekyll 网站

> 原文：<https://dev.to/psnebc/new-jekyll-site-482j>

```
➜  Downloads jekyll new mytheme
Running bundle install in /home/psnc/Downloads/mytheme... 
  Bundler: The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mingw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.
  Bundler: Fetching gem metadata from https://rubygems.org/...........
  Bundler: Fetching gem metadata from https://rubygems.org/.
  Bundler: Resolving dependencies...
  Bundler: Using public_suffix 3.0.1
  Bundler: Using addressable 2.5.2
  Bundler: Using bundler 1.16.1
  Bundler: Using colorator 1.1.0
  Bundler: Using concurrent-ruby 1.0.5
  Bundler: Using eventmachine 1.2.5
  Bundler: Using http_parser.rb 0.6.0
  Bundler: Using em-websocket 0.5.1
  Bundler: Using ffi 1.9.18
  Bundler: Using forwardable-extended 2.6.0
  Bundler: Using i18n 0.9.1
  Bundler: Using rb-fsevent 0.10.2
  Bundler: Using rb-inotify 0.9.10
  Bundler: Using sass-listen 4.0.0
  Bundler: Using sass 3.5.4
  Bundler: Using jekyll-sass-converter 1.5.1
  Bundler: Using ruby_dep 1.5.0
  Bundler: Using listen 3.1.5
  Bundler: Using jekyll-watch 2.0.0
  Bundler: Using kramdown 1.16.2
  Bundler: Using liquid 4.0.0
  Bundler: Using mercenary 0.3.6
  Bundler: Using pathutil 0.16.1
  Bundler: Using rouge 3.1.0
  Bundler: Using safe_yaml 1.0.4
  Bundler: Using jekyll 3.7.0
  Bundler: Using jekyll-feed 0.9.2
  Bundler: Using minima 2.1.1
  Bundler: Bundle complete! 4 Gemfile dependencies, 28 gems now installed.
  Bundler: Use `bundle info [gemname]` to see where a bundled gem is installed.
New jekyll site installed in /home/psnc/Downloads/mytheme. 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  Downloads cd mytheme 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  mytheme bundle install
Using public_suffix 3.0.1
Using addressable 2.5.2
Using bundler 1.16.1
Using colorator 1.1.0
Using concurrent-ruby 1.0.5
Using eventmachine 1.2.5
Using http_parser.rb 0.6.0
Using em-websocket 0.5.1
Using ffi 1.9.18
Using forwardable-extended 2.6.0
Using i18n 0.9.1
Using rb-fsevent 0.10.2
Using rb-inotify 0.9.10
Using sass-listen 4.0.0
Using sass 3.5.4
Using jekyll-sass-converter 1.5.1
Using ruby_dep 1.5.0
Using listen 3.1.5
Using jekyll-watch 2.0.0
Using kramdown 1.16.2
Using liquid 4.0.0
Using mercenary 0.3.6
Using pathutil 0.16.1
Using rouge 3.1.0
Using safe_yaml 1.0.4
Using jekyll 3.7.0
Using jekyll-feed 0.9.2
Using minima 2.1.1
Bundle complete! 4 Gemfile dependencies, 28 gems now installed.
Use `bundle info [gemname]` to see where a bundled gem is installed.
➜  mytheme jekyll serve
Configuration file: /home/psnc/Downloads/mytheme/_config.yml
            Source: /home/psnc/Downloads/mytheme
       Destination: /home/psnc/Downloads/mytheme/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.349 seconds.
 Auto-regeneration: enabled for '/home/psnc/Downloads/mytheme'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
[2018-01-04 19:09:53] ERROR `/favicon.ico' not found. 
```

Enter fullscreen mode Exit fullscreen mode