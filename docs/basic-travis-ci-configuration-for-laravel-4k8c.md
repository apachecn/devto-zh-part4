# Laravel 的基本 Travis CI 配置

> 原文：<https://dev.to/nahuelhds/basic-travis-ci-configuration-for-laravel-4k8c>

经过几个小时的反复试验，我已经完成了这个基本的`.travis.yml`文件，准备用于我的 Laravel 项目。

**注:**我的 Laravel 是 5.8 版本，配合 MariaDB 和 PHP 7.2 使用。

```
# .travis.yml
dist: precise

language: php

php:
  - 7.2

addons:
  mariadb: 10.4

cache:
  directories:
    - node_modules
    - vendor

before_script:
  - cp .env.travis .env
  - sudo mysql -e 'CREATE DATABASE testing;'
  - composer self-update
  - composer install --no-interaction
  - php artisan migrate --no-interaction -vvv

script:
  - vendor/bin/phpunit 
```

Enter fullscreen mode Exit fullscreen mode

`.env.travis`文件被版本化并包含以下代码。

```
# .env.travis

APP_NAME=Laravel
APP_ENV=testing
APP_KEY=base64:Dhtsut2yoe1Oc7Glgl4zPrGLQEKECbi3NoRNQh2N4/c=
APP_DEBUG=true
APP_URL=http://localhost/

DB_CONNECTION=mysql
DB_HOST=localhost
DB_PORT=3306
DB_DATABASE=testing
DB_USERNAME=travis
DB_PASSWORD=

BCRYPT_ROUNDS=4
CACHE_DRIVER=array
MAIL_DRIVER=array
QUEUE_CONNECTION=sync
SESSION_DRIVER=array 
```

Enter fullscreen mode Exit fullscreen mode