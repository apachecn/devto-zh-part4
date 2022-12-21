# 使用从 include . agilo 创建的 docs API 测试程序。apib 与 Laravel 的几个文件

> 原文：<https://dev.to/uatthaphon/dredd-api-docs-agilo-include-apib-laravel-1je4>

本文将讨论编写 artisan command，用于执行
Aglio 的包命令，以编译我们编写的各种子文件。

## 在本文中我们使用了什么

*   [作曲家](https://getcomposer.org/)
*   [npm](https://www.npmjs.com/)
*   拉勒维尔
*   [阿格里奥](https://github.com/danielgtaylor/aglio)
*   [解冻](https://github.com/apiaryio/dredd)

## 从 API 生成中遇到的问题看其长度是否合理

这个问题是我在创建一个足够长的 docs
Api 时遇到的直接经验。在开始创建 Api 时，我们将不会有任何 API。我们的 apib
开始逐渐膨胀，或者在某些情况下在开发之前设计所有 Api 也是如此。

很多时候，当我们不得不无限期地向下滚动页面或者正在打印的时候，
，但是不小心，我们的手不小心碰到了 pad 轨道，直到它使 cud。好的。

因此，我转而使用输入文件`<!-- include(filename{.apib|.md|.json}) -->`
将每个端点的写入分离到新文件中。

但是当我们必须进行测试以验证我们在
【API docs】中设计的端点和我们创建的端点是否吐出值。

所以我得想个办法来修改一些东西，让你不要... t0㎡一份模块化的文件已经没用了。

所以我想这应该对其他人有所帮助。把它贴在这里。

## 开始工作

### [t1㎡安装复合材料](#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-composer)

如果有人安装了，请跳过去

```
$ curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/bin --filename=composer 
```

### 安装 npm

如果有人安装了，请跳过去

如果任何人尚未安装，请下载[节点。js](https://nodejs.org/en/download/)
先安装，然后是
。

```
$ npm install npm@latest -g 
```

### [t1【安装拉威尔】](#%E0%B8%95%E0%B8%B4%E0%B8%94%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87-laravel)

如果有人安装了，请跳过去

如果我的朋友还没有安装 laravel
，我就去看看如何安装它。

让我们创建一个名为 dejo 的新项目。**我运行第 5 版。4.xx，先生，**T2㎡。

```
$ composer create-project --prefer-dist laravel/laravel dejo 
```

### 安装 aglio

如果有人安装了，请跳过去

```
$ npm install -g aglio 
```

### 安装防滚翻

如果有人安装了，请跳过去

```
$ npm install -g dredd 
```

## 踏上写 Api 的 Docs

我们使用[【apiblueprint】](https://apiblueprint.org/)T2
进行书写，先生
可以进入预览。

然后，让我们创建一个`docs`文件夹，以便在
之前将我们的 docs API 放在一起，首先从进入`dejo`文件夹开始。

```
$ cd path/to/dejo 
```

然后创建一个`docs`文件夹，并进入`public`文件夹
中。

```
$ mkdir public/docs && cd public/docs 
```

然后创建了三个文件，第一个文件是`document.apib`
【用作主文件，用来检索不同的 include 文件、第二个文件和文件。

```
$ touch document.apib one.apib two.apib 
```

然后我们就编写了一个 docs API

document.apib

```
FORMAT: 1A
HOST: http://dejo.localhost

# Dejo API

Dejo API ใช้สำหรับ [ใช้ Dredd เทส API Docs ที่สร้างจาก Agilo หลายๆไฟล์ ใน Laravel](https://atthaphon.urairat.me/2018/01/25/ใช้-dredd-เทส-api-docs-ที่สร้างจาก-agilo-หลายๆไฟล์-ใน-laravel/)

# Group One
ใช้รับค่าจากฟีเจอร์ One

## One List [/one]
Retrieving a list of available feature one of Dejo application.

<!-- include(one.apib) -->

# Group Two
ใช้รับค่าจากฟีเจอร์ Two

## Two List [/two]
Retrieving a list of available feature Two of Dejo application.

<!-- include(two.apib) --> 
```

one.apib

```
### Get a list one feature [GET]

ตัวอย่างการคืนค่า สำหรับทบความจาก [ใช้ Dredd เทส API Docs ที่สร้างจาก Agilo หลายๆไฟล์ ใน Laravel](https://atthaphon.urairat.me/2018/01/25/ใช้-dredd-เทส-api-docs-ที่สร้างจาก-agilo-หลายๆไฟล์-ใน-laravel/)
 + Response 200 (application/json) + Body

            {
                    "data": [
                        {
                                "id": 1,
                                "name": "Sexy Name"
                        }
                    ]
            } 
```

two.apib

```
### Get a list two feature [GET]

ตัวอย่างการคืนค่า สำหรับทบความจาก [ใช้ Dredd เทส API Docs ที่สร้างจาก Agilo หลายๆไฟล์ ใน Laravel](https://atthaphon.urairat.me/2018/01/25/ใช้-dredd-เทส-api-docs-ที่สร้างจาก-agilo-หลายๆไฟล์-ใน-laravel/)
 + Response 200 (application/json) + Body

            {
                    "data": [
                        {
                                "id": 1,
                                "name": "Naruto Uzumaki",
                                "title": "Hokage"
                        }
                    ]
            } 
```

完成了，先生，我们的原始 docs API，但还没完成，先生，我们必须创建。html 文件提供给我们的
docs，记得为我们的 docs API 创建一个路由。

## Aglio 用于转换。apib 是。html

我们将从文件中转换我们创建的文档。apib 用 markdown 编写并包含
include，也就是 Aglio 在
gene 中用来映射文件的 syntax。html 让我们用它来展示我们的 API docs
页面，先生。

一旦一切就绪，就执行创建文档的命令。html 文件

*   I 是输入文件。
*   o 是文件输出。

```
$ aglio -i document.apib -o document.html 
```

然后我们将去在 laravel 中创建一个 route，用于查看我们的 docs API。

## 创建用于打开我们的 docs API 的路由

创建一个用于打开 Aglio 为我们创建的\ t0 }文件的 route，在一段时间内，
该文件存储在与文件 120 相同的位置。

通过在`routes/web.php`t1
文件中添加 route

```
<?php
...

Route::get('document/', function () {
    return File::get(public_path() . '/docs/document.html');
}); 
```

然后我们打开网址，就可以看到像这样的 Api Docs。

[![Run Dredd With Aglio Multiple Blueprint Files](img/b1ac58ddd4147eed7c0b877500d0819a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--48TbL8TN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/post/run-dredd-with-aglio-multiple-blueprint-files.jpg)

## 为/one 和/two 创建一个端点

如果我们要将 Api Docs 倒过来，我们就需要一个 eclipse endpoint 与我们已经写好的 API
【docs】匹配，对吗？

```
<?php
...

Route::get('one/', function () {
    $data = ['data' => [['id'=>1, "name"=> "Sexy Name"]]];

    return response()->json($data);
});

Route::get('two/', function () {
    $data = ['data' =>[["id" => 1, "name" => "Naruto Uzumaki", "title" => "Hokage"]]];

    return response()->json($data);
}); 
```

## 用 redd 对 API Docs 进行测试

问题就在这里，当我们执行 include 时 apib 它将使 dredd
无法检查输入的文档

首先，让我们先为我们的 redd 做一个初步的设置，在
之前，我们必须`cd path/to/dejo`才能进入我们的项目文件夹根。

```
$ dredd init
? Location of the API description document ./public/docs/document.apib
? Command to start API backend server e.g. (bundle exec rails server)
? URL of tested API endpoint: http://dejo.localhost
? Programming language of hooks:
❯ php
  perl
  go
  ...
? Do you want to use Apiary test inspector? No
? Dredd is best served with Continuous Integration. Create CircleCI config for Dredd? No 
```

我们就能得到一个文件`dredd.yml`来
，接下来我们就通过键入
命令来执行一个 redd 测试。

```
$ dredd
info: Configuration './dredd.yml' found, ignoring other arguments.
info: Beginning Dredd testing...
complete: Tests took 5ms 
```

我们可以看到，根本没有测试还原，这是因为 dredd 找不到我们创建的端点。
那是因为 dredd 不支持文件。

## 为 comple 和 runtess 创建一个 Laravel aritisan 命令

想法是，我们将创建一个 artisan command line 来帮助我们更好的生活。【t0㎡包含我们需要的命令在运行中。

1.  在文件中创建 docs API。html
2.  建立档案另一个 apib 将从主文件运行的每个 include 文件合并到一个文件中。
3.  运行 redd 以将 Api Docs 与我们创建的端点相结合

我们就不用坐在那里打字了

### 组成阿蒂桑命令

再像以前一样打开 iterm，然后是 t0㎡t0。

```
$ php artisan make:command ApiDocument 
```

我们会得到一个名为`ApiDocument.php`的文件，这个新文件将在
文件夹中，`app/console/commnads/ApiDocument.php`让我们把它打开
然后写。

ApiDocument.php

```
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\File;
use Symfony\Component\Process\Process;
use Symfony\Component\Process\Exception\ProcessFailedException;

class ApiDocument extends Command
{
    protected $signature = 'dejo:document';

    protected $description = 'Dejo\'s command to generate API documentation, also create compile .apib for test purpose  and run  dredd command to test our Api documentation.';

    public function __construct()
    {
        parent::__construct();
    }

    public function handle()
    {
        $this->rendererDoc();
        $this->compileDoc();
        $this->runDreddTesting();
    }

    private function rendererDoc()
    {
        $apibFile = public_path()."/docs/document.apib";

        $docsFile = public_path()."/docs/document.html";

        $process = new Process("aglio -i $apibFile -o $docsFile");

        $process->run();

        try {
            $process->mustRun();

            $this->info("Dejo renderer documentation completed...");
        } catch (ProcessFailedException $e) {
            $this->info($e->getMessage());
        }
    }

    private function compileDoc()
    {
        $apibFile = public_path()."/docs/document.apib";

        $compileFile = public_path()."/docs/compile.apib";

        $process = new Process("aglio -i $apibFile --compile -o $compileFile");

        $process->run();

        try {
            $process->mustRun();

            $this->info("Dejo compile blue print file completed...");
        } catch (ProcessFailedException $e) {
            $this->info($e->getMessage());
        }
    }

    private function runDreddTesting()
    {
        $process = new Process("dredd");

        $process->run();

        try {
            $process->mustRun();

            $this->info("Dredd is running please wait...");

            $this->info($process->getOutput());

        } catch (ProcessFailedException $e) {
            $this->info($e->getMessage());
        }
    }

} 
```

### 注册处我们的指令也是

创建命令后，我们还需要向 laravel
引入这个新命令，然后才能使用它。

让我们打开`app/Console/Commands/Kernel.php`文件，将此行添加到
中。

```
<?php
...

class Kernel extends ConsoleKernel
{

    protected $commands = [
        Commands\ApiDocument::class, // แนะนำตัวกับ kernel ให้เจ้าบ้าน Laravel เรียกใช้งานเราได้
    ];

    ...
} 
```

### [t1【红雀设置】。一个新的 yml 为 Tess](#%E0%B8%95%E0%B8%B1%E0%B9%89%E0%B8%87%E0%B8%84%E0%B9%88%E0%B8%B2-dreddyml-%E0%B9%83%E0%B8%AB%E0%B8%A1%E0%B9%88%E0%B8%AD%E0%B8%B5%E0%B8%81%E0%B8%84%E0%B8%A3%E0%B8%B1%E0%B9%89%E0%B8%87%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A%E0%B9%80%E0%B8%97%E0%B8%AA)

从我们创建的 command 中的新代码示例中可以看出，在
`compileDoc()`函数中，我们完成了文件编译。

因此，我们将在`dredd.yml`中更改变量值，使“t1”
读取“dredd”文件，而不是`document.apib`

。

```
...
# เปลี่ยนจาก ./public/docs/document.apib เป็น ./public/docs/compile.apib
blueprint: ./public/docs/compile.apib 
```

### 测试命令更好

就快完成了，先生，终于尝试了一个新的东西，真的浪费在了
开始测试我们编写的代码，比
更好。

```
$ php artisan dejo:document
Dejo renderer documentation completed...
Dejo compile blue print file completed...
Dredd is running please wait...
info: Configuration './dredd.yml' found, ignoring other arguments.
info: Beginning Dredd testing...
pass: GET (200) /one duration: 391ms
pass: GET (200) /two duration: 286ms
complete: 2 passing, 0 failing, 0 errors, 0 skipped, 2 total
complete: Tests took 695ms 
```

成功了，成功了，很好，现在可以继续开发

## [t1㎡快速测定法](#%E0%B8%AA%E0%B8%A3%E0%B8%B8%E0%B8%9B%E0%B9%81%E0%B8%9A%E0%B8%9A%E0%B8%A3%E0%B8%A7%E0%B8%94%E0%B8%A3%E0%B8%B1%E0%B8%94)

我们希望将 docs API 写入拆分为一个片段，以便更容易地进行扩展。【t0 }但是所选的工具有一些限制。