# 用 Laravel concertive 为 SQL 原始查询添加时间戳？以下是方法。

> 原文：<https://dev.to/shearytan/adding-timestamps-to-the-sql-raw-queries-with-laravel-eloquent-here-is-how-226c>

## 用巧言令色

当使用 Laravel concertive 将数据添加到数据库中时，如果您在迁移脚本中声明了时间戳列，时间戳通常会自动插入。

迁移脚本是这样的:

```
// create_person_table.php

class CreatePersonTable extends Migration
{
    public function up()
    {
        Schema::create('person', function (Blueprint $table) {
            $table->increments('id');
            $table->string('person_name');

            $table->integer('company_id');
            $table->foreign('company_id')->references('id')->on('companies');

            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down()
    {
        Schema::dropIfExists('person');
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从控制器添加一个新的人:

```
// PersonController.php

namespace App\Http\Controllers;

use App\Person;

class PersonController extends Controller
{
    public function store(Request $request)
    {
        $newPerson = new Person;
        $newPerson->person_name = $request->person_name;
        $newPerson->company_id = $request->company_id;
        $newPerson->save();

        return redirect()
            ->route('members.folder.show', $request->id);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

并且时间戳(`created_at` & `updated_at`)将在数据库中自动为您创建。

* * *

## 使用 SQL 原始查询

但是，如果在某些情况下，您不得不在不使用 Laravel 口才的情况下手动将数据插入数据库，尤其是当您有一个复杂的 SQL 查询时，该怎么办呢？

让我们举一个简单的例子，将上面的例子转换成一个 SQL 查询:

```
// PersonController.php

namespace App\Http\Controllers;

use App\Person;

class PersonController extends Controller
{
    public function store(Request $request)
    {
       $person = DB::select(
            DB::raw("INSERT INTO person (person_name, company_id) VALUES (:personName, :companyId);"),
            array(
                "personName" => $request->person_name,
                "companyId" => $request->company_id.
            )
        );

        return $person;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

*仅供参考:阅读[这篇文章](https://fideloper.com/laravel-raw-queries)，了解用 laravel 编写原始查询的最佳实践，以防止 SQL 注入*

因为我们没有在插入部分声明`created_at`和`updated_at`列，所以您将会在数据库的两列上看到值`null`。

那么，我们如何使用 SQL 原始查询将时间戳手动添加到数据库中呢？我们是简单地硬编码日期还是？

* * *

## 解

```
CURRENT_TIMESTAMP 
```

Enter fullscreen mode Exit fullscreen mode

首先，您必须在插入部分添加`created_at`和`updated_at`列:

```
$person = DB::select(
            DB::raw("INSERT INTO person (person_name, company_id, created_at, updated_at) VALUES (:personName, :companyId);"),
            array(
                "personName" => $request->person_name,
                "companyId" => $request->company_id.
            )
        ); 
```

Enter fullscreen mode Exit fullscreen mode

并将`CURRENT_TIMESTAMP`添加到值部分:

```
$person = DB::select(
            DB::raw("INSERT INTO person (person_name, company_id, created_at, updated_at) VALUES (:personName, :companyId, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);"),
            array(
                "personName" => $request->person_name,
                "companyId" => $request->company_id.
            )
        ); 
```

Enter fullscreen mode Exit fullscreen mode

现在运行您的代码并添加一条记录。然后，您将看到时间戳出现在数据库的`created_at`和`updated_at`列中！