# 解决 Travis CI 上的 MariaDB 错误

> 原文：<https://dev.to/nahuelhds/solving-mariadb-errors-on-travis-ci-3p60>

## 错误 1698 (28000):拒绝用户“root”@“localhost”的访问

你需要在行执行中使用`sudo`。是啊。就这样。

我有这个`.travis.yml`文件

```
addons:
  mariadb: 10.4

before_script:
  - mysql -e 'create database testing;' 
```

注意`mysql -e 'create database testing;'`行。修复是使用`sudo`

```
addons:
  mariadb: 10.4

before_script:
  - sudo mysql -e 'create database testing;' 
```

## 用户“Travis”@“localhost”的 SQLSTATE[HY000] [1698]访问被拒绝

这个错误的发生是因为 Travis 默认使用了与 MariaDB 插件不完全兼容的`dist: xenial`。

解决方法是在你的`.travis.yml`文件
中设置`dist: precise`

```
dist: precise

# ... rest of your config file 
```

仅此而已。希望有帮助！