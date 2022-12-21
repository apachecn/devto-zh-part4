# 使用 Laravel 中的存储库模式进行缓存

> 原文：<https://dev.to/programmingdecoded/using-the-repository-pattern-in-laravel-for-caching-2a86>

不久前，我开始使用 repository 模式来提供一些抽象，这样我就可以在一个中心位置进行有说服力的查询。但是我最近才想到这样做的一个好处是，它可以让你集中处理像缓存查询这样的事情。缓存查询可以省去昂贵的数据库调用。这是我的知识库类获得测验设置的样子:

```
app/Repositories/QuizSettingRepository.php

<?php
namespace App\Repositories;

use \App\Models\QuizSettings;
use \Cache;

class QuizSettingRepository implements QuizSettingRepositoryInterface
{
    public function get($quizid, $keyname)
    {
        $value = false;

        $cacheKey = 'quizsettings' . $quizid . "_" . $keyname;

        if (Cache::has($cacheKey)) {
            $value = Cache::get($cacheKey);

        } else {
            $quizsettings = QuizSettings::where('quizid', '=', $quizid)->get()->keyBy('keyname');

            if(isset($quizsettings[$keyname]))
            {
                $value = $quizsettings[$keyname]->value;
            }

            Cache::put($cacheKey, $value, now()->addSeconds(10));
        }

        return $value;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以更进一步，使用 decorator 模式在一个全局位置实现所有存储库的缓存，如下所述:[https://Matthew Daly . co . uk/blog/2017/03/01/decorating-laravel-repositories/](https://matthewdaly.co.uk/blog/2017/03/01/decorating-laravel-repositories/)