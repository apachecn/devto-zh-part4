# 在 Laravel 中使用 Laravel 图表。

> 原文：<https://dev.to/arielmejiadev/use-laravel-charts-in-laravel-5bbm>

如果你需要在你的视图中添加一些图形，也许你可以使用一些 js 库来添加很酷的图形，但是即使有一个像 ChartJS 这样好的库，实现起来也不是那么容易。

## 安装

* * *

首先，我们需要向您的 Laravel 项目添加一个包，因此在终端中添加:

```
composer require consoletvs/charts:6.* 
```

如果您使用的是 Laravel 5.5 或更高版本，那么您只需安装该软件包，这要归功于自动发现功能。

如果您使用低于 5.5 的 Laravel，您将需要注册一个服务提供者，将这一行添加到 config/app.php 文件的 providers 部分:

```
ConsoleTVs\Charts\ChartsServiceProvider::class, 
```

使用命令
在终端中发布配置

```
php artisan vendor:publish --tag=charts_config 
```

现在您已经完成了包的安装！

## 使用包

* * *

我们将使用 artisan cli 创建一个图表类。

```
php artisan make:chart UserChart 
```

现在在 app 目录中我们可以看到一个名为 charts 的文件夹，这是我们的新类 UserChart.php。

我将用一个简单的例子来解释，但是你可以添加你想要的任何复杂性，我们将创建一个资源类型的控制器来显示用户图表:

```
php artisan make:controller UserChartController -r 
```

现在，您可以在 app/Http/controller/UserChartController . PHP 中编辑该文件，并且只保留索引方法，所有其他完整的方法都可以被删除，您将看到类似这样的内容:

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }

} 
```

为了使它更容易，我将创建一个假的数据，但你可以使用雄辩的 o queryBuilder 来创建你需要的查询，我将把之前创建的新图表类导入到控制器，并开始用 Laravel 图表 api fluid 语法创建一个图表:

```
<?php

namespace App\Http\Controllers;

use App\Charts\UserChart;
use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $usersChart = new UserChart;
        $usersChart->labels(['Jan', 'Feb', 'Mar']);
        $usersChart->dataset('Users by trimester', 'line', [10, 25, 13]);
        return view('users', [ 'usersChart' => $usersChart ] );
    }

} 
```

现在我们需要一个视图来显示最后一段代码中的数据。index 方法返回一个用户图表视图，所以我将在 resources/views/中创建一个名为 users.blade.php 的文件，其下一个内容为:

```
@extends('layouts.app')

@section('content')
<h1>Sales Graphs</h1>

<div style="width: 50%">
    {!! $salesChart->container() !!}
</div>
@endsection 
```

现在，我们将图表脚本传递到视图文件，我们只需要添加图表 css 和 js 库文件，为了简单起见，我们将使用布局应用程序 blade 文件，它位于 resources/views/layout/app . blade . PHP 中，在这里，我们将在最底部的标题部分添加下一行:

```
<head>
    <meta charset="utf-8">
    ...
    {{-- ChartScript --}}
    @if($usersChart)
    {!! $usersChart->script() !!}
    @endif
</head> 
```

要添加 JS 库文件我们将在文件 app.blade.php 的底部，在 html 关闭标签之前添加脚本:

```
@extends('layouts.app')

@section('content')
<h1>Users Graphs</h1>

<div style="width: 50%">
    {!! $usersChart->container() !!}
</div>
@endsection 
```

最后，我们只需要一个路由来访问图形视图，在 routes/web.php 文件中，您可以使用 get 方法添加一个路由来访问方法 index()中的 usersChartController 类。在示例中，我将一个路由设置为“sales”:

```
<?php

/*
|--------------------------------------------------------------------------
| Web Routes
|--------------------------------------------------------------------------
|
| Here is where you can register web routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| contains the "web" middleware group. Now create something great!
|
*/

....

Route::get('users', 'UserChartController@index'); 
```

[![Alt Text](img/80972e99485a51044eb9b6614fa96369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8W7CSCsz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fekbgt62ypv7j6ai42ib.png)

这很简单，但是，也许您想对它进行更多的自定义，您可以通过两种方式自定义图表，您可以自定义“数据集”和图表本身，首先我们要自定义“数据集”:

```
<?php

namespace App\Http\Controllers;

use App\Charts\UserChart;
use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $usersChart = new UserChart;
        $usersChart->labels(['Jan', 'Feb', 'Mar']);
        $usersChart->dataset('Users by trimester', 'line', [10, 25, 13])
            ->color("rgb(255, 99, 132)")
            ->backgroundcolor("rgb(255, 99, 132)");
        return view('users', [ 'usersChart' => $usersChart ] );
    }

} 
```

*   方法“color”设置“线条”或“面积”图表的边框颜色，它设置线条颜色，并且作为参数需要一个具有 rgb 或 rgba 颜色的字符串

*   方法“backgroundcolor”设置区域颜色、要填充的颜色，并且 as param 需要一个具有 rgb 或 rgba 颜色的字符串

[![Alt Text](img/60cb01225f4f545f9e6ec8eaa95bd71c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Op0oreXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mo35tc2upidhi0kcxynh.png)

*   “fill”方法需要一个布尔值，如果它设置为 false，则默认填充图表。

[![Alt Text](img/30c4c2ea917f7f91ea3c94b37a62f319.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UP9cknP3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wnd7z32wmzopttdg9ac1.png)

*   “线张力”方法使线不那么弯曲，它需要一个从 0.0 到 1.0 的浮动

[![Alt Text](img/ca44eb6b39a8bd84055333ba44d66180.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MkOBSyHU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujhmcwrvmmcs6o57y5m8.png)

*   “虚线”方法使线成为虚线，它需要一个数组。

[![Alt Text](img/d33efaad6adbd96b5fcd7ec6ca9db5c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--opovXyCw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/alprcf9quw0hyfpd5zkv.png)

## 自定义图表

*   要自定义图表，我们可以使用一些方法:
    *   “极简”方法需要一个布尔值，它删除表格背景和图表的图例
    *   “displaylegend”方法需要一个布尔值，默认情况下为 true，以隐藏设置为 false 的图例作为参数。
    *   “displayaxes”方法需要一个布尔值，默认情况下为 true，绘制图表的背景网格，要隐藏它，只需将参数设置为 false。
    *   “barWidth”该方法在折线图和面积图中不起任何作用，它需要一个 double。

```
<?php

