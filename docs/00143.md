# 安装和管理 Elgg 项目的简单快速指南

> 原文：<https://dev.to/thecodersblog/simple-and-quick-guide-to-install-and-manage-elgg-project-pbf>

Elgg 是一个基于 PHP 的开源软件，用于个人和组织的社交网络。它拥有创建在线社交环境所需的所有组件，如博客、微博、文件共享、网络、群组和许多其他功能。

让我们深入了解一下设置过程。

#### 第一步。安装作曲者

[https://getcomposer.org/download/](https://getcomposer.org/download/)

#### 第二步。作为作曲家项目的全部 Elgg

```
composer self-update
composer global require fxp/composer-asset-plugin
composer create-project elgg/starter-project:dev-master ./path/to/my/project
cd ./path/to/my/project
composer install

## go to your browser and install Elgg via the installation interface 
```

#### 第三步。设置版本控制

```
cd ./path/to/my/project
git init
git add .
git commit -a -m 'Initial commit'
git remote add origin <git repository url>
git push -u origin master 
```

#### 第三步。安装插件

将插件作为 composer 依赖项安装。这里假设一个插件已经在[https://packagist.org/](https://packagist.org/)T2【注册

```
composer install hypejunction/hypefeed
composer install hypejunction/hypeinteractions
# whatever else you need 
```

#### 第四步。犯罪

确保`.gitignore`
中的`composer.lock`没有被忽略

```
git add .
git commit -a -m 'Add new plugins'
git push origin master 
```

#### 第五步。部署到生产环境

###### 初始部署

```
cd ./path/to/www

# you can also use git clone
git init
git remote add origin <git repository url>
git pull origin master

composer install 
```

#### 后续部署

```
cd ./path/to/www
git pull origin master

# never run composer update in production
composer install 
```

#### 在项目开发期间处理插件库

当你在做一个项目的时候，经常会发生你想要拉请求或者更新你的插件的情况。我所做的是要求插件带有-prefere-source 标志。

```
composer require hypejunction/hypefeed --prefer-source

# make some changes
cd hypeFeed
git checkout master
git commit -a -m 'Changes made'
git push origin master 
```

##### 参考文献:

1.  影像:unsplash
2.  Elgg 官方指南。