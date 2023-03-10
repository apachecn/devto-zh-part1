# 安装基于 var 的 drupal 发行版

> 原文：<https://dev.to/psnebc/install-varbase-drupal-distribution-5f0i>

```
➜ 02 composer create-project vardot/varbase-project:8.4.x-dev drupalsite --stability dev --no-interaction
Installing vardot/varbase-project (8.4.x-dev 99d698b2a461edaf7c721967590e626976f8842d)
  - Installing vardot/varbase-project (8.4.x-dev 99d698b): Cloning 99d698b2a4 from cache
Created project in drupalsite
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 192 installs, 0 updates, 0 removals
  - Installing cweagans/composer-patches (1.6.4): Loading from cache
No patches supplied.
Gathering patches for dependencies. This might take a minute.
  - Installing drupal/drupal-library-installer-plugin (0.3): Loading from cache
  - Installing composer/installers (v1.4.0): Loading from cache
  - Installing composer/semver (1.4.2): Loading from cache
  - Installing drupal-composer/drupal-scaffold (2.4.0): Loading from cache
  - Installing webflo/drupal-finder (1.0.0): Loading from cache
  - Installing webmozart/assert (1.2.0): Loading from cache
  - Installing webmozart/path-util (2.3.0): Loading from cache
  - Installing zendframework/zend-stdlib (3.1.0): Loading from cache
  - Installing zendframework/zend-escaper (2.5.2): Loading from cache
  - Installing zendframework/zend-feed (2.9.0): Loading from cache
  - Installing psr/http-message (1.0.1): Loading from cache
  - Installing zendframework/zend-diactoros (1.7.0): Loading from cache
  - Installing twig/twig (v1.35.0): Loading from cache
  - Installing symfony/yaml (v3.2.14): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.6.0): Loading from cache
  - Installing symfony/translation (v3.2.14): Loading from cache
  - Installing symfony/validator (v3.2.14): Loading from cache
  - Installing symfony/serializer (v3.2.14): Loading from cache
  - Installing symfony/routing (v3.2.14): Loading from cache
  - Installing paragonie/random_compat (v2.0.11): Loading from cache
  - Installing symfony/http-foundation (v3.2.14): Loading from cache
  - Installing symfony/psr-http-message-bridge (v1.0.2): Loading from cache
  - Installing symfony/process (v3.2.14): Loading from cache
  - Installing symfony/polyfill-iconv (v1.6.0): Loading from cache
  - Installing symfony/event-dispatcher (v3.2.14): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing symfony/debug (v3.4.3): Loading from cache
  - Installing symfony/http-kernel (v3.2.14): Loading from cache
  - Installing symfony/dependency-injection (v3.2.14): Loading from cache
  - Installing symfony/console (v3.2.14): Loading from cache
  - Installing symfony/class-loader (v3.2.14): Loading from cache
  - Installing symfony-cmf/routing (1.4.1): Loading from cache
  - Installing stack/builder (v1.0.5): Loading from cache
  - Installing masterminds/html5 (2.3.0): Loading from cache
  - Installing guzzlehttp/psr7 (1.4.2): Loading from cache
  - Installing guzzlehttp/promises (v1.3.1): Loading from cache
  - Installing guzzlehttp/guzzle (6.3.0): Loading from cache
  - Installing doctrine/lexer (v1.0.1): Loading from cache
  - Installing egulias/email-validator (1.2.14): Loading from cache
  - Installing easyrdf/easyrdf (0.9.1): Loading from cache
  - Installing doctrine/inflector (v1.2.0): Loading from cache
  - Installing doctrine/collections (v1.5.0): Loading from cache
  - Installing doctrine/cache (v1.7.1): Loading from cache
  - Installing doctrine/annotations (v1.6.0): Loading from cache
  - Installing doctrine/common (v2.8.1): Loading from cache
  - Installing asm89/stack-cors (1.2.0): Loading from cache
  - Installing drupal/core (8.4.4): Loading from cache
  - Applying patches for drupal/core
    https://www.drupal.org/files/issues/1356276-408--8.4.4.patch (Issue #1356276: Allow profiles to provide a base_parent profile and load them in the correct order)
    https://www.drupal.org/files/issues/2720101-43.patch (Issue #2720101 Label (Title) not set for Views block (exposed filters in Block))
    https://www.drupal.org/files/issues/2599228-31.patch (Issue #2599228: Programmatically created translatable content type returns SQL error on content creation)
    https://www.drupal.org/files/issues/patch_empty_fix.patch (Issue #2893892: Fixed Warning Invalid argument supplied for foreach() in Drupal)

  - Installing drupal/entity_reference_revisions (1.4.0): Loading from cache
  - Installing drupal/login_destination (dev-1.x 54be8b8): Cloning 54be8b89fd from cache
  - Installing drupal/link_attributes (1.2.0): Loading from cache
  - Installing drupal/admin_toolbar (1.22.0): Loading from cache
  - Installing drupal/webform (5.0.0-rc1): Loading from cache
  - Installing drupal/search_api (1.6.0): Loading from cache
  - Installing drupal/views_bulk_edit (2.0.0-beta2): Loading from cache
  - Installing drupal/token (1.1.0): Loading from cache
  - Installing drupal/metatag (1.4.0): Loading from cache
  - Installing drupal/viewsreference (2.0.0-alpha3): Loading from cache
  - Installing drupal/paragraphs (1.2.0): Loading from cache
  - Applying patches for drupal/paragraphs
    https://www.drupal.org/files/issues/meta_support-2461695-222.patch (Issue #2461695: Support translatable paragraph entity reference revision field)
    https://www.drupal.org/files/issues/2868155-16.patch (Issue #2868155: Add new hooks to allow easier editing of paragraph forms)
    https://www.drupal.org/files/issues/2877927_29.patch (Issue #2877927: Show add widget between paragraphs on hover)
    https://www.drupal.org/files/issues/2909416-2.patch (Issue #2909416: Hide duplicate action while translating.)
    https://www.drupal.org/files/issues/2924774-4.patch (Issue #2924774: Let Editors add/delete/clone paragraphs When [Editing a translation])

  - Installing drupal/entity (1.0.0-beta1): Loading from cache
  - Installing drupal/media_entity (1.7.0): Loading from cache
  - Installing drupal/media_entity_image (1.2.0): Loading from cache
  - Installing drupal/field_group (3.0.0-beta1): Loading from cache
  - Installing drupal/entity_browser (1.4.0): Loading from cache
  - Installing drupal/varbase_bootstrap_paragraphs (4.0.0-rc1): Loading from cache
  - Installing drupal/redirect (1.0.0): Loading from cache
  - Installing drupal/prlp (1.3.0): Loading from cache
  - Installing drupal/views_bulk_operations (1.0.0): Loading from cache
  - Installing drupal/entity_clone (dev-1.x 9265e35): Cloning 9265e359bd from cache
  - Applying patches for drupal/entity_clone
    https://www.drupal.org/files/issues/2770687_wrong_redirection_for_entity_9.patch (Issue #2770687: Wrong redirection of page for entity clone module)
    https://www.drupal.org/files/issues/entity_clone-event_dispatcher-2800203-5.patch (Issue #2800203: Event dispatcher for clone events)
    https://www.drupal.org/files/issues/entity_clone-attached_entities-2706639-25.patch (Issue #2706639: Support for cloning nodes that have another entities attached)
    https://www.drupal.org/files/issues/entity_clone-abort_clone_causes_fatal_error-2769823-4.patch (Issue #2769823: Getting error after clicking on abort clone button)

  - Installing drupal/ctools (3.0.0): Loading from cache
  - Applying patches for drupal/ctools
    https://www.drupal.org/files/issues/fixblockpager-2820783-27.patch (Issue #2820783: Fix Block.php notices and warnings.)

  - Installing drupal/page_manager (4.0.0-beta2): Loading from cache
  - Applying patches for drupal/page_manager
    https://www.drupal.org/files/issues/page_manager-redirect_location-2624972-30.patch (Issue #2624972: No configuration possible in UI for 301, 303, etc. HTTP responses)

  - Installing drupal/layout_plugin (1.0.0-alpha23): Loading from cache
  - Installing drupal/panels (4.2.0): Loading from cache
  - Installing drupal/total_control (2.0.0-alpha3): Loading from cache
  - Installing drupal/google_analytics_reports (dev-3.x 2b6bb8e): Cloning 2b6bb8efbc from cache
  - Applying patches for drupal/google_analytics_reports
    https://www.drupal.org/files/issues/error_when_saving-2850463-2.patch (Issue #2850463: Error when saving either google_analytics_reports view)
    https://www.drupal.org/files/issues/Fix_message-2860399-5.patch (Issue #2860399: Warning: htmlspecialchars() expects parameter 1 to be string)

  - Installing drupal/charts (3.0.0-alpha7): Loading from cache
  - Installing drupal/varbase_total_control (1.0.0-beta9): Loading from cache
  - Installing drupal/blazy (1.0.0-rc2): Loading from cache
  - Installing drupal/slick (1.0.0): Loading from cache
  - Installing drupal/video_embed_field (1.5.0): Loading from cache
  - Installing drupal/slick_media (1.0.0): Loading from cache
  - Installing drupal/inline_entity_form (1.0.0-beta1): Loading from cache
  - Installing drupal/ds (3.1.0): Loading from cache
  - Installing drupal/dropzonejs (1.0.0-alpha8): Loading from cache
  - Installing drupal/varbase_carousels (4.0.0-beta3): Loading from cache
  - Installing drupal/slick_views (1.0.0-rc2): Loading from cache
  - Installing drupal/rabbit_hole (1.0.0-beta4): Loading from cache
  - Installing drupal/panelizer (4.0.0): Loading from cache
  - Installing drupal/entityqueue (1.0.0-alpha7): Loading from cache
  - Applying patches for drupal/entityqueue
    https://www.drupal.org/files/issues/2145441-32.patch (Issue #2145441: Option to create Entityqueue tab on Entity pages.)
    https://www.drupal.org/files/issues/2825773-23-outside_in_to_settings_tray.patch (Issue #2825773: Make Contextual links work for views using Entityqueue)
    https://www.drupal.org/files/issues/only-users-with-administer-entityqueue-should-be-able-to-delete-entityqueues-2916026-3.patch (Issue #2916026: Only users with [Administer entityqueue] should be able to delete entityqueues)
    https://www.drupal.org/files/issues/only-users-with-administer-entityqueue-should-be-able-edit-configure-entityqueues-2917308-2.patch (Issue #2917308: Only users with [Administer entityqueue] should be able to edit (configure) entityqueues)

  - Installing drupal/block_class (1.0.0-alpha1): Loading from cache
  - Installing drupal/varbase_heroslider_media (4.0.0-beta13): Loading from cache
  - Installing drupal/pathologic (dev-1.x 4f9f3fd): Cloning 4f9f3fdcf1 from cache
  - Installing drupal/linkit (4.3.0): Loading from cache
  - Installing drupal/image_resize_filter (dev-1.x c3f4b23): Cloning c3f4b23b02 from cache
  - Installing drupal/embed (1.0.0): Loading from cache
  - Installing drupal/entity_embed (1.0.0-beta2): Loading from cache
  - Installing drupal/ckeditor_media_embed (1.1.0): Loading from cache
  - Applying patches for drupal/ckeditor_media_embed
    https://www.drupal.org/files/issues/embed_rich_content_in_WYSIWYG-2900313-2.patch (Issue #2900313: Add ability to embed tweets and other rich content in WYSIWYG)

  - Installing drupal/ckeditor_bidi (2.1.0): Loading from cache
  - Installing drupal/anchor_link (1.6.0): Loading from cache
  - Installing drupal/ace_editor (1.0.0): Loading from cache
  - Applying patches for drupal/ace_editor
    https://www.drupal.org/files/issues/2916832-4.patch (Issue #2916832: Add support for calling libraries from the current active profile)

  - Installing drupal/varbase_editor (4.18.0): Loading from cache
  - Installing drupal/simple_sitemap (2.11.0): Loading from cache
  - Installing drupal/entity_browser_enhanced (1.0.0-beta6): Loading from cache
  - Installing drupal/menu_position (dev-1.x d134276): Cloning d134276b4b from cache
  - Installing drupal/bootstrap (3.7.0): Loading from cache
  - Installing drupal/config_update (1.5.0): Loading from cache
  - Installing drupal/config_filter (1.1.0): Loading from cache
  - Installing drupal/drd_agent (3.6.0): Loading from cache
  - Installing drupal/config_perms (1.1.0): Loading from cache
  - Applying patches for drupal/config_perms
    https://www.drupal.org/files/issues/2877329-16.patch (Issue #2877329: When [content_translation, config_translate] module is enabled, And enabled [Custom Permissions], Then we will get  Route custom_perms_select_list_form does not exist)

  - Installing drupal/crop (1.3.0): Loading from cache
  - Installing drupal/default_content (1.0.0-alpha7): Loading from cache
  - Applying patches for drupal/default_content
    https://www.drupal.org/files/issues/2900089-14.patch (Issue #2900089: Invalid translation language error when selected installation language does not match demo content language)

  - Installing drupal/config_ignore (2.1.0): Loading from cache
  - Installing drupal/bootstrap_layouts (5.0.0): Loading from cache
  - Installing drupal/focal_point (1.0.0-beta5): Loading from cache
  - Applying patches for drupal/focal_point
    https://www.drupal.org/files/issues/2915497_3.patch (Issue #2915497: Missing schema for focal_point_scale_and_crop effect)
    https://www.drupal.org/files/issues/2916562-16.patch (Issue #2916562: Broken image preview)

  - Installing lsolesen/pel (0.9.6): Loading from cache
  - Installing phenx/php-font-lib (0.5): Loading from cache
  - Installing drupal/file_mdm (1.1.0): Loading from cache
  - Installing drupal/imagemagick (2.2.0): Loading from cache
  - Installing drupal/views_bootstrap (dev-3.x ec4e787): Cloning ec4e787231 from cache
  - Installing drupal/responsive_preview (1.0.0-alpha7): Loading from cache
  - Installing drupal/devel (1.2.0): Loading from cache
  - Installing drupal/menu_admin_per_menu (1.0.0): Loading from cache
  - Installing drupal/entityqueue_form_widget (1.0.0-beta3): Loading from cache
  - Installing drupal/password_policy (3.0.0-alpha4): Loading from cache
  - Installing drupal/google_analytics (2.2.0): Loading from cache
  - Installing drupal/smart_trim (1.0.0): Loading from cache
  - Installing drupal/vmi (1.0.0-beta3): Loading from cache
  - Installing drupal/tour_ui (dev-1.x 5cf793c): Cloning 5cf793c071 from cache
  - Installing drupal/tour_builder (dev-1.x d70e898): Cloning d70e898949 from cache
  - Installing drupal/schema_metatag (1.0.0-rc4): Loading from cache
  - Installing drupal/autocomplete_deluxe (1.0.0-beta1): Loading from cache
  - Installing drupal/content_lock (1.0.0-alpha4): Loading from cache
  - Applying patches for drupal/content_lock
    https://www.drupal.org/files/issues/2907026-7_0.patch (Issue #2907026: Fixed Warning: When we do not have content types yet in the system.)

  - Installing drupal/paragraphs_previewer (1.0.0): Loading from cache
  - Applying patches for drupal/paragraphs_previewer
    https://www.drupal.org/files/issues/2904917-2.patch (Issue #2904917: Make paragraph previewer work with new UI/UX improvement patch)

  - Installing drupal/views_infinite_scroll (1.5.0): Loading from cache
  - Installing drupal/ultimate_cron (2.0.0-alpha2): Loading from cache
  - Installing vardot/ace (1.2.8.1): Loading from cache
  - Installing drupal/extlink (dev-1.x 8a773a6): Cloning 8a773a6c55 from cache
  - Installing drupal/libraries (dev-3.x 061ead0): Cloning 061ead081c from cache
  - Installing drupal/masonry (1.0.0-rc2): Loading from cache
  - Installing drupal/better_exposed_filters (3.0.0-alpha3): Loading from cache
  - Installing drupal/adminimal_admin_toolbar (1.5.0): Loading from cache
  - Installing drupal/revision_log_default (1.0.0): Loading from cache
  - Installing vardot/dropzone (4.3.0.1): Loading from cache
  - Installing vardot/slick (1.6.0.1): Loading from cache
  - Installing vardot/blazy (1.8.2.1): Loading from cache
  - Installing drupal/honeypot (1.27.0): Loading from cache
  - Installing drupal/yoast_seo (1.3.0): Loading from cache
  - Applying patches for drupal/yoast_seo
    https://www.drupal.org/files/issues/2877613-2_0.patch (Issue #2877613: Add [ Text (plain, long) ] filed type [string_log] for yoast seo to use.)

  - Installing tinify/tinify (1.5.2): Loading from cache
  - Installing drupal/imageapi_optimize (2.0.0-alpha3): Loading from cache
  - Installing drupal/shield (1.1.0): Loading from cache
  - Installing drupal/smtp (1.0.0-beta3): Loading from cache
  - Installing drupal/color_field (2.0.0-rc2): Loading from cache
  - Installing drupal/xmlsitemap (1.0.0-alpha2): Loading from cache
  - Installing drupal/taxonomy_access_fix (2.1.0): Loading from cache
  - Installing drupal/mail_edit (dev-1.x bcd0041): Cloning bcd0041830 from cache
  - Installing drupal/maxlength (1.0.0-beta1): Loading from cache
  - Installing drupal/mailsystem (4.1.0): Loading from cache
  - Installing drupal/captcha (1.0.0-beta1): Loading from cache
  - Installing drupal/recaptcha (2.2.0): Loading from cache
  - Installing drupal/security_review (dev-1.x 35ebae4): Cloning 35ebae445b from cache
  - Installing drupal/seckit (1.0.0-alpha2): Loading from cache
  - Installing drupal/username_enumeration_prevention (1.0.0-beta1): Loading from cache
  - Applying patches for drupal/username_enumeration_prevention
    https://www.drupal.org/files/issues/username_enumeration_prevention-check_phase_runtime-2871931-5.patch (Issue #2871931: hook_requirements error on install phase)

  - Installing drupal/adminimal_theme (1.3.0): Loading from cache
  - Applying patches for drupal/adminimal_theme
    https://www.drupal.org/files/issues/adminimal-checkboxes_with_error-2696393-3.patch (Issue #2696393: Checkboxes with .error class are not usable)

  - Installing drupal/libraries_ui (1.0.0): Loading from cache
  - Installing drupal/masquerade (2.0.0-beta1): Loading from cache
  - Installing sunra/php-simple-html-dom-parser (v1.5.2): Loading from cache
  - Installing ezyang/htmlpurifier (v4.9.3): Loading from cache
  - Installing caxy/php-htmldiff (v0.1.5): Loading from cache
  - Installing mkalkbrenner/php-htmldiff-advanced (0.0.8): Loading from cache
  - Installing drupal/diff (1.0.0-rc1): Loading from cache
  - Installing drupal/fast_404 (1.0.0-alpha2): Loading from cache
  - Installing drupal/node_edit_protection (dev-1.x 902339c): Cloning 902339c082 from cache
  - Installing drupal/coffee (1.0.0-beta2): Loading from cache
  - Applying patches for drupal/coffee
    https://www.drupal.org/files/issues/coffee-coffee_breaks_page_with_rtl-2815381-6.patch (Issue #2815381: Coffee breaks whole page when using right to left (rtl) languages in drupal 8)
    https://www.drupal.org/files/issues/2872093-10.patch (Issue #2872093: Coffee css fixes in RTL)

  - Installing drupal/l10n_client (dev-1.x 9bf8d59): Cloning 9bf8d59773 from cache
  - Applying patches for drupal/l10n_client
    https://www.drupal.org/files/issues/2867460-2.patch (Issue #2867460: Incompatible with this version of Drupal core 8.3.0)
    https://www.drupal.org/files/issues/l10n_client-hook_toolbar-fix-2872668-3.patch (Issue #2872668: User error: 0 is an invalid render array key)

  - Installing drupal/pathauto (1.0.0): Loading from cache
  - Installing drupal/roleassign (1.0.0-alpha2): Loading from cache
  - Installing drupal/userprotect (1.0.0): Loading from cache
  - Installing drupal/persistent_login (1.0.0-alpha3): Loading from cache
  - Applying patches for drupal/persistent_login
    https://www.drupal.org/files/issues/persistent_login-user_task_permission-2899530-6.patch (Issue #2899530: Hide user^s local task)

  - Installing drupal/media_entity_document (1.1.0): Loading from cache
  - Installing drupal/features (3.5.0): Loading from cache
  - Installing drupal/menu_block (1.4.0): Loading from cache
  - Installing drupal/advanced_text_formatter (1.0.0-beta3): Loading from cache
  - Installing vardot/varbase (dev-8.x-4.x 84c794f): Cloning 84c794fec1 from cache
zendframework/zend-feed suggests installing zendframework/zend-cache (Zend\Cache component, for optionally caching feeds between requests)
zendframework/zend-feed suggests installing zendframework/zend-db (Zend\Db component, for use with PubSubHubbub)
zendframework/zend-feed suggests installing zendframework/zend-http (Zend\Http for PubSubHubbub, and optionally for use with Zend\Feed\Reader)
zendframework/zend-feed suggests installing zendframework/zend-servicemanager (Zend\ServiceManager component, for easily extending ExtensionManager implementations)
zendframework/zend-feed suggests installing zendframework/zend-validator (Zend\Validator component, for validating email addresses used in Atom feeds and entries when using the Writer subcomponent)
symfony/translation suggests installing symfony/config ()
symfony/validator suggests installing psr/cache-implementation (For using the metadata cache.)
symfony/validator suggests installing symfony/config ()
symfony/validator suggests installing symfony/expression-language (For using the Expression validator)
symfony/validator suggests installing symfony/intl ()
symfony/serializer suggests installing psr/cache-implementation (For using the metadata cache.)
symfony/serializer suggests installing symfony/property-info (To deserialize relations.)
symfony/serializer suggests installing symfony/config (For using the XML mapping loader.)
symfony/serializer suggests installing symfony/property-access (For using the ObjectNormalizer.)
symfony/routing suggests installing symfony/config (For using the all-in-one router or any loader)
symfony/routing suggests installing symfony/expression-language (For using expression matching)
paragonie/random_compat suggests installing ext-libsodium (Provides a modern crypto API that can be used to generate random bytes.)
symfony/http-kernel suggests installing symfony/browser-kit ()
symfony/http-kernel suggests installing symfony/config ()
symfony/http-kernel suggests installing symfony/finder ()
symfony/http-kernel suggests installing symfony/var-dumper ()
symfony/dependency-injection suggests installing symfony/config ()
symfony/dependency-injection suggests installing symfony/expression-language (For using expressions in service container configuration)
symfony/dependency-injection suggests installing symfony/proxy-manager-bridge (Generate service proxies to lazy load them)
symfony/console suggests installing symfony/filesystem ()
symfony/class-loader suggests installing symfony/polyfill-apcu (For using ApcClassLoader on HHVM)
easyrdf/easyrdf suggests installing ml/json-ld (~1.0)
doctrine/cache suggests installing alcaeus/mongo-php-adapter (Required to use legacy MongoDB driver)
drupal/dropzonejs suggests installing enyo/dropzone (Required to user drupal/dropzonejs. Dropzone is an easy to use drag'n'drop library.)
drupal/config_filter suggests installing drupal/config_split (Split site configuration for different environments.)
drupal/devel suggests installing symfony/var-dumper (Pretty print complex values better with var-dumper available)
Writing lock file
Generating autoload files
Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)DownloadinDownloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)Downloading (100%)> Varbase\composer\ScriptHandler::postDrupalScaffoldProcedure
> Varbase\composer\ScriptHandler::createRequiredFiles
Create a sites/default/settings.php file with chmod 0666
Create a sites/default/services.yml file with chmod 0666
Create a sites/default/files directory with chmod 0777
> Varbase\composer\ScriptHandler::removeGitDirectories 
```

Enter fullscreen mode Exit fullscreen mode