namespace App\Http\Controllers;

use App\Charts\UserChart;
use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $usersChart = new UserChart;
        $usersChart->title('Users by Months', 30, "rgb(255, 99, 132)", true, 'Helvetica Neue');
        $usersChart->barwidth(0.0);
        $usersChart->displaylegend(false);
        $usersChart->labels(['Jan', 'Feb', 'Mar']);
        $usersChart->dataset('Users by trimester', 'line', [10, 25, 13])
            ->color("rgb(255, 99, 132)")
            ->backgroundcolor("rgb(255, 99, 132)")
            ->fill(false)
            ->linetension(0.1)
            ->dashed([5]);
        return view('users', [ 'usersChart' => $usersChart ] );
    }

} 
```

[![Alt Text](img/54dbd7f0508dd8221d317b3cb3033678.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u_szmEMh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uajg70x2s2up6ofatmug.png)

## 甜甜圈示例:

* * *

```
<?php

namespace App\Http\Controllers;

use App\Charts\UserChart;
use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $borderColors = [
            "rgba(255, 99, 132, 1.0)",
            "rgba(22,160,133, 1.0)",
            "rgba(255, 205, 86, 1.0)",
            "rgba(51,105,232, 1.0)",
            "rgba(244,67,54, 1.0)",
            "rgba(34,198,246, 1.0)",
            "rgba(153, 102, 255, 1.0)",
            "rgba(255, 159, 64, 1.0)",
            "rgba(233,30,99, 1.0)",
            "rgba(205,220,57, 1.0)"
        ];
        $fillColors = [
            "rgba(255, 99, 132, 0.2)",
            "rgba(22,160,133, 0.2)",
            "rgba(255, 205, 86, 0.2)",
            "rgba(51,105,232, 0.2)",
            "rgba(244,67,54, 0.2)",
            "rgba(34,198,246, 0.2)",
            "rgba(153, 102, 255, 0.2)",
            "rgba(255, 159, 64, 0.2)",
            "rgba(233,30,99, 0.2)",
            "rgba(205,220,57, 0.2)"

        ];
        $usersChart = new UserChart;
        $usersChart->minimalist(true);
        $usersChart->labels(['Jan', 'Feb', 'Mar']);
        $usersChart->dataset('Users by trimester', 'doughnut', [10, 25, 13])
            ->color($borderColors)
            ->backgroundcolor($fillColors);
        return view('users', [ 'usersChart' => $usersChart ] );
    }

} 
```

[![Alt Text](img/10a1253653cd3609f4df6cea085dc348.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--py3BmsHg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cu5wpd6cdws5kolq8w3r.png)

## 栏示例

* * *

只需稍加努力，使用 Laravel 安装中的默认引导程序:

### UserChartController 生成图表

```
<?php

namespace App\Http\Controllers;

use App\Charts\UserChart;
use Illuminate\Http\Request;

class UserChartController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $borderColors = [
            "rgba(255, 99, 132, 1.0)",
            "rgba(22,160,133, 1.0)",
            "rgba(255, 205, 86, 1.0)",
            "rgba(51,105,232, 1.0)",
            "rgba(244,67,54, 1.0)",
            "rgba(34,198,246, 1.0)",
            "rgba(153, 102, 255, 1.0)",
            "rgba(255, 159, 64, 1.0)",
            "rgba(233,30,99, 1.0)",
            "rgba(205,220,57, 1.0)"
        ];
        $fillColors = [
            "rgba(255, 99, 132, 0.2)",
            "rgba(22,160,133, 0.2)",
            "rgba(255, 205, 86, 0.2)",
            "rgba(51,105,232, 0.2)",
            "rgba(244,67,54, 0.2)",
            "rgba(34,198,246, 0.2)",
            "rgba(153, 102, 255, 0.2)",
            "rgba(255, 159, 64, 0.2)",
            "rgba(233,30,99, 0.2)",
            "rgba(205,220,57, 0.2)"

        ];
        $usersChart = new UserChart;
        $usersChart->minimalist(true);
        $usersChart->labels(['Jan', 'Feb', 'Mar']);
        $usersChart->dataset('Users by trimester', 'bar', [10, 25, 13])
            ->color($borderColors)
            ->backgroundcolor($fillColors);
        return view('users', [ 'usersChart' => $usersChart ] );
    }

} 
```

### 叶片视图带有一些用于造型的引导程序

```
@extends('layouts.app')

@section('content')

<div class="container">
    <h1>Users Graphs</h1>
    <div class="row">
        <div class="col-6">
            <div class="card rounded">
                <div class="card-body py-3 px-3">
                    {!! $usersChart->container() !!}
                </div>
            </div>
        </div>
    </div>
</div>

@endsection 
```

[![Alt Text](img/4edfd6f227d945fe085011e30f690265.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_CC6fYQt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gn7zlsx5smsmkpe6db4.png)

以下是 Github repo 上的代码:

[https://github.com/ArielMejiaDev/SNIPPET_Laravel-charts](https://github.com/ArielMejiaDev/SNIPPET_Laravel-charts)