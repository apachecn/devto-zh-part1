# 安装 Drupal Acquia Lightning

> 原文：<https://dev.to/psnebc/install-drupal-acquia-lightning-298n>

```
➜  01 composer create-project acquia/lightning-project
Installing acquia/lightning-project (8.4.0)
  - Installing acquia/lightning-project (8.4.0): Downloading (100%)         
Created project in /var/www/drupal/base/002/01/lightning-project
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 191 installs, 0 updates, 0 removals
  - Installing composer/installers (v1.5.0): Loading from cache
  - Installing cweagans/composer-patches (1.6.4): Loading from cache
No patches supplied.
Gathering patches for dependencies. This might take a minute.
  - Installing symfony/finder (v3.4.3): Loading from cache
  - Installing symfony/yaml (v3.2.14): Loading from cache
  - Installing drupal/console-extend-plugin (0.9.2): Loading from cache
  - Installing composer/semver (1.4.2): Loading from cache
  - Installing oomphinc/composer-installers-extender (v1.1.2): Downloading (100%)         
  - Installing drupal-composer/drupal-scaffold (2.4.0): Loading from cache
  - Installing paragonie/random_compat (v2.0.11): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.6.0): Loading from cache
  - Installing symfony/http-foundation (v3.2.14): Loading from cache
  - Installing psr/simple-cache (1.0.0): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing psr/cache (1.0.1): Loading from cache
  - Installing symfony/cache (v4.0.3): Loading from cache
  - Installing symfony/expression-language (v3.4.3): Loading from cache
  - Installing symfony/dom-crawler (v3.4.3): Loading from cache
  - Installing symfony/css-selector (v2.8.33): Downloading (100%)         
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
  - Installing psr/container (1.0.0): Loading from cache
  - Installing symfony/dependency-injection (v3.2.14): Loading from cache
  - Installing symfony/config (v3.2.14): Loading from cache
  - Installing stecman/symfony-console-completion (0.7.0): Loading from cache
  - Installing drupal/console-en (1.0.1): Downloading (100%)         
  - Installing dflydev/placeholder-resolver (v1.0.2): Loading from cache
  - Installing dflydev/dot-access-data (v1.1.0): Loading from cache
  - Installing dflydev/dot-access-configuration (v1.0.2): Loading from cache
  - Installing drupal/console-core (1.0.1): Downloading (100%)         
  - Installing doctrine/collections (v1.5.0): Loading from cache
  - Installing doctrine/lexer (v1.0.1): Loading from cache
  - Installing doctrine/annotations (v1.6.0): Loading from cache
  - Installing alchemy/zippy (0.4.3): Loading from cache
  - Installing drupal/console (1.0.1): Downloading (100%)         
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
  - Installing doctrine/inflector (v1.3.0): Downloading (100%)         
  - Installing doctrine/cache (v1.7.1): Loading from cache
  - Installing doctrine/common (v2.8.1): Loading from cache
  - Installing asm89/stack-cors (1.2.0): Loading from cache
  - Installing drupal/core (8.4.4): Loading from cache
  - Applying patches for drupal/core
    https://www.drupal.org/files/issues/1356276-408--8.4.4.patch (1356276 - Allow profiles to provide a base/parent profile and load them in the correct order)
    https://www.drupal.org/files/issues/2880374-remove-experimental-warnings-6.patch (2880374 - Experimental modules should not have warnings after being installed)
    https://www.drupal.org/files/issues/2869592-remove-update-warning-7.patch (2869592 - Disabled update module shouldn't produce a status report warning)
    https://www.drupal.org/files/issues/2885441-2.patch (2885441 - EntityReferenceAutocompleteWidget should define its size setting as an integer)
    https://www.drupal.org/files/issues/2883813-27.patch (2883813 - Move File/Image media type into Standard once Media is stable)
    https://www.drupal.org/files/issues/2877383-23.patch (2877383 - Add action support to Media module)
    https://www.drupal.org/files/issues/2862041-229.patch (2862041 - Provide useful Views filters for Content Moderation State fields)
    https://www.drupal.org/files/issues/2815221-69-8.4.x.patch (2815221 - Add quickedit to the latest-revision route)

  - Installing drupal/media_entity_generic (1.0.0-alpha1): Downloading (100%)         
  - Installing drupal/wbm2cm (dev-2.x 6300a32): Cloning 6300a32370 from cache
  - Installing sebastian/version (1.0.6): Loading from cache
  - Installing sebastian/recursion-context (1.0.5): Loading from cache
  - Installing sebastian/global-state (1.1.1): Loading from cache
  - Installing sebastian/exporter (1.2.2): Loading from cache
  - Installing sebastian/environment (1.3.8): Loading from cache
  - Installing sebastian/diff (1.4.3): Loading from cache
  - Installing sebastian/comparator (1.2.4): Loading from cache
  - Installing doctrine/instantiator (1.1.0): Loading from cache
  - Installing phpunit/php-text-template (1.2.1): Loading from cache
  - Installing phpunit/phpunit-mock-objects (2.3.8): Loading from cache
  - Installing phpunit/php-timer (1.0.9): Loading from cache
  - Installing phpunit/php-token-stream (1.4.12): Loading from cache
  - Installing phpunit/php-file-iterator (1.4.5): Loading from cache
  - Installing phpunit/php-code-coverage (2.2.4): Loading from cache
  - Installing mikey179/vfsstream (v1.6.5): Loading from cache
  - Installing symfony/browser-kit (v3.4.3): Loading from cache
  - Installing fabpot/goutte (v3.2.2): Loading from cache
  - Installing behat/mink (v1.7.1): Loading from cache
  - Installing behat/mink-browserkit-driver (v1.3.2): Loading from cache
  - Installing behat/mink-goutte-driver (v1.2.1): Loading from cache
  - Installing phpdocumentor/reflection-common (1.0.1): Loading from cache
  - Installing phpdocumentor/type-resolver (0.4.0): Downloading (100%)         
  - Installing phpdocumentor/reflection-docblock (4.2.0): Downloading (100%)         
  - Installing phpspec/prophecy (1.7.3): Loading from cache
  - Installing drupal/entity (1.0.0-beta1): Loading from cache
  - Installing sunra/php-simple-html-dom-parser (v1.5.2): Loading from cache
  - Installing ezyang/htmlpurifier (v4.9.3): Loading from cache
  - Installing caxy/php-htmldiff (v0.1.6): Downloading (100%)         
  - Installing mkalkbrenner/php-htmldiff-advanced (0.0.8): Loading from cache
  - Installing drupal/diff (1.0.0-rc1): Loading from cache
  - Installing drupal/workbench_moderation (1.2.0): Downloading (100%)         
  - Applying patches for drupal/workbench_moderation
    https://www.drupal.org/files/issues/2668006-2.patch (2668006 - Provide logical access control for when IPE should be applied to moderated nodes)
    https://www.drupal.org/files/issues/2847078-6.patch (2847078 - Revision tracker's dynamic table creation can break Views)
    https://www.drupal.org/files/issues/workbench_moderation-quickedit-support-2749503-6.patch (2749503 - Add basic support for Quick Edit)

  - Installing drupal/inline_entity_form (1.0.0-beta1): Loading from cache
  - Installing drupal/scheduled_updates (1.0.0-alpha6): Downloading (100%)         
  - Installing drupal/search_api (1.6.0): Loading from cache
  - Installing drupal/token (1.1.0): Loading from cache
  - Installing drupal/contact_storage (1.0.0-beta9): Downloading (100%)         
  - Installing drupal/ctools (3.0.0): Loading from cache
  - Installing drupal/pathauto (1.0.0): Loading from cache
  - Installing drupal/metatag (1.4.0): Loading from cache
  - Installing drupal/config_update (1.5.0): Loading from cache
  - Installing drupal/features (3.5.0): Loading from cache
  - Installing drupal/acquia_connector (1.14.0): Downloading (100%)         
  - Installing drupal/lightning_core (1.0.0-alpha3): Downloading (100%)         
  - Installing drupal/lightning_workflow (1.0.0-alpha2): Downloading (100%)         
  - Installing drupal/media_entity (2.0.0-alpha2): Downloading (100%)         
  - Applying patches for drupal/media_entity
    https://www.drupal.org/files/issues/2918172-5.patch (2918172 - Media Entity upgrade -> core fails on absent column revision_uid)

  - Installing drupal/media_entity_image (dev-1.x 3264d04): Cloning 3264d04a39 from cache
  - Installing drupal/media_entity_document (dev-1.x b98ec72): Cloning b98ec72e30 from cache
  - Installing bower-asset/cropper (v2.3.4): Downloading (100%)         
  - Installing drupal/crop (2.0.0-beta1): Downloading (100%)         
  - Applying patches for drupal/crop
    https://www.drupal.org/files/issues/2918441-beta1-packaged-info-file.patch (Make Crop API compatible with core Media)

  - Installing drupal/image_widget_crop (2.1.0): Downloading (100%)         
  - Installing bower-asset/dropzone (v5.1.1): Downloading (100%)         
  - Installing drupal/dropzonejs (2.0.0-alpha3): Downloading (100%)         
  - Installing drupal/video_embed_field (2.0.0-alpha2): Downloading (100%)         
  - Installing j7mbo/twitter-api-php (1.0.6): Downloading (100%)         
  - Installing drupal/media_entity_twitter (2.0.0-alpha2): Downloading (100%)         
  - Installing drupal/media_entity_instagram (2.0.0-alpha1): Downloading (100%)         
  - Applying patches for drupal/media_entity_instagram
    https://www.drupal.org/files/issues/2917454-2.patch (2917454 - Field formatter test is broken)

  - Installing drupal/embed (1.0.0): Loading from cache
  - Installing drupal/views_infinite_scroll (1.5.0): Loading from cache
  - Installing drupal/entity_embed (1.0.0-beta2): Loading from cache
  - Applying patches for drupal/entity_embed
    https://www.drupal.org/files/issues/2832504-2.patch (2832504 - Send the CKEditor instance ID to the embed.preview route)

  - Installing drupal/entity_browser (2.0.0-alpha2): Downloading (100%)         
  - Applying patches for drupal/entity_browser
    https://www.drupal.org/files/issues/entity-browser-view-context-2865928-14.patch (2865928 - The View widget should filter based on field settings)
    https://www.drupal.org/files/issues/2877751-17.patch (2877751 - Inform users how many items they can add to a field that uses an entity browser)

  - Installing drupal/lightning_media (1.0.0-alpha2): Downloading (100%)         
  - Installing drupal/entity_block (1.0.0-alpha2): Downloading (100%)         
  - Applying patches for drupal/entity_block
    https://www.drupal.org/files/issues/entity_block-view-builder-class.patch (2844068 - Create derivatives for any viewable entity.)
    https://www.drupal.org/files/issues/2846004-2.patch (2846004 - Entity Block does not perform any access control)

  - Installing drupal/panels (4.2.0): Loading from cache
  - Applying patches for drupal/panels
    https://www.drupal.org/files/issues/panels-ipe-2878684-3.patch (2878684 - Use String.match to correlate regions when switching Layouts in Panels IPE)
    https://www.drupal.org/files/issues/panels-ipe-propogate-errors-2825034-5.patch (2825034 - Form error messages do not appear in IPE)
    https://www.drupal.org/files/issues/2908897-2.patch (2908897 - CategoryView leaves AJAX request marked active)
    https://www.drupal.org/files/issues/2913353-2.patch (2913353 - IPE attaches behaviors a bit too quickly)
    https://www.drupal.org/files/issues/panels-ipe-date-field-2825028-10.patch (2825028 - AJAX error creating and placing blocks with date fields in IPE)

  - Installing drupal/panelizer (4.0.0): Loading from cache
  - Applying patches for drupal/panelizer
    https://www.drupal.org/files/issues/2908917-2.patch (2908917 - SaveTabView blindly assumes a Revert tab exists)

  - Installing drupal/lightning_layout (1.0.0-alpha2): Downloading (100%)         
  - Installing defuse/php-encryption (v2.1.0): Loading from cache
  - Installing lcobucci/jwt (3.2.2): Downloading (100%)         
  - Installing league/event (2.1.2): Downloading (100%)         
  - Installing league/oauth2-server (6.1.1): Downloading (100%)         
  - Installing drupal/consumers (1.0.0-beta1): Downloading (100%)         
  - Installing drupal/simple_oauth (3.3.0): Downloading (100%)         
  - Applying patches for drupal/simple_oauth
    https://www.drupal.org/files/issues/2925369-2.patch (2925369 - TokenAuthUser does not fulfill RevisionableInterface, causes fatals)

  - Installing drupal/schemata (1.0.0-alpha2): Downloading (100%)         
  - Installing drupal/openapi (1.0.0-alpha1): Downloading (100%)         
  - Installing drupal/jsonapi (1.4.0): Downloading (100%)         
  - Installing drupal/lightning_api (1.0.0-alpha2): Downloading (100%)         
  - Installing acquia/lightning (3.0.1): Downloading (100%)         
  - Installing drupal/drupal-driver (v1.3.2): Downloading (100%)         
  - Installing container-interop/container-interop (1.2.0): Downloading (100%)         
  - Installing behat/transliterator (v1.2.0): Loading from cache
  - Installing behat/gherkin (v4.5.1): Downloading (100%)         
  - Installing behat/behat (v3.4.3): Downloading (100%)         
  - Installing behat/mink-extension (2.3.0): Downloading (100%)         
  - Installing instaclick/php-webdriver (1.4.5): Downloading (100%)         
  - Installing behat/mink-selenium2-driver (v1.3.1): Downloading (100%)         
  - Installing drupal/drupal-extension (v3.3.1): Downloading (100%)         
  - Installing jcalderonzumba/gastonjs (v1.0.3): Loading from cache
  - Installing squizlabs/php_codesniffer (2.9.1): Downloading (100%)         
  - Installing drupal/coder (8.2.12): Cloning 984c54a7b1 from cache
  - Installing phpunit/phpunit (4.8.36): Loading from cache
  - Installing league/container (2.4.1): Downloading (100%)         
  - Installing grasmash/yaml-expander (1.4.0): Downloading (100%)         
  - Installing consolidation/output-formatters (3.1.13): Loading from cache
  - Installing consolidation/log (1.0.5): Downloading (100%)         
  - Installing consolidation/annotated-command (2.8.2): Loading from cache
  - Installing grasmash/expander (1.0.0): Downloading (100%)         
  - Installing consolidation/config (1.0.9): Downloading (100%)         
  - Installing consolidation/robo (1.2.1): Downloading (100%)         
  - Installing chi-teck/drupal-code-generator (1.22.0): Downloading (100%)         
  - Installing drush/drush (9.0.0-rc2): Downloading (100%)         
  - Installing se/selenium-server-standalone (v2.53.1): Downloading (100%)         
  - Installing seld/phar-utils (1.0.1): Downloading (100%)         
  - Installing seld/jsonlint (1.7.0): Downloading (100%)         
  - Installing seld/cli-prompt (1.0.3): Loading from cache
  - Installing justinrainbow/json-schema (5.2.6): Downloading (100%)         
  - Installing composer/spdx-licenses (1.2.0): Downloading (100%)         
  - Installing composer/ca-bundle (1.1.0): Loading from cache
  - Installing composer/composer (1.6.2): Downloading (100%)         
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
symfony/class-loader suggests installing symfony/polyfill-apcu (For using ApcClassLoader on HHVM)
easyrdf/easyrdf suggests installing ml/json-ld (~1.0)
doctrine/cache suggests installing alcaeus/mongo-php-adapter (Required to use legacy MongoDB driver)
sebastian/global-state suggests installing ext-uopz (*)
phpunit/phpunit-mock-objects suggests installing ext-soap (*)
phpunit/php-code-coverage suggests installing ext-xdebug (>=2.2.1)
behat/mink suggests installing behat/mink-zombie-driver (fast and JS-enabled headless driver for any app (requires node.js))
drupal/dropzonejs suggests installing enyo/dropzone (Required to user drupal/dropzonejs. Dropzone is an easy to use drag'n'drop library.)
lcobucci/jwt suggests installing mdanter/ecc (Required to use Elliptic Curves based algorithms.)
behat/behat suggests installing behat/symfony2-extension (for integration with Symfony2 web framework)
behat/behat suggests installing behat/yii-extension (for integration with Yii web framework)
phpunit/phpunit suggests installing phpunit/php-invoker (~1.1)
consolidation/robo suggests installing pear/archive_tar (Allows tar archives to be created and extracted in taskPack and taskExtract, respectively.)
consolidation/robo suggests installing henrikbjorn/lurker (For monitoring filesystem changes in taskWatch)
consolidation/robo suggests installing patchwork/jsqueeze (For minifying JS files in taskMinify)
consolidation/robo suggests installing natxet/CssMin (For minifying CSS files in taskMinify)
Writing lock file
Generating autoload files
Creating packages services cache file: 
/var/www/drupal/base/002/01/lightning-project/vendor/drupal/console/extend.console.uninstall.services.yml
Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)DownloadinDownloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)> DrupalComposer\DrupalScaffold\Plugin::scaffold
Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)DownloadinDownloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)%                                                               ➜  01 
```

Enter fullscreen mode Exit fullscreen mode