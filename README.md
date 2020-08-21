# Magento 2 Docker Apache

This is just a personal docker-compose setup that I put together after frustrations trying to set up Magento on Windows and only seeing Nginx Docker configs for Magento instead of Apache + PHPFPM.

This isn't intended to be a comprehensive guide for setting up a Magento 2 Apache Docker environment, but some people might find it helpful or have suggestions for improvement.

Most of the helper bin commands are from Mark Shust's [Docker Magento](https://github.com/markshust/docker-magento) project, with some tweaks.

### Setup (Existing Projects)
0. Set your `.env` file, see `.env.example` for reference.
1. Import your db either using the cli helpers or using phpmyadmin
2. Run `bin/copytocontainer --all`
3. Run commands `bin/magento setup:di:compile upgrade cache:clean cache:flush`
4. Clear content directories `rm -rf var/cache/* var/di/* generated/* var/page_cache/* var/view_preprocessed/* pub/static/frontend/*`
    - Reference: https://devdocs.magento.com/guides/v2.3/howdoi/php/php_clear-dirs.html
5. `bin/magento deploy:mode:set developer`
6. Deploy static contents if page is blank: `bin/magento setup:static-content:deploy -f`
    - Run `bin/magento config:set dev/static/sign 0` since static-content:deploy sets this value to 1
    - Reset to developer mode

### Misc. Config
#### PHP-FPM - Session Files Path
Reference: https://devdocs.magento.com/guides/v2.3/config-guide/sessions.html

`env.php`
```php
    'session' => [
        'save' => 'files',
        'save_path' => '/var/www/html/var/session'
    ],
 ```

 `php.ini`
 ```ini
session.save_handler = files
session.save_path = "/var/www/html/var/session"
```

### Misc. Helpful Commands
#### Import Existing DB
`bin/clinotty mysql -hdb -u{magento_user} -p{magento_db_pw} {magento_db_name} < existing/magento.sql`

#### 

### Errors
#### Exception: Warning: SessionHandler::read(): open(/var/www/html/var/session/sess_..., O_RDWR) failed: Permission denied (13) in`

Causes:
* Incorrect file permissions for session directory

Solution: Run `bin/fixowns`

#### Everything but require.js fails to load
1. `rm -rf pub/static/*`
2. `bin/magento config:set dev/static/sign 0` (this is set to 1 if you use static-content:deploy)
3. Reset developer mode `bin/magento deploy:mode:set developer`

#### 403 error for pub/static folder
Cause: Incorrect file permissions / running Magento commands that generate files as root (`static-content:deploy`, `upgrade`, etc.) instead of Magento filesystem owner (app:www-data).

Solution: 
1. Run `bin/fixperms`
2. Run `bin/magento setup:upgrade`