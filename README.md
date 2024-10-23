Based on https://hub.docker.com/_/php/

Using:
* PHP-modules management: https://github.com/mlocati/docker-php-extension-installer
* FPM-healthcheck: https://github.com/renatomefi/php-fpm-healthcheck

Changelog:
* 

PHP-FPM based containers, adding PHP extensions that we often use:

Support for locales:
* en_GB.UTF-8
* en_US.UTF-8
* nl_NL.UTF-8
* de_DE.UTF-8
* fr_FR.UTF-8
* es_ES.UTF-8

Enabled default PHP extensions:
* gd 
* exif 
* mcrypt 
* zip 
* mbstring 
* intl 
* bcmath 
* curl 
* pdo_mysql 
* xsl 
* soap 
* mysqli 
* simplexml 
* sockets 
* xmlrpc 
* opcache 
* mysql 
* ldap
* memcache
* memcached
* redis
* imagick

# Extra software
Used for apps that have no separated front-end (like react), we have added nodejs so these can also be build inside the same container:
* nodejs
* npm
* yarn 
* pm2

## EXAMPLE docker-compose.yml, using the VIRTUAL_HOST tag and the jwilder-proxy:
```
services:
    web:
        image: jurriaanr/apache:2.4-fpm
        restart: always
        network_mode: "bridge"
        environment:
            - "VIRTUAL_HOST=${DOCKER_VHOST}"
        links:
            - php
        depends_on:
            - php
        volumes:
            - .:/app/:cached

    php:
        image: jurriaanr/php:8.3-fpm
        restart: always
        network_mode: "bridge"
        volumes:
            - .:/app/:cached
        environment:
            - "PHP_SESSION_SAVE_HANDLER=redis"
            - "PHP_SESSION_SAVE_PATH=tcp://redis:6379"
        links:
            - redis
        depends_on:
            - redis

    redis:
        image: redis:alpine
        restart: always
        network_mode: "bridge"
```

## DEFAULT VALUES:

These values can be overwritten in your .env or "environment"-tag in your docker-compose.yml:

PHP_MAX_EXECUTION_TIME=120\
PHP_MEMORY_LIMIT=256M\
PHP_UPLOAD_LIMIT=8M\
PHP_OPCACHE_LIMIT=64M\
PHP_OPCACHE_REVALIDATE=2\
PHP_SESSION_SAVE_HANDLER=files\
PHP_SESSION_SAVE_PATH=""\
PHP_SMTP_HOST=localhost\
PHP_MAX_INPUT_VARS=1000

For example, you can add a redis container and change the redis-settings in your PHP-container:
```
services:
    php:
        image: jurriaanr/php:8.3-fpm
        network_mode: "bridge"
        environment:
            - "PHP_SESSION_SAVE_HANDLER=redis"
            - "PHP_SESSION_SAVE_PATH=tcp://redis:6379"
        links:
            - redis
        depends_on:
            - redis

    redis:
        image: redis:alpine
        network_mode: "bridge"
```

For development, set PHP_OPCACHE_REVALIDATE to "0", the default value "2" is somewhat of a tradeoff.