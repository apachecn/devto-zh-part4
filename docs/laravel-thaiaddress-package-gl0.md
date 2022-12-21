# Laravel Thaiaddress 套装

> 原文：<https://dev.to/uatthaphon/laravel-thaiaddress-package-gl0>

好吧，我有机会跟 repo [【省、区、区、区、纬度】t1
匹配。在 GitHub 上就可以了。](https://github.com/aaronamm/thai-administrative-division-province-district-subdistrict-sql)

你的 repo，T0，aaron AMM .. . t1]非常有用，谢谢你在这里。

在 laravel 有机会的时候，我想它在未来的项目中很可能有用。
所以把它做成一个 laravel packagage(laravel package)。

## 储存库

*   github[uatthaphon/laravel-Thai-address](https://github.com/uatthaphon/laravel-thai-address)
*   包装商[uatthaphon/laravel-Thai-address](https://packagist.org/packages/uatthaphon/laravel-thai-address)

## laravel-thai-address 包是什么

laravel-thai-address 软件包是将上述
中提到的地区省数据库作为 ll 的迁移。

然后以 csv 的形式执行 seeds，将包中包含的文件正确地输入到每个表中。
完成后。

### 安装

将软件包添加到我们的项目
中

```
$ composer require uatthaphon/laravel-thai-address 
```

如果我们使用 laravel 5 以下的版本。5 我们必须手动将服务产品添加到`config/app.php`手动
。

```
<?php
'providers' => [
  ...

  Uatthaphon\ThaiAddress\ThaiAddressServiceProvider::class,
], 
```

但对于拉威尔版 5 .5 及以上，我已将[auto discoverable】](https://laravel-news.com/package-auto-discovery)成功地添加到包体中，使我们不必对行进行添加。

然后执行 3 个标记的 publish verder 命令

1.  `$ php artisan vendor:publish --tag=migrations`
2.  `$ php artisan vendor:publish --tag=csv`
3.  `$ php artisan vendor:publish --tag=seeds`

或者，如果您需要一次发布所有标记，它可以做到

。

```
$ php artisan vendor:publish 
```

下一步，我们将在完成所有的发布之后来执行表创建。
t0㎡。

```
$ php artisan migrate 
```

然后用 seeder 将数据添加到我们的表中，其中 seeder

将把当前在我们的项目中的 csv 文件作为一个调用。

```
$ php artisan db:seed --class=ThailandAddressSeeder 
```

### [t 1㎡的用法](#%E0%B8%81%E0%B8%B2%E0%B8%A3%E0%B9%83%E0%B8%8A%E0%B9%89%E0%B8%87%E0%B8%B2%E0%B8%99)

现在我们得到了一个包含省、区、区、县、地区信息的数据，latitude t0㎡的邮政编码，我们可以应用到项目中。

### 辅助

在我们的包里，有一个后续的 relationship 模型。

~~如果任何人想要使用 Models，则绑定 relationship 的文件可以将 psr-4 添加到
`composer.json`中，以便 json
【运行中的项目】，先生，我们可以在下面的例子中直接运行 。~~

然后就可以使用了。
t0㎡。

```
 <?php

use Uatthaphon\ThaiAddress\Models\ThailandProvince;
use Uatthaphon\ThaiAddress\Models\ThailandDistrict;
use Uatthaphon\ThaiAddress\Models\ThailandSubdistrict;

...

/**
 * Available Relationships
 */

// list all districts under the province
app(ThailandProvince::class)->find(1)->districts()->get();

// get province of the district
app(ThailandDistrict::class)->find(1)->province;
// list all sub districtes under the district
app(ThailandDistrict::class)->find(1)->subdistricts()->get();

// get district of the subdistrict
app(ThailandSubdistrict::class)->find(1)->district;
// get province of the subdistrict
app(ThailandSubdistrict::class)->find(1)->province; 
```

如果有人有疑问，可以把它放在这里，t0㎡或者有 bug 或者 issue，任何东西都可以贴在 github 上。