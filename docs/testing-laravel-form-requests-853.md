# 测试 Laravel 表单请求

> 原文：<https://dev.to/dsazup/testing-laravel-form-requests-853>

我正在努力决定如何在 Laravel 中测试表单请求。具体来说，我想测试验证。我做了一个快速的谷歌搜索，看起来有多种方法可以做到这一点。其中有一篇博文脱颖而出，它是由[@ daaan](https://medium.com/@daaaan)撰写的[以不同方式](https://medium.com/%40daaaan/a-guide-to-unit-testing-laravel-form-requests-in-a-different-way-f1bdb6d86053)对 Laravel 表单请求进行单元测试的指南。他建议我们应该在 PHPUnit 的@dataProviders 的帮助下使用单元测试，而不是编写集成测试和使用多种方法，比如`it_fails_validation_without_title`。

这绝对是我想尝试的事情，因为在过去，我的表单请求有几十种测试方法，每一种都测试单独的请求属性，并且有很多代码重复。

金奎大建议如下:

```
<?php

namespace Tests\Feature\App\Http\Requests;

use App\Http\Requests\SaveProductRequest;
use Faker\Factory;
use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

class SaveProductRequestTest extends TestCase
{
    use RefreshDatabase;

    /** @var \App\Http\Requests\SaveProductRequest */
    private $rules;

    /** @var \Illuminate\Validation\Validator */
    private $validator;

    public function setUp(): void
    {
        parent::setUp();

        $this->validator = app()->get('validator');

        $this->rules = (new SaveProductRequest())->rules();
    }

    public function validationProvider()
    {
        /* WithFaker trait doesn't work in the dataProvider */
        $faker = Factory::create( Factory::DEFAULT_LOCALE);

        return [
            'request_should_fail_when_no_title_is_provided' => [
                'passed' => false,
                'data' => [
                    'price' => $faker->numberBetween(1, 50)
                ]
            ],
            'request_should_fail_when_no_price_is_provided' => [
                'passed' => false,
                'data' => [
                    'title' => $faker->word()
                ]
            ],
            'request_should_fail_when_title_has_more_than_50_characters' => [
                'passed' => false,
                'data' => [
                    'title' => $faker->paragraph()
                ]
            ],
            'request_should_pass_when_data_is_provided' => [
                'passed' => true,
                'data' => [
                    'title' => $faker->word(),
                    'price' => $faker->numberBetween(1, 50)
                ]
            ]
        ];
    }

    /**
     * @test
     * @dataProvider validationProvider
     * @param bool $shouldPass
     * @param array $mockedRequestData
     */
    public function validation_results_as_expected($shouldPass, $mockedRequestData)
    {
        $this->assertEquals(
            $shouldPass, 
            $this->validate($mockedRequestData)
        );
    }

    protected function validate($mockedRequestData)
    {
        return $this->validator
            ->make($mockedRequestData, $this->rules)
            ->passes();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 这是怎么回事？

看起来我们只是提供了一个带有测试方法的数组，我们是否期望它通过，以及一些请求属性。通过这种方式，我们可以拥有任意多的测试方法，PHPunit 会处理剩下的事情。我们给`validation_results_as_expected`方法添加了`@dataProvider`注释，这是断言实际发生的地方。遍历每一项，然后在我们的测试用例中调用`validate`方法。这个方法依次调用我们在`setUp`中定义的`$this->validator`上的`validate`。

# 问题

现在，我真的很喜欢这种方法，但是我觉得我实际上并没有测试我的表单请求。在`setUp`方法中，我们实例化了表单请求，但是我们只从中获取验证规则，并将其传递给 Laravel 的验证器。这可能在大多数情况下适用于简单的数据，但是在我的例子中，我在`prepareForValidation`表单请求方法中进行了一些数据操作。我也用`withValidator`方法做了一些其他的检查。使用这种测试方法，那些方法没有被调用，我也不知道我的表单请求是否如预期的那样工作。

# 解？

我认为我们应该设置实际的表单请求，而不是设置验证器，所以我很快将测试用例中的`validate`方法改为如下所示:

```
protected function validate($mockedRequestData)
{
    return (new CreateRedirectRequest())->...;
} 
```

Enter fullscreen mode Exit fullscreen mode

等一下...我叫什么方法？我如何将数据传递给它？然后我检查了`Illuminate\Foundation\Http\FormRequest`，看起来它扩展了`Illuminate\Http\Request`，而 T1 又扩展了 Symfony `Symfony\Component\HttpFoundation\Request`。好的，看起来表单请求实际上是请求的扩展，我们不想自己设置它。

所以我想我会在容器外解决它。我把 validate 方法改成了这样:

```
protected function validate($mockedRequestData)
{
    app(CreateRedirectRequest::class);
} 
```

Enter fullscreen mode Exit fullscreen mode

我运行了测试，结果立即出现了一个错误。在我的`withValidator`方法中，我是这样做的:

```
public function withValidator($validator)
{
    $validator->after(function ($validator) {
        if (! $this->parse($this->source)) {
            $validator->errors()->add('source', 'Invalid source');
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

错误是`Argument 1 passed to parse() must be of the type string, null given`。我想，这是怎么回事。我们还没有调用验证，但是调用了`withValidator`方法，我甚至还没有向它传递数据。

我再次检查了`FormRequest`类，看起来它正在使用`ValidatesWhenResolved`接口和`ValidatesWhenResolvedTrait`特征。就像它说的那样。当使用该特征的对象被解析出容器时，它将调用`prepareForValidation`，它将检查授权，并最终正确验证请求。

在`FormRequestServiceProvider`中我们可以看到这段代码:

```
 /**
 * Bootstrap the application services.
 *
 * @return void
 */
public function boot()
{
    $this->app->afterResolving(ValidatesWhenResolved::class, function ($resolved) {
        $resolved->validateResolved();
    });

    $this->app->resolving(FormRequest::class, function ($request, $app) {
        $request = FormRequest::createFrom($app['request'], $request);

        $request->setContainer($app)->setRedirector($app->make(Redirector::class));
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，这发生在解析之后，我知道 laravel 也有一个`resolving`回调，所以我们能在解析之前传递我们的数据吗？我又一次把`validate`方法改成了这样:

```
protected function validate($mockedRequestData)
{
    $this->app->resolving(CreateRedirectRequest::class, function ($resolved) use ($mockedRequestData){
        $resolved->merge($mockedRequestData);
    });

    try {
        app(CreateRedirectRequest::class);

        return true;
    } catch (ValidationException $e) {
        return false;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我做了测试，他们都通过了。现在，当我想向这个测试用例添加一个新的测试时，我只需要添加一个新的数组项，其中包含数据以及它是否应该通过。

## 那么为什么会起作用呢？

它之所以有效，是因为当 Laravel 的容器解析表单请求
对象时，它会将保存来自测试数组的数据的`$mockedRequestData`数组合并到请求对象中。

如果它解析成功，我们知道所有的数据都是有效的，否则它将抛出`Illuminate\Validation\ValidationException`，我们试图捕捉它。我们返回 true 或 false，因为我们的`validation_results_as_expected`方法然后将它与测试用例是否应该通过进行比较。我们也不再需要“设置”了。

## 最后的话

如果您正在编写多个测试方法来单独测试每个请求属性，我会说试试这个。也许你会喜欢。我当然更喜欢这种方法。

#### 点

虽然我不是 web 开发新手，但这是我的第一篇博文。如果你想在未来阅读更多这样的博客文章，请随时关注我或留下评论，我欢迎任何反馈或问题。