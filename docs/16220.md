# 为处理器创建 PHP 包

> 原文：<https://dev.to/uatthaphon/php-pacakge-composer-21no>

当我们可以创建一个资源库，无论是在 Github 中，还是在 Bitbucket 中，对于 PHP 代码都是成功的，那么我们就可以使用`git clone`23055。

正因为如此，我们才需要在[【packagist】T3㎡中加入我们的 PHP 资源库，使其重新定位。](https://packagist.org/)

# 步骤

让我们开始吧，先生，希望有人看到我们试着或不做这样的工作去“哇～这个人的包，他可以用一个复合材料。

1)创建`composer.json`我们创建的项目中的文件
。

```
{  "name":  "uatthaphon/g-crawler",  "description":  "A simple php Web Scraper that wrap up Guzzle and DomCrawler",  "keywords":  ["scrap",  "dom-crawler",  "guzzle"],  "type":  "library",  "license":  "MIT",  "authors":  [  {  "name":  "Atthaphon Urairat",  "email":  "u.atthaphon@gmail.com",  "homepage":  "https://atthaphon.urairat.com/"  }  ],  "require":  {  "php":  "^7.2",  "symfony/dom-crawler":  "^4.2",  "guzzlehttp/guzzle":  "^6.3",  "symfony/css-selector":  "^4.2"  },  "require-dev":  {  "phpunit/phpunit":  "^8.1"  },  "autoload":  {  "psr-4":  {  "GCrawler\\":  "src/"  }  },  "autoload-dev":  {  "psr-4":  {  "GCrawler\\Tests\\":  "tests/"  }  }  } 
```

2)在 [Github](https://github.com/new) 或 [Bitbucket](https://bitbucket.org/repo/create) 中创建一个资源库，然后将项目推上，或者如果

3)在[【软件包】t1㎡中添加我们的资源库，添加一个网址来检查并按下子表。](https://packagist.org/packages/submit)

4)packagist 将项目添加到系统中后，我们就可以使用`composer require yourname/coolcool`了，先生。

最后，谁在用 php 进行数据条带化，我可以试着将我的“拓扑”T3、【g-crawler】T4、检索到它。