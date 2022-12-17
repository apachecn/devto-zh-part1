# 安装 Grav CMS

> 原文：<https://dev.to/psnebc/install-grav-cms-6oj>

```
➜  01 composer create-project getgrav/grav
Installing getgrav/grav (1.3.10)
  - Installing getgrav/grav (1.3.10): Loading from cache
Created project in /home/psnc/webroot/grav
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 64 installs, 0 updates, 0 removals
  - Installing antoligy/dom-string-iterators (v1.0.0): Loading from cache
  - Installing composer/ca-bundle (1.0.8): Loading from cache
  - Installing doctrine/cache (v1.6.2): Loading from cache
  - Installing doctrine/collections (v1.3.0): Loading from cache
  - Installing donatj/phpuseragentparser (v0.7.0): Loading from cache
  - Installing erusev/parsedown (1.6.3): Loading from cache
  - Installing erusev/parsedown-extra (0.7.1): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing filp/whoops (2.1.10): Loading from cache
  - Installing gregwar/cache (v1.0.12): Loading from cache
  - Installing gregwar/image (v2.0.21): Loading from cache
  - Installing seld/cli-prompt (1.0.3): Loading from cache
  - Installing league/climate (3.2.1): Loading from cache
  - Installing matthiasmullie/path-converter (1.1.0): Loading from cache
  - Installing matthiasmullie/minify (1.3.52): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.5.0): Loading from cache
  - Installing symfony/var-dumper (v2.8.27): Loading from cache
  - Installing maximebf/debugbar (v1.14.0): Loading from cache
  - Installing miljar/php-exif (v0.6.3): Loading from cache
  - Installing monolog/monolog (1.23.0): Loading from cache
  - Installing psr/container (1.0.0): Loading from cache
  - Installing symfony/yaml (v2.8.27): Loading from cache
  - Installing symfony/event-dispatcher (v2.8.27): Loading from cache
  - Installing pimple/pimple (v3.2.2): Loading from cache
  - Installing rockettheme/toolbox (1.3.8): Loading from cache
  - Installing symfony/debug (v3.0.9): Loading from cache
  - Installing symfony/polyfill-iconv (v1.5.0): Loading from cache
  - Installing twig/twig (v1.35.0): Loading from cache
  - Installing symfony/finder (v3.3.9): Loading from cache
  - Installing symfony/dom-crawler (v3.3.9): Loading from cache
  - Installing symfony/css-selector (v3.3.9): Loading from cache
  - Installing symfony/console (v2.8.27): Loading from cache
  - Installing symfony/browser-kit (v3.3.9): Loading from cache
  - Installing stecman/symfony-console-completion (0.7.0): Loading from cache
  - Installing sebastian/diff (1.4.3): Loading from cache
  - Installing sebastian/recursion-context (1.0.5): Loading from cache
  - Installing sebastian/exporter (1.2.2): Loading from cache
  - Installing sebastian/comparator (1.2.4): Loading from cache
  - Installing phpunit/php-text-template (1.2.1): Loading from cache
  - Installing doctrine/instantiator (1.0.5): Loading from cache
  - Installing phpunit/phpunit-mock-objects (2.3.8): Loading from cache
  - Installing sebastian/version (1.0.6): Loading from cache
  - Installing sebastian/global-state (1.1.1): Loading from cache
  - Installing sebastian/environment (1.3.8): Loading from cache
  - Installing phpunit/php-timer (1.0.9): Loading from cache
  - Installing phpunit/php-file-iterator (1.4.2): Loading from cache
  - Installing phpunit/php-token-stream (1.4.11): Loading from cache
  - Installing phpunit/php-code-coverage (2.2.4): Loading from cache
  - Installing webmozart/assert (1.2.0): Loading from cache
  - Installing phpdocumentor/reflection-common (1.0.1): Loading from cache
  - Installing phpdocumentor/type-resolver (0.3.0): Loading from cache
  - Installing phpdocumentor/reflection-docblock (3.2.2): Loading from cache
  - Installing phpspec/prophecy (v1.7.2): Loading from cache
  - Installing phpunit/phpunit (4.8.36): Loading from cache
  - Installing psr/http-message (1.0.1): Loading from cache
  - Installing guzzlehttp/psr7 (1.4.2): Loading from cache
  - Installing guzzlehttp/promises (v1.3.1): Loading from cache
  - Installing guzzlehttp/guzzle (6.3.0): Loading from cache
  - Installing symfony/process (v3.3.9): Loading from cache
  - Installing facebook/webdriver (1.4.1): Loading from cache
  - Installing behat/gherkin (v4.4.5): Loading from cache
  - Installing codeception/codeception (2.3.5): Loading from cache
  - Installing fzaninotto/faker (v1.7.1): Loading from cache
  - Installing victorjonsson/markdowndocs (dev-master c9fa153): Cloning c9fa153b28 from cache
filp/whoops suggests installing whoops/soap (Formats errors as SOAP responses)
gregwar/image suggests installing behat/transliterator (Transliterator provides ability to set non-latin1 pretty names)
matthiasmullie/minify suggests installing psr/cache-implementation (Cache implementation to use with Minify::cache)
symfony/var-dumper suggests installing ext-symfony_debug ()
maximebf/debugbar suggests installing kriswallsmith/assetic (The best way to manage assets)
maximebf/debugbar suggests installing predis/predis (Redis storage)
miljar/php-exif suggests installing lib-exiftool (Use perl lib exiftool as adapter)
monolog/monolog suggests installing aws/aws-sdk-php (Allow sending log messages to AWS services like DynamoDB)
monolog/monolog suggests installing doctrine/couchdb (Allow sending log messages to a CouchDB server)
monolog/monolog suggests installing ext-amqp (Allow sending log messages to an AMQP server (1.0+ required))
monolog/monolog suggests installing ext-mongo (Allow sending log messages to a MongoDB server)
monolog/monolog suggests installing graylog2/gelf-php (Allow sending log messages to a GrayLog2 server)
monolog/monolog suggests installing mongodb/mongodb (Allow sending log messages to a MongoDB server via PHP Driver)
monolog/monolog suggests installing php-amqplib/php-amqplib (Allow sending log messages to an AMQP server using php-amqplib)
monolog/monolog suggests installing php-console/php-console (Allow sending log messages to Google Chrome)
monolog/monolog suggests installing rollbar/rollbar (Allow sending log messages to Rollbar)
monolog/monolog suggests installing ruflin/elastica (Allow sending log messages to an Elastic Search server)
monolog/monolog suggests installing sentry/sentry (Allow sending log messages to a Sentry server)
symfony/event-dispatcher suggests installing symfony/dependency-injection ()
symfony/event-dispatcher suggests installing symfony/http-kernel ()
phpunit/phpunit-mock-objects suggests installing ext-soap (*)
sebastian/global-state suggests installing ext-uopz (*)
phpunit/php-code-coverage suggests installing ext-xdebug (>=2.2.1)
phpunit/phpunit suggests installing phpunit/php-invoker (~1.1)
codeception/codeception suggests installing codeception/specify (BDD-style code blocks)
codeception/codeception suggests installing codeception/verify (BDD-style assertions)
codeception/codeception suggests installing flow/jsonpath (For using JSONPath in REST module)
codeception/codeception suggests installing league/factory-muffin (For DataFactory module)
codeception/codeception suggests installing league/factory-muffin-faker (For Faker support in DataFactory module)
codeception/codeception suggests installing phpseclib/phpseclib (for SFTP option in FTP Module)
codeception/codeception suggests installing symfony/phpunit-bridge (For phpunit-bridge support)
Generating autoload files
> bin/grav install Installing vendor dependencies
Loading composer repositories with package information
Installing dependencies from lock file
Package operations: 0 installs, 0 updates, 35 removals
  - Removing webmozart/assert (1.2.0)
  - Removing victorjonsson/markdowndocs (dev-master)
  - Removing symfony/process (v3.3.9)
  - Removing symfony/finder (v3.3.9)
  - Removing symfony/dom-crawler (v3.3.9)
  - Removing symfony/css-selector (v3.3.9)
  - Removing symfony/browser-kit (v3.3.9)
  - Removing stecman/symfony-console-completion (0.7.0)
  - Removing sebastian/version (1.0.6)
  - Removing sebastian/recursion-context (1.0.5)
  - Removing sebastian/global-state (1.1.1)
  - Removing sebastian/exporter (1.2.2)
  - Removing sebastian/environment (1.3.8)
  - Removing sebastian/diff (1.4.3)
  - Removing sebastian/comparator (1.2.4)
  - Removing psr/http-message (1.0.1)
  - Removing phpunit/phpunit-mock-objects (2.3.8)
  - Removing phpunit/phpunit (4.8.36)
  - Removing phpunit/php-token-stream (1.4.11)
  - Removing phpunit/php-timer (1.0.9)
  - Removing phpunit/php-text-template (1.2.1)
  - Removing phpunit/php-file-iterator (1.4.2)
  - Removing phpunit/php-code-coverage (2.2.4)
  - Removing phpspec/prophecy (v1.7.2)
  - Removing phpdocumentor/type-resolver (0.3.0)
  - Removing phpdocumentor/reflection-docblock (3.2.2)
  - Removing phpdocumentor/reflection-common (1.0.1)
  - Removing guzzlehttp/psr7 (1.4.2)
  - Removing guzzlehttp/promises (v1.3.1)
  - Removing guzzlehttp/guzzle (6.3.0)
  - Removing fzaninotto/faker (v1.7.1)
  - Removing facebook/webdriver (1.4.1)
  - Removing doctrine/instantiator (1.0.5)
  - Removing codeception/codeception (2.3.5)
  - Removing behat/gherkin (v4.4.5)
Generating optimized autoload files

Cloning Bits
============

Cloning into 'user/plugins/problems'...
SUCCESS cloned https://github.com/getgrav/grav-plugin-problems -> /home/psnc/webroot/grav/user/plugins/problems

Cloning into 'user/plugins/error'...
SUCCESS cloned https://github.com/getgrav/grav-plugin-error -> /home/psnc/webroot/grav/user/plugins/error

Cloning into 'user/plugins/markdown-notices'...
SUCCESS cloned https://github.com/getgrav/grav-plugin-markdown-notices -> /home/psnc/webroot/grav/user/plugins/markdown-notices

Cloning into 'user/themes/antimatter'...
SUCCESS cloned https://github.com/getgrav/grav-theme-antimatter -> /home/psnc/webroot/grav/user/themes/antimatter 
```

