# 安装 Wallabag

> 原文：<https://dev.to/psnebc/install-wallabag-2fl>

共享链接由 Wallabag 在这里免费托管 Framabag[https://framabag.org/share/5a1ac616985444.97419345](https://framabag.org/share/5a1ac616985444.97419345)T2】

```
➜  html cd wallabag 
➜  wallabag git:(master) sudo make install composer.phar not found, we'll see if composer is installed globally.
Note: checking out '2.2.3'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 82307d88... Release wallabag 2.2.3
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Installing dependencies from lock file
Package operations: 108 installs, 0 updates, 0 removals
  - Installing react/promise (v2.5.1): Loading from cache
  - Installing guzzlehttp/streams (3.0.0): Loading from cache
  - Installing guzzlehttp/ringphp (1.1.0): Loading from cache
  - Installing guzzlehttp/guzzle (5.3.1): Loading from cache
  - Installing bdunogier/guzzle-site-authenticator (v1.0.0-beta1): Loading from cache
  - Installing behat/transliterator (v1.1.0): Loading from cache
  - Installing twig/twig (v1.33.2): Loading from cache
  - Installing symfony/polyfill-util (v1.3.0): Loading from cache
  - Installing paragonie/random_compat (v1.4.2): Loading from cache
  - Installing symfony/polyfill-php70 (v1.3.0): Loading from cache
  - Installing symfony/polyfill-php56 (v1.3.0): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.3.0): Loading from cache
  - Installing symfony/symfony (v3.2.8): Loading from cache
  - Installing symfony/polyfill-intl-icu (v1.3.0): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing psr/cache (1.0.1): Loading from cache
  - Installing doctrine/lexer (v1.0.1): Loading from cache
  - Installing doctrine/inflector (v1.1.0): Loading from cache
  - Installing doctrine/collections (v1.3.0): Loading from cache
  - Installing doctrine/cache (v1.6.1): Loading from cache
  - Installing doctrine/annotations (v1.2.7): Loading from cache
  - Installing doctrine/common (v2.6.2): Loading from cache
  - Installing craue/config-bundle (1.4.2): Loading from cache
  - Installing doctrine/doctrine-cache-bundle (1.3.0): Loading from cache
  - Installing zendframework/zend-eventmanager (3.0.1): Loading from cache
  - Installing zendframework/zend-code (2.6.3): Loading from cache
  - Installing ocramius/proxy-manager (1.0.2): Loading from cache
  - Installing doctrine/dbal (v2.5.12): Loading from cache
  - Installing doctrine/migrations (v1.5.0): Loading from cache
  - Installing jdorn/sql-formatter (v1.2.17): Loading from cache
  - Installing doctrine/doctrine-bundle (1.6.7): Loading from cache
  - Installing doctrine/doctrine-migrations-bundle (v1.2.1): Loading from cache
  - Installing willdurand/jsonp-callback-validator (v1.1.0): Loading from cache
  - Installing friendsofsymfony/jsrouting-bundle (1.6.0): Loading from cache
  - Installing friendsofsymfony/oauth2-php (1.2.1): Loading from cache
  - Installing friendsofsymfony/oauth-server-bundle (1.5.2): Loading from cache
  - Installing willdurand/negotiation (v2.3.1): Loading from cache
  - Installing friendsofsymfony/rest-bundle (2.2.0): Loading from cache
  - Installing friendsofsymfony/user-bundle (dev-master e0c1f70): Loading from cache
  - Installing grandt/relativepath (1.0.2): Loading from cache
  - Installing grandt/binstring (1.0.0): Loading from cache
  - Installing grandt/phpzipmerge (1.0.4): Loading from cache
  - Installing phpzip/phpzip (2.0.8): Loading from cache
  - Installing grandt/phpresizegif (1.0.3): Loading from cache
  - Installing grandt/phpepub (4.0.7.1): Loading from cache
  - Installing hoa/exception (1.17.01.16): Loading from cache
  - Installing hoa/consistency (1.17.05.02): Loading from cache
  - Installing hoa/event (1.17.01.13): Loading from cache
  - Installing hoa/iterator (2.17.01.10): Loading from cache
  - Installing hoa/visitor (2.17.01.16): Loading from cache
  - Installing hoa/ustring (4.17.01.16): Loading from cache
  - Installing hoa/protocol (1.17.01.14): Loading from cache
  - Installing hoa/zformat (1.17.01.10): Loading from cache
  - Installing hoa/regex (1.17.01.13): Loading from cache
  - Installing hoa/math (1.17.05.16): Loading from cache
  - Installing hoa/stream (1.17.02.21): Loading from cache
  - Installing hoa/file (1.17.01.13): Loading from cache
  - Installing hoa/compiler (3.17.01.10): Loading from cache
  - Installing hoa/ruler (2.17.05.16): Loading from cache
  - Installing incenteev/composer-parameter-handler (v2.1.2): Loading from cache
  - Installing wallabag/tcpdf (6.2.15): Loading from cache
  - Installing true/punycode (v2.1.1): Loading from cache
  - Installing smalot/pdfparser (v0.9.26): Loading from cache
  - Installing simplepie/simplepie (1.3.1): Loading from cache
  - Installing monolog/monolog (1.22.1): Loading from cache
  - Installing j0k3r/safecurl (2.0.2): Loading from cache
  - Installing j0k3r/php-readability (1.1.7): Loading from cache
  - Installing j0k3r/graby-site-config (1.0.36): Loading from cache
  - Installing htmlawed/htmlawed (1.1.22): Loading from cache
  - Installing j0k3r/graby (1.8.2): Loading from cache
  - Installing javibravo/simpleue (1.1.1): Loading from cache
  - Installing phpoption/phpoption (1.5.0): Loading from cache
  - Installing phpcollection/phpcollection (0.5.0): Loading from cache
  - Installing jms/parser-lib (1.0.0): Loading from cache
  - Installing jms/metadata (1.6.0): Loading from cache
  - Installing doctrine/instantiator (1.0.5): Loading from cache
  - Installing jms/serializer (1.7.1): Loading from cache
  - Installing kphoen/rulerz (0.20.4): Loading from cache
  - Installing kphoen/rulerz-bundle (0.13.1): Loading from cache
  - Installing doctrine/orm (v2.5.6): Loading from cache
  - Installing lexik/form-filter-bundle (v5.0.4): Loading from cache
  - Installing liip/theme-bundle (1.4.6): Loading from cache
  - Installing mgargano/simplehtmldom (1.5): Loading from cache
  - Installing mnapoli/piwik-twig-extension (1.0.0): Loading from cache
  - Installing michelf/php-markdown (1.7.0): Loading from cache
  - Installing nelmio/api-doc-bundle (2.13.1): Loading from cache
  - Installing nelmio/cors-bundle (1.4.1): Loading from cache
  - Installing php-amqplib/php-amqplib (v2.6.3): Loading from cache
  - Installing php-amqplib/rabbitmq-bundle (v1.12.0): Loading from cache
  - Installing predis/predis (v1.1.1): Loading from cache
  - Installing sonata-project/google-authenticator (1.0.2): Loading from cache
  - Installing scheb/two-factor-bundle (v2.9.0): Loading from cache
  - Installing composer/ca-bundle (1.0.7): Loading from cache
  - Installing sensiolabs/security-checker (v4.0.4): Loading from cache
  - Installing sensio/distribution-bundle (v5.0.20): Loading from cache
  - Installing sensio/framework-extra-bundle (v3.0.26): Loading from cache
  - Installing gedmo/doctrine-extensions (v2.4.28): Loading from cache
  - Installing stof/doctrine-extensions-bundle (v1.2.2): Loading from cache
  - Installing symfony/monolog-bundle (v3.1.0): Loading from cache
  - Installing swiftmailer/swiftmailer (v5.4.8): Loading from cache
  - Installing symfony/swiftmailer-bundle (v2.5.4): Loading from cache
  - Installing twig/extensions (v1.4.1): Loading from cache
  - Installing wallabag/php-mobi (1.0.1): Loading from cache
  - Installing pagerfanta/pagerfanta (v1.0.5): Loading from cache
  - Installing white-october/pagerfanta-bundle (v1.0.8): Loading from cache
  - Installing willdurand/hateoas (2.10.0): Loading from cache
  - Installing jms/serializer-bundle (1.5.0): Loading from cache
  - Installing willdurand/hateoas-bundle (1.2.0): Loading from cache
Generating optimized autoload files
> Incenteev\ParameterHandler\ScriptHandler::buildParameters
Creating the "app/config/parameters.yml" file
Some parameters are missing. Please provide them.
database_driver (pdo_sqlite): pdo_mysql
database_host (127.0.0.1): 3306
database_port (null): ^CMakefile:13: recipe for target 'install' failed
make: *** [install] Interrupt

➜  wallabag git:(82307d88) sudo make install
[sudo] password for psnc: 
composer.phar not found, we'll see if composer is installed globally.
HEAD is now at 82307d88... Release wallabag 2.2.3
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Installing dependencies from lock file
Nothing to install or update
Generating optimized autoload files
> Incenteev\ParameterHandler\ScriptHandler::buildParameters
Creating the "app/config/parameters.yml" file
Some parameters are missing. Please provide them.
database_driver (pdo_sqlite): pdo_mysql
database_host (127.0.0.1): 
database_port (null): 3306
database_name (symfony): test_wallabag
database_user (root): root
database_password (null): psnc
database_path ('%kernel.root_dir%/../data/db/wallabag.sqlite'): 
database_table_prefix (wallabag_): 
database_socket (null): 
database_charset (utf8): 
mailer_transport (smtp): 
mailer_host (127.0.0.1): 
mailer_user (null): 
mailer_password (null): 
locale (en): 
secret (ovmpmAWXRCabNlMgzlzFXDYmCFfzGv): 
twofactor_auth (true): 
twofactor_sender (no-reply@wallabag.org): 
fosuser_registration (true): 
fosuser_confirmation (true): 
from_email (no-reply@wallabag.org): 
rss_limit (50): 
rabbitmq_host (localhost): 
rabbitmq_port (5672): 
rabbitmq_user (guest): 
rabbitmq_password (guest): 
rabbitmq_prefetch_count (10): 
redis_scheme (tcp): 
redis_host (localhost): 
redis_port (6379): 
redis_path (null): 
redis_password (null): 
sites_credentials ({  }): 
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::buildBootstrap
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::clearCache
PHP Warning:  count(): Parameter must be an array or an object that implements Countable in /var/www/html/wallabag/vendor/symfony/monolog-bundle/DependencyInjection/Configuration.php on line 654

 // Clearing the cache for the prod environment with debug                      
 // false                                                                       

 [OK] Cache for the "prod" environment (debug=false) was successfully cleared.  

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installAssets
PHP Warning:  count(): Parameter must be an array or an object that implements Countable in /var/www/html/wallabag/vendor/symfony/monolog-bundle/DependencyInjection/Configuration.php on line 654

 Trying to install assets as relative symbolic links.

 --- ------------------------------ ------------------ 
      Bundle                         Method / Error    
 --- ------------------------------ ------------------ 
  ✔   NelmioApiDocBundle             relative symlink  
  ✔   WhiteOctoberPagerfantaBundle   relative symlink  
  ✔   FOSJsRoutingBundle             relative symlink  
 --- ------------------------------ ------------------ 

 [OK] All assets were successfully installed.                                   

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installRequirementsFile
Installing wallabag...

Step 1 of 4\. Checking system requirements.
+------------------------+--------+----------------+
| Checked                | Status | Recommendation |
+------------------------+--------+----------------+
| PDO Driver (pdo_mysql) | OK!    |                |
| Database connection    | OK!    |                |
| Database version       | OK!    |                |
| curl_exec              | OK!    |                |
| curl_multi_init        | OK!    |                |
+------------------------+--------+----------------+
Success! Your system can run wallabag properly.

Step 2 of 4\. Setting up database.
It appears that your database already exists. Would you like to reset it? (y/N)y
Droping database, creating database and schema

The command "doctrine:database:drop" generates some errors: 
Could not drop database for connection named /var/www/html/wallabag/app/../data/db/wallabag.sqlite
An exception occurred while executing 'DROP DATABASE /var/www/html/wallabag/app/../data/db/wallabag.sqlite':

SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '/var/www/html/wallabag/app/../data/db/wallabag.sqlite' at line 1

➜  wallabag git:(82307d88) ✗ sudo make install
[sudo] password for psnc: 
composer.phar not found, we'll see if composer is installed globally.
M       bin/symfony_requirements
HEAD is now at 82307d88... Release wallabag 2.2.3
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Installing dependencies from lock file
Nothing to install or update
Generating optimized autoload files
> Incenteev\ParameterHandler\ScriptHandler::buildParameters
Updating the "app/config/parameters.yml" file
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::buildBootstrap
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::clearCache

 // Clearing the cache for the prod environment with debug                      
 // false                                                                       

 [OK] Cache for the "prod" environment (debug=false) was successfully cleared.  

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installAssets
PHP Warning:  count(): Parameter must be an array or an object that implements Countable in /var/www/html/wallabag/vendor/symfony/monolog-bundle/DependencyInjection/Configuration.php on line 654

 Trying to install assets as relative symbolic links.

 --- ------------------------------ ------------------ 
      Bundle                         Method / Error    
 --- ------------------------------ ------------------ 
  ✔   NelmioApiDocBundle             relative symlink  
  ✔   WhiteOctoberPagerfantaBundle   relative symlink  
  ✔   FOSJsRoutingBundle             relative symlink  
 --- ------------------------------ ------------------ 

 [OK] All assets were successfully installed.                                   

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installRequirementsFile
Installing wallabag...

Step 1 of 4\. Checking system requirements.
+------------------------+--------+----------------+
| Checked                | Status | Recommendation |
+------------------------+--------+----------------+
| PDO Driver (pdo_mysql) | OK!    |                |
| Database connection    | OK!    |                |
| Database version       | OK!    |                |
| curl_exec              | OK!    |                |
| curl_multi_init        | OK!    |                |
+------------------------+--------+----------------+
Success! Your system can run wallabag properly.

Step 2 of 4\. Setting up database.
It appears that your database already exists. Would you like to reset it? (y/N)y
Droping database, creating database and schema

The command "doctrine:database:drop" generates some errors: 
Could not drop database for connection named /var/www/html/wallabag/app/../data/db/wallabag.sqlite
An exception occurred while executing 'DROP DATABASE /var/www/html/wallabag/app/../data/db/wallabag.sqlite':

SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '/var/www/html/wallabag/app/../data/db/wallabag.sqlite' at line 1

➜  wallabag git:(82307d88) ✗ sudo make install
[sudo] password for psnc: 
composer.phar not found, we'll see if composer is installed globally.
M       bin/symfony_requirements
HEAD is now at 82307d88... Release wallabag 2.2.3
Do not run Composer as root/super user! See https://getcomposer.org/root for details
Loading composer repositories with package information
Installing dependencies from lock file
Nothing to install or update
Generating optimized autoload files
> Incenteev\ParameterHandler\ScriptHandler::buildParameters
Updating the "app/config/parameters.yml" file
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::buildBootstrap
> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::clearCache

 // Clearing the cache for the prod environment with debug                      
 // false                                                                       

 [OK] Cache for the "prod" environment (debug=false) was successfully cleared.  

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installAssets
PHP Warning:  count(): Parameter must be an array or an object that implements Countable in /var/www/html/wallabag/vendor/symfony/monolog-bundle/DependencyInjection/Configuration.php on line 654

 Trying to install assets as relative symbolic links.

 --- ------------------------------ ------------------ 
      Bundle                         Method / Error    
 --- ------------------------------ ------------------ 
  ✔   NelmioApiDocBundle             relative symlink  
  ✔   WhiteOctoberPagerfantaBundle   relative symlink  
  ✔   FOSJsRoutingBundle             relative symlink  
 --- ------------------------------ ------------------ 

 [OK] All assets were successfully installed.                                   

> Sensio\Bundle\DistributionBundle\Composer\ScriptHandler::installRequirementsFile
Installing wallabag...

Step 1 of 4\. Checking system requirements.
+------------------------+--------+----------------+
| Checked                | Status | Recommendation |
+------------------------+--------+----------------+
| PDO Driver (pdo_mysql) | OK!    |                |
| Database connection    | OK!    |                |
| Database version       | OK!    |                |
| curl_exec              | OK!    |                |
| curl_multi_init        | OK!    |                |
+------------------------+--------+----------------+
Success! Your system can run wallabag properly.

Step 2 of 4\. Setting up database.
It appears that your database already exists. Would you like to reset it? (y/N)n
Seems like your database contains schema. Do you want to reset it? (y/N)y
Droping schema and creating schema
Clearing the cache
PHP Warning:  count(): Parameter must be an array or an object that implements Countable in /var/www/html/wallabag/vendor/symfony/monolog-bundle/DependencyInjection/Configuration.php on line 654

Step 3 of 4\. Administration setup.
Would you like to create a new admin user (recommended) ? (Y/n)y
Username (default: wallabag) :admin
Password (default: wallabag) :psnc
Email:patrick.schanen@gmail.com

Step 4 of 4\. Config setup.

wallabag has been successfully installed.
Just execute `php bin/console server:run --env=prod` for using wallabag: http://localhost:8000
➜  wallabag git:(82307d88) ✗ php bin/console server:run --env=prod

 [ERROR] Running PHP built-in server in production environment is NOT recommended!                                      

 [OK] Server running on http://127.0.0.1:8000                                                                           

 // Quit the server with CONTROL-C. 
```

Enter fullscreen mode Exit fullscreen mode