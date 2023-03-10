# ★点火:Laravel 的新错误页面

> 原文：<https://dev.to/freekmurze/ignition-a-new-error-page-for-laravel-2dgm>

今天在 Laracon EU，Marcel Pociot 和我揭开了 T2 点火的面纱，这是 Laravel 的一个美丽的新错误页面。这是 Laravel 6 中新的默认错误屏幕，您可以手动将其安装到 Laravel 5 应用程序中。在这篇博文中，我想告诉你这一切。

## 起动点火

我和我的团队喜欢[创造包装](https://spatie.be/open-source/packages)。但是我们并不是唯一积极为 Laravel 和 PHP 社区创建开源内容的人。我的朋友 [Marcel Pociot](https://twitter.com/marcelpociot) 和他的公司 [Beyond Code](https://beyondco.de/) 也为[做出了很大贡献](https://github.com/beyondcode)。我们的团队总共制作了 300 多个软件包，下载量超过 5000 万次。

我和 Marcel 不时会寻求一些反馈，互相帮助。去年，马塞尔和我甚至一起制作了一个包: [laravel-websockets](https://docs.beyondco.de/laravel-websockets/) 。这个包提供了一个完整的服务器端 websockets 实现。它是 Pusher 的替代产品。

作为阿朵，我们在编程上有很多乐趣，所以我们一起再做一个也是合乎逻辑的。我们有几个想法。其中之一是错误报告。

那时，我参观了我的好友[史蒂文·范德维德](https://twitter.com/icidasset)在榆树上举办的[研讨会。让我印象深刻的是 Elm 有非常好的错误屏幕。](https://www.eventbrite.com/e/womencodebe-elm-workshop-tickets-54052124359?utm_source=eb_email&utm_medium=email&utm_campaign=reminder_attendees_48hour_email&utm_term=eventname)

这让我们相信，我们也可以改善 Laravel 生态系统的错误屏幕。很早的时候，Marcel 和我就知道，如果我们想正确地解决这个问题，我们需要大量的时间和团队的帮助。因此，我们与所有相关人员讨论了很多，一起成立了一家新公司(名为 Facade ),并开始与我们的团队成员合作。

马塞尔和我可能是公众人物，但如果没有我们的团队，我们不可能做到这一点。他们也提出了很多好主意。这是所有 Spatie 团队成员的 twitter 列表，这是 Beyond Code 的 T2 的 twitter 账户。确保遵循它们。

我们创建了一个新的错误跟踪服务，名为 Flare(你可以在这篇博文的[中了解更多信息)和一个漂亮的新错误页面，名为 Ignition。](https://freek.dev/1442-flare-a-new-error-tracker-built-for-laravel-apps)

在这篇博文中，我将告诉你所有关于点火的知识。

## 从榆树到点火

我们来看看 Elm 的几个错误画面。请注意，Elm 中的错误屏幕不仅关注错误，还关注解决方案。

[![Elm error](img/d6c578a8f854a878ec96ed8a2ce281a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R-T2GBVM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/elm1.png)

让我们看看另一个。这个提供了文档的链接。

[![Elm error](img/0aca729751ce2831dbd4d819feed3151.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kg8KyIRq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/elm2.png)

现在让我们看看 PHP 默认情况下都有什么。没有框架，PHP 给你提供了这个。您只会得到错误:没有堆栈跟踪，没有请求或应用程序细节。

[![PHP error](img/4466e1acb464ff57533c24dc00a7e1aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ffv9mWK2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/php-error.png)

Symfony 的错误页面稍微好一点，它显示了堆栈跟踪，但不是很有帮助。

[![Symfony error](img/29cb5ed07aed8dc6b3ccd3ff9519fe1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---jrRhqma--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/symfony-error.png)

下面的截图是呜呜，是 Laravel 5 中的标配。这比 Symfony 的默认设置好得多，向您显示堆栈跟踪和请求的一些信息。尽管呜呜是 Laravel 的默认设置，但它是一个框架无关的屏幕。它只显示一般信息。

[![Whoops error](img/765771cd5866e6fe4e982712477ae31b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eyuQwREj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/whoops-error.png)

这是 Ignition 的屏幕截图，我们创建的新错误屏幕。因为是 Laravel 特有的，我们可以做很多很酷的事情。

[![Ignition error](img/3ac471aff32c041a2a17b7a817d0a308.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qrS-7C2M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/ignition-error.png)

## 探索点火

让我们一起来探索点火中所有的巧妙之处。它是开源的，你可以在这里探索代码[。](https://github.com/facade/ignition)

如果您的视图中有错误，这就是 ouws 显示它们的方式。请注意，异常消息不适合分配的空间。你必须悬停在它上面才能完全看到它。在堆栈跟踪中，您可以看到使用了已编译的刀片视图和内容。这使得很难跟踪哪个刀片视图文件包含错误，并且视图内容本身是不可读的。

[![Whoops view error](img/6b581e8a66e9b458a0e44139bf815a62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7yLjr6a8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/view-error-whoops.png)

点火是一个 Laravel 特定的错误页面。因此，它可以挂钩到框架中来显示未编译的视图路径和您的刀片视图。顶部也有足够的空间来显示整个异常页面，不需要额外的点击。默认情况下，我们只显示应用程序框架，因为这些框架可能是您感兴趣的。

[![Ignition view error](img/3bf5219d72d6fc303f7c1f910bb70870.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l5hobCjL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/view-error-ignition.png)

如果您单击堆栈跟踪选项卡右侧文件名旁边铅笔图标，我们将自动在您喜欢的编辑器中打开该文件。默认情况下，它是 PhpStorm。您可以在`ignition`配置文件中将其配置为您最喜欢的编辑器。

注意到右上角的小“望远镜”链接了吗？只有当你安装了第一方调试助手 [Laravel Telescope](https://laravel.com/docs/5.8/telescope) 时，我们才会显示出来。如果您单击该链接，您将被带到望远镜中发生错误的异常。

## 黑暗模式

如果我们的默认错误屏幕对你来说太亮了，你会很高兴知道我们的错误页面也有一个黑暗的模式。

[![Dark mode](img/6f48c374a1a31b1ac4864dbd32104055.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7147tQZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/dark-mode.png)

## 点火标签

让我们研究一下点火页面上显示的选项卡。

### 请求页签

[![Request tab](img/4c27b0aee74e5c54d267e392580c590d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4GdCesR1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/request-tab.png)

在“堆栈跟踪”选项卡旁边，您会看到“请求”选项卡。它显示了您所期望的关于请求的所有信息。

### app 标签页

[![App tab](img/3196c71574ad8869f398a117649ec048.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O7MHiZQZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/app-tab.png)

“应用”标签是它变得更有趣的地方。此选项卡显示有关您的应用程序的特定信息。首先，我们向您显示关于发生异常的路由的信息。这包括从当前请求执行的控制器(如果可用)以及路由名称。

另一个很酷的功能是能够检查你的路线参数。

假设您有这样一个路由定义:

```
Route::get('/posts/{post}', function (Post $post) {
    //
}); 
```

当您在这条路线上遇到异常时，我们将向您显示点火中的`post`模型的`toArray`表示，作为您路线参数的一部分。不需要任何绑定的“简单”路由参数也是如此。这是一个非常好的方法，可以很容易地看到 Laravel 在这条特定的路线上收到了什么信息。

在 route 参数之后，我们还向您显示了这个请求中使用的中间件的列表。

接下来是“视图”部分。如果异常发生在视图中，我们将在这里显示视图名称。甚至更多:我们还会给你一个传递给视图的所有数据的列表。

### 用户标签页

[![User tab](img/2d897099660e9e3b4da36eaf3d1cb66f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5JoorcUa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/user-tab.png)

“用户”标签包含关于谁在使用该应用程序以及使用哪个浏览器的更多信息。

### 上下文选项卡

[![Context tab](img/58cedfcd76d3b20f3ae45c25371796b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K99pz-kI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/context-tab.png)

在“Context”选项卡中，我们显示关于您的 repo 的信息(repo 位于哪里，checkout commit hash 是什么)和环境信息(您使用的是哪个版本的 PHP 和 Laravel)。

### 调试选项卡

[![Debug tab](img/2c84f336ab9822a5755bff789266d77b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9zkc7DY7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/debug-tab.png)

在“调试”选项卡中，我们向您显示在异常发生之前发生的事情。如查询、日志和转储。在转储旁边，我们还显示了放置该`dump`语句的文件名。点击铅笔图标，你就可以在你最喜欢的编辑器中找到那个文件和正确的行号。

## 暗示解决方案

让我们来看看另一个错误。这一次我们将忘记导入一个类。这是这样一个错误的点火页面的样子。

所以 Ignition 看到异常是关于一个没有找到的类。它将试图判断在另一个名称空间中是否有同名的类。如果有，它会建议进口。

[![Missing import solution](img/3504219beb25b0e82a4d24213aad2ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2kb4Tr4m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/import-missing-error.png)

Ignition 附带了一系列针对常见问题的解决方案。这里有一个是在找不到刀片视图时使用的。

[![Invalid view](img/7906b7f4b8566488335e3bfe51257ea7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nb_4SD-3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/invalid-view.png)

您还可以向例外添加解决方案建议。您应该让您的异常实现`Facade\IgnitionContracts\ProvidesSolutions`实现。这将需要您添加一个`getSolution`函数。这里有一个可能的实现。

```
namespace App\Exceptions;

use Exception;
use Facade\IgnitionContracts\Solution;
use Facade\IgnitionContracts\BaseSolution;
use Facade\IgnitionContracts\ProvidesSolution;

class CustomException extends Exception implements ProvidesSolution
{
    public function getSolution(): Solution
    {
          return BaseSolution::create("You're doing it wrong")
            ->setSolutionDescription('You are obviously doing something wrong. Check your code and try again.')
            ->setDocumentationLinks([
                'Laracasts' => 'https://laracasts.com',
                'Use Flare' => 'https://flareapp.io',
            ]);
    }
} 
```

这就是在 Ignition 中抛出异常的方式。

[![Custom solution](img/b0d379756d1a57d1e1f392b96ed97797.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Zz3V2si7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/custom-solution.png)

## 运行方案

除了建议解决方案，我们还可以运行它们？。想象一下，例如，你忘记设置你的应用程序密钥。这是点火过程中的错误。

[![App key missing](img/170e927e015c141adbb54331ba903270.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vDR1K9OC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/app-key-missing.png)

如果您点击“生成应用密钥”按钮，我们将在后台生成并设置应用密钥。

[![App key generated](img/491c91e5aa5997d1074d4ae2c4cd350b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9iP0CEbf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/app-key-generated.png)

刷新页面后，您的应用程序将工作(除非它包含其他异常)。

在这个视频中，你可以看到几个可行的解决方案。我们将尝试保存一个模型，但是我们忘记了生成 app key 和创建一个 DB。这些解决方案将生成一个应用程序，将正确的数据库凭据放在适当的位置，并运行我们的迁移。

您可以通过让您的异常实现`Facade\IgnitionContracts\ProvidesSolution`来创建您的可运行解决方案，就像非可运行解决方案一样。`getSolution`方法可以返回可运行和不可运行的解决方案。

```
namespace App\Exceptions;

use Exception;
use Facade\IgnitionContracts\ProvidesSolution;

class CustomException extends Exception implements ProvidesSolution
{
    public function getSolution(): Solution
    {
          return new MyRunnableSolution();
    }
} 
```

下面是`MyRunnableSolution`的示例实现。

```
namespace App\Solutions;

use Facade\IgnitionContracts\RunnableSolution;

class MyRunnableSolution implements RunnableSolution
{
    public function getSolutionTitle(): string
    {
        return 'You are doing it wrong';
    }

    public function getSolutionDescription(): string
    {
        return 'You are doing something wrong, but we can fix it for you.';
    }

    public function getDocumentationLinks(): array
    {
        return [];
    }

    public function getSolutionActionDescription(): string
    {
        return 'To fix this issue, all you need to do is press the button below.';
    }

    public function getRunButtonText(): string
    {
        return 'Fix this for me';
    }

    public function run(array $parameters = [])
    {
        // Your solution implementation
    }

    public function getRunParameters(): array
    {
        return [];
    }
} 
```

这就是点火时投掷`CustomException`的样子。

[![Custom runnable solution](img/a876c4c5ff4f3044b39340c0ed9cf480.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VdU4Q-eE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/custom-runnable-solution.png)

当用户点击`Fix this for me`按钮时，将执行`run`功能。

您可以将参数从发生异常的请求传递到将运行解决方案的请求。就让`getRunParameters`返回一个数组。该数组将被传递给`run`。

## 让点火更智能

因此，您可以通过使用文本或可运行的解决方案来增强自己的异常。但是有时为内置的 PHP 异常甚至第三方异常提供解决方案会很好，因为您无法控制代码。

我们允许您使用“解决方案提供商”来完成这项工作。解决方案提供者是从开始就可以挂钩到解决方案查找过程的类。当抛出一个异常并且 Ignition 接收到它时，可以调用您的定制解决方案提供者来返回这个异常的一个或多个可能的解决方案。

例如，您可以创建一个自定义的“堆栈溢出”解决方案提供程序，它将尝试为给定的异常找到匹配的堆栈溢出答案，并将它们作为解决方案返回。

我们还利用点火本身的解决方案提供商。下面是一个这样的解决方案提供商的样子:

```
use Throwable;
use RuntimeException;
use Facade\IgnitionContracts\Solution;
use Facade\Ignition\Solutions\GenerateAppKeySolution;
use Facade\IgnitionContracts\HasSolutionForThrowable;

class MissingAppKeySolutionProvider implements HasSolutionForThrowable
{
    public function canSolve(Throwable $throwable): bool
    {
        if (! $throwable instanceof RuntimeException) {
            return false;
        }

        return $throwable->getMessage() === 'No application encryption key has been specified.';
    }

    public function getSolutions(Throwable $throwable): array
    {
        return [
            new GenerateAppKeySolution()
        ];
    }
} 
```

这些解决方案提供商可以像这样在 Ignition 中自动注册:

```
namespace App\Providers;

use App\Solutions\GenerateAppKeySolution;
use Facade\IgnitionContracts\SolutionProviderRepository;
use Illuminate\Support\ServiceProvider;

class YourServiceProvider extends ServiceProvider
{
    /**
     * Register services.
     *
     * @return void
     */
    public function register(SolutionProviderRepository $solutionProviderRepository)
    {
        $solutionProviderRepository->registerSolutionProvider(GenerateAppKeySolution::class);
    }
} 
```

就像这样，解决方案提供商将继续提高点火能力，为您的例外情况提供解决方案，我们迫不及待地想看到社区拿出什么！

## 定制点火

我们让点火变得可扩展。您可以添加新选项卡或替换默认选项卡。

让我们看看我们提供的[外观/点火修补标签](https://ignition-tinker-tab)。这个包是一个关于[spatie/laravel-we b-tinker](https://github.com/spatie/laravel-web-tinker)的包装器，允许你在浏览器中使用 Artisan tinker。

安装了[门面/点火修补标签](https://ignition-tinker-tab)后，你可以在你的错误页面上使用工匠修补。

[![Tinker tab](img/93e8b9806b590b5efe39d8660eb796a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1ATqtYd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/ignition-tinker-tab.png)

我们还创建了第二个包，叫做[facade/ignition-code-editor](https://github.com/facade/ignition-code-editor)。这个标签取代了默认的 stack trace 标签，用一个定制的标签允许你在错误屏幕上编辑你的代码。这就是它的作用。

[![Code editor](img/209ad9c182557da3bccb630ad5bc4ea1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JP2ikFJ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://freek.dev/uploads/media/ignition-2019/editor.gif)

要了解如何添加自定义选项卡，请访问[关于添加选项卡的文档](https://flareapp.io/docs/ignition-for-laravel/adding-custom-tabs)。

## 在 Flare 上共享本地错误

错误页面的一个很好的特性是能够与他人分享错误。在“调试”标签旁边，有一个共享按钮。点击它，会弹出一个菜单，允许你选择要分享的信息。

[![Share dialog](img/a74350ec70ec36b60c4897e2cc0e5c65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JoVkQ9tC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/share-dialog.png)

如果你点击它，你会看到两个网址。

[![Shared error dialog](img/4a3acb1ed071227c3ef4076cc8058ce0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cXq9pH_8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/shared-error-dialog.png)

公共分享链接是为了与任何你想分享你的错误的人分享。你应该把管理链接留给自己。您可以使用该链接删除共享错误。

这就是共享错误的样子。

[![Shared error](img/92fa84a49b6c5b3a86c5551bcd37b9a8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FADnNjLV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/shared-error.png)

## 向 Flare 报告所有错误

在上面的截图中，您可能已经注意到共享错误位于[flare prep . io](https://flareapp.io)中。Flare 是我们与 Ignition 一起推出的新型 Laravel 特定异常跟踪器。

Ignition 带来的大多数功能对于 Flare 也是有效的。如果您向 flare 报告有解决方案的异常错误，我们也会在 Flare 上显示该解决方案。

[![Shared error](img/0038bf141cac53149500b7149203eb34.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o9haIw0n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freek.dev/uploads/media/ignition-2019/solution.png)

要了解关于 Flare 的更多信息，请阅读这篇博文。

## 关闭思绪

如果你想看看错误页面是如何工作的，请前往 GitHub 上的[点火报告。软件包的自述文件位于 Flare](https://github.com/facade/ignition) 的[文档页面上。](https://flareapp.io/docs/ignition-for-laravel/introduction)

我们和一群非常有才华的人一起在这个项目上工作了大约八个月。在我们看来，这个错误页面是对您在 Laravel 4 和 5 中使用的呜呜页面的重大改进。我们希望你和我们一样喜欢它。