Enter fullscreen mode Exit fullscreen mode

```
➜  grav bin/gpm index                    

GPM Releases Configuration: Stable

PLUGINS [ 223 ]
------------------------------------------------------------------------------------------------
| Count | Name                     | Slug                       | Version | Installed     |
===========================================================================================
| 1     | Staticfilecache          | staticfilecache            | v1.0.0  | not installed |
| 2     | Embed                    | embed                      | v1.0.0  | not installed |
| 3     | filesource               | filesource                 | v0.2    | not installed |
| 4     | TopicMenu                | topicmenu                  | v1.1.0  | not installed |
| 5     | Archive Plus             | archive_plus               | v1.3.2  | not installed |
| 6     | File Content             | file-content               | v1.0.0  | not installed |
| 7     | Smileys                  | smileys                    | v1.2.1  | not installed |
| 8     | Shortcodes               | shortcodes                 | v1.2.0  | not installed |
| 9     | Gitter                   | gitter                     | v1.0.0  | not installed |
| 10    | Mautic                   | mautic                     | v1.0.0  | not installed |
| 11    | Hitbox                   | hitbox                     | v1.0.0  | not installed |
| 12    | Twitch                   | twitch                     | v1.1.0  | not installed |
| 13    | Leaflet                  | leaflet                    | v1.0.1  | not installed |
| 14    | SocialButtons            | socialbuttons              | v1.0.3  | not installed |
| 15    | DropCaps                 | dropcaps                   | v1.3.4  | not installed |
| 16    | Markdown Sections        | markdown-sections          | v0.9.1  | not installed |
| 17    | Markdown Notices         | markdown-notices           | v1.0.0  | installed     |
| 18    | Assets                   | assets                     | v1.2.1  | not installed |
| 19    | Yandex Metrika           | metrika                    | v1.0.0  | not installed |
| 20    | Markdown Color           | markdown-color             | v1.0.1  | not installed |
| 21    | Random                   | random                     | v1.5.0  | not installed |
| 22    | HTTP Basic Authentic...  | httpbasicauth              | v1.0.0  | not installed |
| 23    | Uikitifier               | uikitifier                 | v0.3.0  | not installed |
| 24    | Smartypants              | smartypants                | v2.5.0  | not installed |
| 25    | Mini Code                | minicode                   | v1.1.0  | not installed |
| 26    | twigpcre                 | twigpcre                   | v1.8.0  | not installed |
| 27    | Customadmin              | customadmin                | v1.0.0  | not installed |
| 28    | Markdown Font Awesom...  | markdown-fontawesome       | v1.0.1  | not installed |
| 29    | Tidyhtml                 | tidyhtml                   | v1.0.3  | not installed |
| 30    | GitHub                   | github                     | v1.3.0  | not installed |
| 31    | Pagination               | pagination                 | v1.3.2  | not installed |
| 32    | Grav Pages as Data       | page-as-data               | v0.1.0  | not installed |
| 33    | Url-Paramfilter          | urlparamfilter             | v1.2    | not installed |
| 34    | Image Collage            | image-collage              | v1.0.4  | not installed |
| 35    | Music Card               | musiccard                  | v1.2.3  | not installed |
| 36    | Feed Us                  | feed-us                    | v0.1.3  | not installed |
| 37    | Log Errors               | logerrors                  | v1.0.1  | not installed |
| 38    | SweetCaptcha             | sweetcaptcha               | v1.0.2  | not installed |
| 39    | AdvancedPageCache        | advanced-pagecache         | v1.2.0  | not installed |
| 40    | Related Pages            | relatedpages               | v1.1.4  | not installed |
| 41    | Themer                   | themer                     | v1.1.0  | not installed |
| 42    | Custom CSS               | custom-css                 | v0.2.1  | not installed |
| 43    | CORS                     | cors                       | v1.0.1  | not installed |
| 44    | BlogInjector             | blog-injector              | v1.2.3  | not installed |
| 45    | CookiesPolicy            | cookiespolicy              | v1.0.10 | not installed |
| 46    | Text Formatter           | textformatter              | v1.1.0  | not installed |
| 47    | Date Tools               | datetools                  | v1.0.6  | not installed |
| 48    | Form Database            | form-database              | v1.0.0  | not installed |
| 49    | BibLaTeX                 | biblatex                   | v1.0.1  | not installed |
| 50    | Bibliography             | bibliography               | v1.0.0  | not installed |
| 51    | Tagcloud                 | tagcloud                   | v1.0.2  | not installed |
| 52    | Blogroll                 | blogroll                   | v1.0.2  | not installed |
| 53    | Snipcart                 | snipcart                   | v1.7.1  | not installed |
| 54    | Filter Strings           | filter-strings             | v1.1.0  | not installed |
| 55    | Twig Jasny Array         | twig-jasny-array           | v1.0.1  | not installed |
| 56    | Events                   | events                     | v1.0.15 | not installed |
| 57    | Recent Posts             | recent-posts               | v1.0.0  | not installed |
| 58    | Error                    | error                      | v1.6.0  | installed     |
| 59    | Data Manager             | data-manager               | v1.0.7  | not installed |
| 60    | Chessboard               | chessboard                 | v0.1.0  | not installed |
| 61    | Header By Taxonomy       | header-by-taxonomy         | v1.0.1  | not installed |
| 62    | Graveyard                | graveyard                  | v1.0.2  | not installed |
| 63    | Tablesorter              | tablesorter                | v1.1.4  | not installed |
| 64    | Roman                    | roman                      | v1.0.0  | not installed |
| 65    | Word Count               | wordcount                  | v1.0.0  | not installed |
| 66    | View Source              | view-source                | v1.0.1  | not installed |
| 67    | Prism Highlighter        | prism-highlight            | v1.0.0  | not installed |
| 68    | Backlinks                | backlinks                  | v1.0.0  | not installed |
| 69    | Count Views              | count-views                | v2.0.0  | not installed |
| 70    | Optimus                  | optimus                    | v1.3.0  | not installed |
| 71    | Scheduler                | scheduler                  | v1.0.0  | not installed |
| 72    | Flickr                   | flickr                     | v1.0.6  | not installed |
| 73    | Gallery                  | gffi                       | v0.0.1  | not installed |
| 74    | LoginLdap                | loginldap                  | v0.3.0  | not installed |
| 75    | Shopping Cart Stripe...  | shoppingcart-stripe        | v1.1.1  | not installed |
| 76    | Classifier               | classifier                 | v1.0.3  | not installed |
| 77    | Shopping Cart PayPal...  | shoppingcart-paypal        | v1.1.2  | not installed |
| 78    | Google Analytics         | ganalytics                 | v1.4.0  | not installed |
| 79    | CC-Linker                | cclinker                   | v0.5.3  | not installed |
| 80    | Twig Cache               | twigcache                  | v1.3.0  | not installed |
| 81    | CookiesNotice            | cookiesnotice              | v1.0.2  | not installed |
| 82    | Shortcode Owl Carous...  | shortcode-owl-carousel     | v1.0.1  | not installed |
| 83    | Ui Libraries             | ui-libraries               | v0.1.0  | not installed |
| 84    | Guestbook                | guestbook                  | v0.5.0  | not installed |
| 85    | Import                   | import                     | v1.1.0  | not installed |
| 86    | Google Charts            | google-charts              | v1.0.1  | not installed |
| 87    | Bootstrapper             | bootstrapper               | v1.3.4  | not installed |
| 88    | CDN                      | cdn                        | v1.4.0  | not installed |
| 89    | Slack Invite             | slack-invite               | v1.0.0  | not installed |
| 90    | Social Counters          | social-counters            | v1.0.0  | not installed |
| 91    | MediaEmbed               | mediaembed                 | v1.3.0  | not installed |
| 92    | OER Schema               | oerschema                  | v0.1.0  | not installed |
| 93    | BibTeXify                | bibtexify                  | v1.1.0  | not installed |
| 94    | Grava11y (Acccessibi...  | grava11y                   | v1.0.2  | not installed |
| 95    | Maintenance              | maintenance                | v1.3.1  | not installed |
| 96    | External Links           | external_links             | v1.5.3  | not installed |
| 97    | IPLocate                 | iplocate                   | v1.0.3  | not installed |
| 98    | Shortcode Assets         | shortcode-assets           | v1.0.2  | not installed |
| 99    | Image Srcset             | imgsrcset                  | v1.0.3  | not installed |
| 100   | srcset Helper            | srcset                     | v1.0.0  | not installed |
| 101   | Resize Images            | resize-images              | v0.2.2  | not installed |
| 102   | Vimeo                    | vimeo                      | v1.0.3  | not installed |
| 103   | Groove                   | groove                     | v0.9.0  | not installed |
| 104   | Gravstrap                | gravstrap                  | v1.2.3  | not installed |
| 105   | QR Code                  | qrcode                     | v1.1.0  | not installed |
| 106   | Subscriber               | subscriber                 | v1.0.1  | not installed |
| 107   | Flex-Directory           | flex-directory             | v2.0.1  | not installed |
| 108   | Hypothesis               | hypothesis                 | v0.9.3  | not installed |
| 109   | piwik                    | piwik                      | v1.0.2  | not installed |
| 110   | PreCache                 | precache                   | v1.1.3  | not installed |
| 111   | Diagrams                 | diagrams                   | v1.0.1  | not installed |
| 112   | Backup Manager           | backup-manager             | v0.1.5  | not installed |
| 113   | LightSlider              | lightslider                | v1.5.3  | not installed |
| 114   | MathJax                  | mathjax                    | v1.5.1  | not installed |
| 115   | Admin Identicons         | adminidenticons            | v1.0.3  | not installed |
| 116   | Google Maps              | google-maps                | v1.0.7  | not installed |
| 117   | Psysh                    | psysh                      | v1.2.0  | not installed |
| 118   | Sitemap                  | sitemap                    | v1.9.1  | not installed |
| 119   | Materializer             | materializer               | v1.8.0  | not installed |
| 120   | Shopping Cart            | shoppingcart               | v1.2.2  | not installed |
| 121   | Comments                 | comments                   | v1.2.7  | not installed |
| 122   | Responsive Images        | responsive-images          | v1.0.0  | not installed |
| 123   | Twig Extensions          | twig-extensions            | v1.0.2  | not installed |
| 124   | Directory Listing        | directorylisting           | v1.1.1  | not installed |
| 125   | Problems                 | problems                   | v1.4.7  | installed     |
| 126   | Shortcode UI             | shortcode-ui               | v2.2.5  | not installed |
| 127   | Archives                 | archives                   | v1.5.1  | not installed |
| 128   | Unitegallery             | unitegallery               | v1.1.3  | not installed |
| 129   | Auto Date                | auto-date                  | v1.0.2  | not installed |
| 130   | Webmention               | webmention                 | v1.1.0  | not installed |
| 131   | reCAPTCHA Contact        | recaptchacontact           | v2.1.2  | not installed |
| 132   | Admin Styles             | adminstyles                | v1.1.1  | not installed |
| 133   | Social Meta Tags         | social-meta-tags           | v0.2.0  | not installed |
| 134   | Reading Time             | readingtime                | v1.2.0  | not installed |
| 135   | Feed                     | feed                       | v1.6.2  | not installed |
| 136   | Toc                      | toc                        | v1.4.1  | not installed |
| 137   | AdSense                  | adsense                    | v1.1.1  | not installed |
| 138   | Pingdom Real User Mo...  | pingdom_rum                | v0.2.1  | not installed |
| 139   | Bigfoot JS               | bigfoot-js                 | v0.1.4  | not installed |
| 140   | Nomnoml Uml Diagrams     | nomnoml-uml-diagrams       | v1.0.0  | not installed |
| 141   | Esperanto Helper         | esperanto-helper           | v1.0.2  | not installed |
| 142   | BibleRef                 | bibleref                   | v0.1.0  | not installed |
| 143   | LangSwitcher             | langswitcher               | v1.4.0  | not installed |
| 144   | Editor Buttons           | editor-buttons             | v1.2.1  | not installed |
| 145   | Table Importer           | table-importer             | v2.1.5  | not installed |
| 146   | Editable With Simple...  | editable-simplemde         | v0.1.0  | not installed |
| 147   | Shoppingcart 2checko...  | shoppingcart-2checkout-dev | v0.0.1  | not installed |
| 148   | Pubmed                   | pubmed                     | v2.0.2  | not installed |
| 149   | Social & SEO Meta Ta...  | social-seo-metatags        | v1.0.1  | not installed |
| 150   | Star Ratings             | star-ratings               | v2.0.1  | not installed |
| 151   | Language Selector        | language-selector          | v1.0.0  | not installed |
| 152   | Thumb Ratings            | thumb-ratings              | v0.1.1  | not installed |
| 153   | Featherlight             | featherlight               | v1.5.0  | not installed |
| 154   | Cascade Filters          | cascade-filters            | v0.1.0  | not installed |
| 155   | Page Toc                 | page-toc                   | v1.0.0  | not installed |
| 156   | Print Friendly           | printfriendly              | v0.5.0  | not installed |
| 157   | Markdown Spoilers        | markdown-spoilers          | v1.0.0  | not installed |
| 158   | Quick Tray Links         | quick-tray-links           | v1.0.0  | not installed |
| 159   | Git Sync                 | git-sync                   | v1.0.4  | not installed |
| 160   | OnWebChat                | onwebchat                  | v1.2    | not installed |
| 161   | Clicksend Form Notif...  | grav-form-notifier         | v1.0.0  | not installed |
| 162   | Shortcode Chart.js       | shortcode-chartjs          | v1.0.1  | not installed |
| 163   | Shortcode Ruby           | shortcode-ruby             | v1.0.0  | not installed |
| 164   | Auto Author              | auto-author                | v0.1.0  | not installed |
| 165   | Add Page By Form         | add-page-by-form           | v2.1.0  | not installed |
| 166   | Users Page Ownership     | users-page-ownership       | v0.9.4  | not installed |
| 167   | Twig Feeds               | twigfeeds                  | v3.3.0  | not installed |
| 168   | YouTube                  | youtube                    | v2.0.4  | not installed |
| 169   | Anchors                  | anchors                    | v1.5.2  | not installed |
| 170   | FetchWP                  | fetchwp                    | v0.6.0  | not installed |
| 171   | DevTools                 | devtools                   | v1.0.8  | not installed |
| 172   | PasswordGen              | passwordgen                | v0.1.4  | not installed |
| 173   | Widget                   | widget                     | v1.2.0  | not installed |
| 174   | Proposal                 | proposal                   | v1.0.0  | not installed |
| 175   | AboutMe                  | aboutme                    | v1.1.5  | not installed |
| 176   | Freshdesk                | freshdesk                  | v1.0.0  | not installed |
| 177   | One Time Login           | one-time-login             | v0.1.5  | not installed |
| 178   | Admin Addon Media Re...  | admin-addon-media-rename   | v1.6.2  | not installed |
| 179   | Breadcrumbs              | breadcrumbs                | v1.5.1  | not installed |
| 180   | Facebook                 | facebook                   | v1.7.0  | not installed |
| 181   | Cookie Consent           | cookieconsent              | v0.2.0  | not installed |
| 182   | Taxonomy List            | taxonomylist               | v1.3.1  | not installed |
| 183   | Simple Responsive Ta...  | simple-responsive-tables   | v1.1.1  | not installed |
| 184   | Speech Bubble            | speech-bubble              | v2.1.0  | not installed |
| 185   | Social Feed              | social-feed                | v0.1.2  | not installed |
| 186   | Instagram Feed           | instagram-feed             | v1.0.0  | not installed |
| 187   | Private                  | private                    | v1.0.9  | not installed |
| 188   | View                     | view                       | v1.3.2  | not installed |
| 189   | Instagram                | instagram                  | v1.3.0  | not installed |
| 190   | Aviary Image Editor      | aviary                     | v1.0.3  | not installed |
| 191   | Image Captions           | image-captions             | v1.0.0  | not installed |
| 192   | Blackhole                | blackhole                  | v0.9.2  | not installed |
| 193   | Highlight                | highlight                  | v1.4.0  | not installed |
| 194   | Email                    | email                      | v2.7.1  | installed     |
| 195   | Login                    | login                      | v2.5.0  | installed     |
| 196   | Admin Panel              | admin                      | v1.6.7  | installed     |
| 197   | Login Plugin OAuth A...  | login-oauth                | v1.4.0  | not installed |
| 198   | Admin Addon Revision...  | admin-addon-revisions      | v1.3.2  | not installed |
| 199   | Page Inject              | page-inject                | v1.3.0  | not installed |
| 200   | Img Captions             | imgcaptions                | v2.0.0  | not installed |
| 201   | Amazon Products          | amazon-products            | v0.5.0  | not installed |
| 202   | Gantry 5 Framework       | gantry5                    | v5.4.22 | installed     |
| 203   | GetId3                   | get-id3                    | v1.0.2  | not installed |
| 204   | Frontend Edit Button     | frontend-edit-button       | v1.0.4  | not installed |
| 205   | Form                     | form                       | v2.11.1 | installed     |
| 206   | Codepen Embed            | codepen-embed              | v0.1.0  | not installed |
| 207   | SnappyGrav               | snappygrav                 | v1.6.0  | not installed |
| 208   | Admin Addon Media So...  | admin-addon-media-sort     | v0.1.0  | not installed |
| 209   | Markdown RubyText        | rubytext                   | v3.1.4  | not installed |
| 210   | Webcomponents - Poly...  | webcomponents              | v1.0.1  | not installed |
| 211   | Minify Html              | minify-html                | v0.0.7  | not installed |
| 212   | Astrobin                 | astrobin                   | v1.0.3  | not installed |
| 213   | Admin Addon User Man...  | admin-addon-user-manager   | v2.0.1  | not installed |
| 214   | Podcast                  | podcast                    | v2.1.1  | not installed |
| 215   | Mobile Detect            | mobile-detect              | v1.3.1  | not installed |
| 216   | SEO                      | seo                        | v2.1.9  | not installed |
| 217   | Auto SEO                 | autoseo                    | v1.3.2  | not installed |
| 218   | SimpleSearch             | simplesearch               | v1.14.1 | not installed |
| 219   | GoSquared Analytics      | gosquared                  | v0.1.6  | not installed |
| 220   | TinyMCE Editor Integ...  | tinymce-editor             | v1.1.6  | not installed |
| 221   | JSComments               | jscomments                 | v2.1.4  | not installed |
| 222   | Shortcode Core           | shortcode-core             | v2.7.0  | not installed |
| 223   | TNT Search               | tntsearch                  | v1.2.1  | not installed |
------------------------------------------------------------------------------------------------

THEMES [ 84 ]
------------------------------------------------------------------------------------------------
| Count | Name                     | Slug                       | Version | Installed     |
===========================================================================================
| 1     | Foundation               | foundation                 | v1.0.0  | not installed |
| 2     | Tessellate               | tessellate                 | v1.0.1  | not installed |
| 3     | Sidewalk                 | sidewalk                   | v0.0.5  | not installed |
| 4     | Identity                 | identity                   | v1.1.0  | not installed |
| 5     | Lens                     | lens                       | v1.1.0  | not installed |
| 6     | Materialize              | materialize                | v1.3.8  | not installed |
| 7     | Minimalist               | minimalist                 | v1.0.0  | not installed |
| 8     | Grayscale                | grayscale                  | v1.0.0  | not installed |
| 9     | Prickly Pear             | pricklypear                | v1.0.2  | not installed |
| 10    | Library                  | library                    | v0.4.4  | not installed |
| 11    | Saba                     | saba                       | v1.0.0  | not installed |
| 12    | MilliGrav                | milligrav                  | v0.0.1  | not installed |
| 13    | BootGrav                 | bootgrav                   | v0.3    | not installed |
| 14    | Bones Vanilla            | bones-vanilla              | v0.5.2  | not installed |
| 15    | Bones                    | bones                      | v0.5.2  | not installed |
| 16    | Tintamarre               | tintamarre                 | v1.0.0  | not installed |
| 17    | Solid State              | solid-state                | v1.0.2  | not installed |
| 18    | Alpha                    | alpha                      | v2.0.2  | not installed |
| 19    | Parallelism              | parallelism                | v2.0.2  | not installed |
| 20    | Future                   | future                     | v1.0.7  | not installed |
| 21    | Cardstack                | cardstack                  | v1.1.3  | not installed |
| 22    | Gravi-k                  | gravi-k                    | v0.0.1  | not installed |
| 23    | Nucleare                 | nucleare                   | v1.1.5  | not installed |
| 24    | Purity                   | purity                     | v0.5.5  | not installed |
| 25    | Agency                   | agency                     | v1.4.0  | not installed |
| 26    | Notepad                  | notepad                    | v1.3.0  | not installed |
| 27    | Lingonberry              | lingonberry                | v1.1.0  | not installed |
| 28    | HPSTR                    | hpstr                      | v1.5.0  | not installed |
| 29    | Mediator                 | mediator                   | v1.3.0  | not installed |
| 30    | Cacti                    | cacti                      | v1.3.0  | not installed |
| 31    | Woo                      | woo                        | v1.2.0  | not installed |
| 32    | Striped                  | striped                    | v2.2.0  | not installed |
| 33    | Resume                   | resume                     | v1.2.0  | not installed |
| 34    | Photographer             | photographer               | v1.4.0  | not installed |
| 35    | Halcyon                  | halcyon                    | v1.1.0  | not installed |
| 36    | Ceevee                   | ceevee                     | v1.3.0  | not installed |
| 37    | Aerial                   | aerial                     | v2.0.4  | not installed |
| 38    | Gravstrap Theme          | gravstrap-theme            | v1.2.0  | not installed |
| 39    | Multiverse               | multiverse                 | v1.0.0  | not installed |
| 40    | Boxify                   | boxify                     | v1.1.1  | not installed |
| 41    | TwentyFifteen            | twentyfifteen              | v1.2.0  | not installed |
| 42    | Twenty                   | twenty                     | v1.6.0  | not installed |
| 43    | Appi                     | appi                       | v1.0.0  | not installed |
| 44    | Pinpress                 | pinpress                   | v1.3.0  | not installed |
| 45    | Bootstrap                | bootstrap                  | v1.6.0  | not installed |
| 46    | Deliver                  | deliver                    | v1.6.2  | not installed |
| 47    | Saturn                   | saturn                     | v1.6.0  | not installed |
| 48    | Receptar                 | receptar                   | v1.3.0  | not installed |
| 49    | Landio                   | landio                     | v1.3.0  | not installed |
| 50    | MDUX                     | mdux                       | v0.9.8  | not installed |
| 51    | Cascade                  | cascade                    | v0.1.8  | not installed |
| 52    | Machine                  | machine                    | v2.0.6  | not installed |
| 53    | Masonry                  | masonry                    | v1.5.0  | not installed |
| 54    | Afterburner2             | afterburner2               | v1.7.1  | not installed |
| 55    | Gateway                  | gateway                    | v1.3.1  | not installed |
| 56    | SoraArticle              | soraarticle                | v1.4.1  | not installed |
| 57    | Material Lite            | material-lite              | v0.1.7  | not installed |
| 58    | Developer                | developer                  | v2.2.0  | not installed |
| 59    | Tikva                    | tikva                      | v0.1.7  | not installed |
| 60    | Flaten                   | flaten                     | v2.0.0  | not installed |
| 61    | Knowledge Base           | knowledge-base             | v1.3.3  | not installed |
| 62    | zSimplex                 | zsimplex                   | v2.2.1  | not installed |
| 63    | X-Corporation            | x-corporation              | v1.1.2  | not installed |
| 64    | Medium                   | medium                     | v2.0.5  | not installed |
| 65    | UIKit                    | uikit                      | v1.1.0  | not installed |
| 66    | Learn2                   | learn2                     | v1.7.0  | not installed |
| 67    | Hyde                     | hyde                       | v0.1.1  | not installed |
| 68    | Gravnd Zero              | gravnd-zero                | v0.1.0  | not installed |
| 69    | Antimatter               | antimatter                 | v2.1.2  | installed     |
| 70    | Generallax               | generallax                 | v0.1.0  | not installed |
| 71    | Big Picture              | big-picture                | v1.3.0  | not installed |
| 72    | Clean Blog               | clean-blog                 | v1.4.2  | not installed |
| 73    | Hydrogen                 | g5_hydrogen                | v5.4.22 | not installed |
| 74    | Helium                   | g5_helium                  | v5.4.22 | installed     |
| 75    | Simple Dark              | simple-dark                | v0.1.0  | not installed |
| 76    | Quark                    | quark                      | v1.0.0  | not installed |
| 77    | Mache                    | mache                      | v1.0.3  | not installed |
| 78    | PRIME                    | prime                      | v1.0.0  | not installed |
| 79    | pluspunkt                | pluspunkt                  | v1.0.3  | not installed |
| 80    | Haywire                  | haywire                    | v0.3.2  | not installed |
| 81    | Learn2 Git Sync          | learn2-git-sync            | v0.9.81 | not installed |
| 82    | Story                    | story                      | v1      | not installed |
| 83    | Antimatter Open Publ...  | antimatter-open-publishing | v1.2.3  | not installed |
| 84    | Course Hub Bootstrap     | course-hub-bootstrap       | v1.9.97 | not installed |
---------------------------------------------------------------------------------------- 
```

Enter fullscreen mode Exit fullscreen mode