# 安装 Drupal Thunder 发行版

> 原文：<https://dev.to/psnebc/install-drupal-thunder-distribution-4faa>

```
➜  01 composer create-project burdamagazinorg/thunder-project thunder
Installing burdamagazinorg/thunder-project (8.2.1)
  - Installing burdamagazinorg/thunder-project (8.2.1): Downloading (100%)         
Created project in thunder
> DrupalProject\composer\ScriptHandler::checkComposerVersion
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 153 installs, 0 updates, 0 removals
  - Installing composer/installers (v1.5.0): Loading from cache
  - Installing cweagans/composer-patches (1.6.4): Loading from cache
No patches supplied.
Gathering patches for dependencies. This might take a minute.
  - Installing symfony/finder (v3.4.3): Loading from cache
  - Installing symfony/yaml (v3.2.14): Loading from cache
  - Installing drupal/console-extend-plugin (0.9.2): Loading from cache
  - Installing composer/semver (1.4.2): Loading from cache
  - Installing drupal-composer/drupal-scaffold (2.4.0): Loading from cache
  - Installing oomphinc/composer-installers-extender (v1.1.2): Loading from cache
  - Installing paragonie/random_compat (v2.0.11): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.6.0): Loading from cache
  - Installing symfony/http-foundation (v3.2.14): Loading from cache
  - Installing psr/simple-cache (1.0.0): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing psr/cache (1.0.1): Loading from cache
  - Installing symfony/cache (v4.0.3): Loading from cache
  - Installing symfony/expression-language (v3.4.3): Loading from cache
  - Installing symfony/dom-crawler (v3.4.3): Loading from cache
  - Installing symfony/css-selector (v3.4.3): Loading from cache
  - Installing jakub-onderka/php-console-color (0.1): Loading from cache
  - Installing jakub-onderka/php-console-highlighter (v0.3.2): Loading from cache
  - Installing dnoegel/php-xdg-base-dir (0.1): Loading from cache
  - Installing nikic/php-parser (v3.1.3): Loading from cache
  - Installing symfony/var-dumper (v3.4.3): Loading from cache
  - Installing symfony/debug (v3.4.3): Loading from cache
  - Installing symfony/console (v3.2.14): Loading from cache
  - Installing psy/psysh (v0.8.17): Loading from cache
  - Installing psr/http-message (1.0.1): Loading from cache
  - Installing guzzlehttp/psr7 (1.4.2): Loading from cache
  - Installing guzzlehttp/promises (v1.3.1): Loading from cache
  - Installing guzzlehttp/guzzle (6.3.0): Loading from cache
  - Installing gabordemooij/redbean (v4.3.4): Loading from cache
  - Installing vlucas/phpdotenv (v2.4.0): Loading from cache
  - Installing drupal/console-dotenv (0.3.0): Loading from cache
  - Installing webmozart/assert (1.2.0): Loading from cache
  - Installing webmozart/path-util (2.3.0): Loading from cache
  - Installing webflo/drupal-finder (1.1.0): Loading from cache
  - Installing twig/twig (v1.35.0): Loading from cache
  - Installing symfony/translation (v3.2.14): Loading from cache
  - Installing symfony/process (v3.2.14): Loading from cache
  - Installing symfony/filesystem (v3.4.3): Loading from cache
  - Installing symfony/event-dispatcher (v3.2.14): Loading from cache
  - Installing symfony/dependency-injection (v3.2.14): Loading from cache
  - Installing symfony/config (v3.2.14): Loading from cache
  - Installing stecman/symfony-console-completion (0.7.0): Loading from cache
  - Installing drupal/console-en (1.0.2): Downloading (failed)       
Downloading (100%)         
  - Installing dflydev/placeholder-resolver (v1.0.2): Loading from cache
  - Installing dflydev/dot-access-data (v1.1.0): Loading from cache
  - Installing dflydev/dot-access-configuration (v1.0.2): Loading from cache
  - Installing drupal/console-core (1.0.2): Downloading (100%)         
  - Installing doctrine/collections (v1.5.0): Loading from cache
  - Installing doctrine/lexer (v1.0.1): Loading from cache
  - Installing doctrine/annotations (v1.6.0): Loading from cache
  - Installing alchemy/zippy (0.4.3): Loading from cache
  - Installing drupal/console (1.0.2): Downloading (100%)         
  - Installing zendframework/zend-stdlib (3.1.0): Loading from cache
  - Installing zendframework/zend-escaper (2.5.2): Loading from cache
  - Installing zendframework/zend-feed (2.9.0): Loading from cache
  - Installing zendframework/zend-diactoros (1.7.0): Loading from cache
  - Installing symfony/validator (v3.2.14): Loading from cache
  - Installing symfony/serializer (v3.2.14): Loading from cache
  - Installing symfony/routing (v3.2.14): Loading from cache
  - Installing symfony/psr-http-message-bridge (v1.0.2): Loading from cache
  - Installing symfony/polyfill-iconv (v1.6.0): Loading from cache
  - Installing symfony/http-kernel (v3.2.14): Loading from cache
  - Installing symfony/class-loader (v3.2.14): Loading from cache
  - Installing symfony-cmf/routing (1.4.1): Loading from cache
  - Installing stack/builder (v1.0.5): Loading from cache
  - Installing masterminds/html5 (2.3.0): Loading from cache
  - Installing egulias/email-validator (1.2.14): Loading from cache
  - Installing easyrdf/easyrdf (0.9.1): Loading from cache
  - Installing doctrine/inflector (v1.3.0): Loading from cache
  - Installing doctrine/cache (v1.7.1): Loading from cache
  - Installing doctrine/common (v2.8.1): Loading from cache
  - Installing asm89/stack-cors (1.2.0): Loading from cache
  - Installing drupal/core (8.4.3): Downloading (100%)         
  - Applying patches for drupal/core
    https://www.drupal.org/files/issues/2930715-2-4.patch (Recursive rebuild caused by installing admin_toolbar_tools module)

  - Installing sebastian/diff (1.4.3): Loading from cache
  - Installing phpdocumentor/reflection-docblock (2.0.5): Loading from cache
  - Installing drupal/views_load_more (dev-1.x 178d321): Cloning 178d321952 from cache
  - Installing drupal/entity_reference_revisions (1.4.0): Loading from cache
  - Installing drupal/paragraphs (1.1.0): Downloading (100%)         
  - Applying patches for drupal/paragraphs
    https://www.drupal.org/files/issues/2899917_6.patch (create a NEW paragraph 'in place' between existing ones)
    https://www.drupal.org/files/issues/build_failing_schema-2881459-7.patch (Schema errors)

  - Installing drupal/entity (1.0.0-beta1): Loading from cache
  - Installing drupal/media_entity (1.6.0): Downloading (100%)         
  - Applying patches for drupal/media_entity
    https://www.drupal.org/files/issues/2813685-21.patch (Make the label form element non-required in the entity form)

  - Installing drupal/media_entity_pinterest (1.0.0-beta2): Downloading (100%)         
  - Applying patches for drupal/media_entity_pinterest
    https://www.drupal.org/files/issues/media_name_always_h-2837977-8.patch (Media name always 'h')

  - Installing drupal/libraries (dev-3.x 061ead0): Cloning 061ead081c from cache
  - Installing drupal/fb_instant_articles (dev-1.x 2a5210f): Cloning 2a5210fec6 from cache
  - Installing drupal/entity_browser (1.4.0): Loading from cache
  - Applying patches for drupal/entity_browser
    https://www.drupal.org/files/issues/2858438_6.patch (Provide inline entity form FieldWidgetDisplay)

  - Installing sunra/php-simple-html-dom-parser (v1.5.2): Loading from cache
  - Installing ezyang/htmlpurifier (v4.9.3): Loading from cache
  - Installing caxy/php-htmldiff (v0.1.6): Loading from cache
  - Installing mkalkbrenner/php-htmldiff-advanced (0.0.8): Loading from cache
  - Installing drupal/diff (1.0.0-rc1): Loading from cache
  - Applying patches for drupal/diff
    https://www.drupal.org/files/issues/back_button_for-2853193-4.patch (Back button for comparison page)

  - Installing drupal/amptheme (1.0.0): Downloading (100%)         
  - Applying patches for drupal/amptheme
    https://www.drupal.org/files/issues/amptheme-do_not_use_the_network_for_loading_logo_information-2753089-7.patch (Don't use network for loading logo information)

  - Installing sabberworm/php-css-parser (8.1.0): Downloading (100%)         
  - Installing querypath/querypath (3.0.5): Downloading (100%)         
  - Installing marc1706/fast-image-size (v1.1.4): Downloading (100%)         
  - Installing lullabot/amp (1.1.2): Downloading (100%)         
  - Installing drupal/token (1.1.0): Loading from cache
  - Installing drupal/amp (1.0.0): Downloading (100%)         
  - Applying patches for drupal/amp
    https://www.drupal.org/files/issues/missing_schema_for-2878769-3.patch (Missing schema)

  - Installing bower-asset/exif-js (v2.3.0): Downloading (100%)         
  - Installing pear/console_table (v1.3.0): Loading from cache
  - Installing consolidation/output-formatters (3.1.13): Loading from cache
  - Installing consolidation/annotated-command (2.8.2): Loading from cache
  - Installing drush/drush (8.1.15): Loading from cache
  - Installing bower-asset/blazy (1.8.2): Downloading (100%)         
  - Installing bower-asset/dropzone (v5.1.1): Loading from cache
  - Installing bower-asset/shariff (1.26.2): Downloading (100%)         
  - Installing valiton/harbourmaster (8.1.1-beta6): Downloading (100%)         
  - Installing drupal/video_embed_field (1.5.0): Loading from cache
  - Installing drupal/thunder_admin (2.0.0-beta7): Downloading (100%)         
  - Installing drupal/blazy (1.0.0-rc2): Loading from cache
  - Installing drupal/slick (1.0.0): Loading from cache
  - Installing drupal/media_entity_image (1.2.0): Loading from cache
  - Installing drupal/slick_media (1.0.0): Loading from cache
  - Installing drupal/simple_sitemap (2.11.0): Loading from cache
  - Installing drupal/shariff (1.3.0): Downloading (100%)         
  - Installing drupal/scheduler (1.0.0): Downloading (100%)         
  - Installing drupal/riddle_marketplace (2.0.0-beta1): Downloading (100%)         
  - Installing drupal/responsive_preview (1.0.0-alpha7): Loading from cache
  - Installing drupal/ctools (3.0.0): Loading from cache
  - Installing drupal/pathauto (1.0.0): Loading from cache
  - Installing drupal/nexx_integration (1.10.0): Downloading (100%)         
  - Installing drupal/metatag (1.4.0): Loading from cache
  - Installing drupal/media_expire (1.0.0-alpha2): Downloading (100%)         
  - Installing j7mbo/twitter-api-php (1.0.6): Loading from cache
  - Installing drupal/media_entity_twitter (1.3.0): Downloading (100%)         
  - Installing drupal/media_entity_slideshow (1.2.0): Downloading (100%)         
  - Installing drupal/media_entity_instagram (1.4.0): Downloading (100%)         
  - Installing drupal/simple_gmap (1.4.0): Downloading (100%)         
  - Installing pusher/pusher-php-server (v3.0.1): Downloading (100%)         
  - Installing drupal/liveblog (1.2.0-alpha14): Downloading (100%)         
  - Installing drupal/linkit (4.3.0): Loading from cache
  - Installing drupal/breakpoint_js_settings (1.0.0-rc1): Downloading (100%)         
  - Installing drupal/ivw_integration (1.3.0): Downloading (100%)         
  - Installing drupal/inline_entity_form (1.0.0-beta1): Loading from cache
  - Installing drupal/google_analytics (2.2.0): Loading from cache
  - Installing drupal/crop (1.3.0): Loading from cache
  - Installing drupal/focal_point (1.0.0-beta5): Loading from cache
  - Installing drupal/empty_fields (1.0.0-alpha1): Downloading (100%)         
  - Installing drupal/default_content (1.0.0-alpha7): Loading from cache
  - Installing drupal/content_lock (1.0.0-alpha4): Loading from cache
  - Installing drupal/config_update (1.5.0): Loading from cache
  - Installing drupal/checklistapi (1.7.0): Downloading (100%)         
  - Installing drupal/better_normalizers (1.0.0-beta3): Downloading (100%)         
  - Installing drupal/adsense (1.0.0): Downloading (100%)         
  - Installing drupal/admin_toolbar (1.22.0): Loading from cache
  - Installing drupal/access_unpublished (1.0.0-alpha3): Downloading (100%)         
  - Installing burdamagazinorg/infinite_theme (1.0-beta8): Downloading (100%)         
  - Installing burdamagazinorg/infinite_module (1.0-beta7): Downloading (100%)         
  - Installing drupal/field_group (1.0.0): Downloading (100%)         
  - Installing drupal/dropzonejs (1.0.0-alpha8): Loading from cache
  - Installing bower-asset/slick-carousel (v1.8.1): Downloading (100%)         
  - Installing burdamagazinorg/thunder (8.2.12): Downloading (100%)         
paragonie/random_compat suggests installing ext-libsodium (Provides a modern crypto API that can be used to generate random bytes.)
symfony/var-dumper suggests installing ext-intl (To show region name in time zone dump)
symfony/var-dumper suggests installing ext-symfony_debug ()
psy/psysh suggests installing ext-pdo-sqlite (The doc command requires SQLite to work.)
psy/psysh suggests installing hoa/console (A pure PHP readline implementation. You'll want this if your PHP install doesn't already support readline or libedit.)
symfony/dependency-injection suggests installing symfony/proxy-manager-bridge (Generate service proxies to lazy load them)
alchemy/zippy suggests installing guzzle/guzzle (To use the GuzzleTeleporter with Guzzle 3)
zendframework/zend-feed suggests installing zendframework/zend-cache (Zend\Cache component, for optionally caching feeds between requests)
zendframework/zend-feed suggests installing zendframework/zend-db (Zend\Db component, for use with PubSubHubbub)
zendframework/zend-feed suggests installing zendframework/zend-http (Zend\Http for PubSubHubbub, and optionally for use with Zend\Feed\Reader)
zendframework/zend-feed suggests installing zendframework/zend-servicemanager (Zend\ServiceManager component, for easily extending ExtensionManager implementations)
zendframework/zend-feed suggests installing zendframework/zend-validator (Zend\Validator component, for validating email addresses used in Atom feeds and entries when using the Writer subcomponent)
symfony/validator suggests installing symfony/intl ()
symfony/serializer suggests installing symfony/property-info (To deserialize relations.)
symfony/serializer suggests installing symfony/property-access (For using the ObjectNormalizer.)
symfony/http-kernel suggests installing symfony/browser-kit ()
symfony/class-loader suggests installing symfony/polyfill-apcu (For using ApcClassLoader on HHVM)
easyrdf/easyrdf suggests installing ml/json-ld (~1.0)
doctrine/cache suggests installing alcaeus/mongo-php-adapter (Required to use legacy MongoDB driver)
phpdocumentor/reflection-docblock suggests installing dflydev/markdown (~1.0)
phpdocumentor/reflection-docblock suggests installing erusev/parsedown (~1.0)
pear/console_table suggests installing pear/Console_Color2 (>=0.1.2)
drush/drush suggests installing drush/config-extra (Provides configuration workflow commands, such as config-merge.)
drupal/dropzonejs suggests installing enyo/dropzone (Required to user drupal/dropzonejs. Dropzone is an easy to use drag'n'drop library.)
Writing lock file
Generating autoload files
Creating packages services cache file: 
/var/www/drupal/base/004/01/thunder/vendor/drupal/console/extend.console.uninstall.services.yml
Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)DownloadinDownloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)> DrupalProject\composer\ScriptHandler::createRequiredFiles
Create a sites/default/settings.php file with chmod 0666
Create a sites/default/files directory with chmod 0777 
```

Enter fullscreen mode Exit fullscreen mode