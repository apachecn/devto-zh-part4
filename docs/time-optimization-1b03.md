# 时间优化

> 原文：<https://dev.to/kield01/time-optimization-1b03>

## 为什么？

我经常使用一些网络项目，这帮助我在我玩的游戏中找到一些东西。

所以，最近我开始收集横幅，图像，与任务。为了看到横幅，我使用了[入口马赛克](https://ingressmosaik.com)网站。

要获得斯科普斯缟玛瑙勋章，我必须完成 500 次任务，但要想名列前茅，至少要完成 1400 次。因此，为了计算任务和长度，我使用了 Laravel 5.8 的 CLI。

## 我做了什么？

*   用`php artisan make:command`做了一个工匠命令
*   安装了`fabpot/goutte`的依赖项，以发出 HTTPS 请求并有效地处理内容
*   了解了我需要处理的页面的结构
*   去码！

首先，我必须为要从 CLI 执行的命令的名称创建一个别名。我想出了下一个简写:`profile:parser {nickname}`。
30 分钟——我带来了下一个代码。

```
 /** Fetching profile page **/
        $this->http = new Client([
            'base_uri' => 'https://ingressmosaik.com'
        ]);

        /** Transforming the response to the DOM **/
        $dom = new Crawler($this->http->get("/profil/{$this->argument('nickname')}")->getBody()->getContents());
        $todoItems = $dom->filter('div#this_todo > div');

        $mosaics = [];

        /** Fetching no. of the mosaics **/
        $todoItems->each(function (Crawler $node) use (&$mosaics) {
            $link = $node->filter('h5 > a')->attr('href');
            list(, , $no) = explode('/', $link);
            $mosaics[] = intval($no);
        });

        $targets = [
            'cities' => [],
            'missions' => 0,
            'distance' => 0.00
        ];

        collect($mosaics)
            ->each(function (int $id) use (&$targets) {
                /** Fetching mission page **/
                $missionPage = new Crawler($this->http->get("/mosaic/{$id}")->getBody()->getContents());
                $title = $missionPage->filter('div.panel-heading-1 > h4');
                $block = $missionPage->filter('div#mo_img > div.panel-body-1');

                list($title,) = explode(' - ', $title->text());

                $city = $block->filter('div > span')->eq(1)->text();
                $mc = $block->filter('div > span')->eq(6)->text();
                $lt = $block->filter('div > span')->eq(7)->text();
                $startPortal = $missionPage->filter('div#portals > table')->eq(0)
                    ->filter('a')
                    ->eq(1);

                $title = trim($title);

                $this->output->text("Parsing '{$title}'");

                $targets['cities'][$city][] = [
                    'mosaic' => trim($title),
                    'first_portal' => $startPortal->attr('href'),
                    'missions' => intval($mc),
                    'distance' => floatval($lt)
                ];

                $targets['missions'] += intval($mc);
                $targets['distance'] += floatval($lt);
            });

        $targets['distance'] = (float)number_format($targets['distance'], 2);

        foreach ($targets['cities'] as $city => $mosaicsList) {
            $targets['cities'][$city] = collect($mosaicsList)
                ->sortBy('missions', SORT_DESC, true);
        }

        file_put_contents(public_path('missions.json'), json_encode($targets, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE)); 
```

Enter fullscreen mode Exit fullscreen mode

是的，这段代码很难看，因为所有代码都在`handle()`方法中。但是计算所有待办事项并将所有数据保存到 JSON 文件中是一个快速的解决方案。