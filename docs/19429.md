# ★重构到行动

> 原文：<https://dev.to/freekmurze/refactoring-to-actions-5fa>

在我们最近在 [Spatie](https://spatie.be) 的项目中，我们已经开始使用一个叫做“动作”的概念。它让我们的控制器和模型保持苗条。这是一种简单的做法。在这篇博文中，我想给你解释一下。

## 来自控制器和模型中的逻辑...

假设您有一个 Laravel powered 博客，您想在其中发布帖子。当一个帖子发布时，应用程序应该发布标题和链接。

这样做的控制器可能是这样的:

```
class PostsController
{
    public function create()
    {
        // ...
    }

    public function store()
    {
        // ...
    }

    public function edit()
    {
        // ...
    }

    public function update()
    {
        // ...
    }

    public function delete()
    {
        // ...
    }

    public function publish(Post $post, TwitterApi $twitterApi)
    {
        $post->markAsPublished();

        $twitterApi->tweet($post->title . PHP_EOL . $post->url);

        flash()->success('Your post has been published!');

        return back();
    }
} 
```

如果你想知道为什么这个控制器没有扩展默认的控制器，请阅读这篇关于简化控制器的博客文章。

对我来说，一个非 crud 动作存在于一个 crud 控制器中有点脏。让我们遵循[亚当](https://github.com/adamwathan/laracon2017)的建议，将`publish`方法放入它自己的控制器中。

```
class PublishPostController
{
    public function __invoke(Post $post, TwitterApi $twitter)
    {
        $post->markAsPublished();

        $twitter->tweet($post->title . PHP_EOL . $post->url);

        flash()->success('Your post has been published!');

        return back();
    }
} 
```

这已经很好了，但是我们可以更进一步。假设您想要创建一个 artisan 命令来发布博客文章。现在，这是不可能的，因为这样做的逻辑是在控制器内部。

为了使逻辑可以从命令(或应用程序中的任何地方)调用，逻辑不应该在控制器中。理想情况下，放在控制器中的唯一代码是处理 HTTP 层的代码。

您可能想将所有这些代码都转移到`Post`模型的`publish`方法中。对于小型项目来说，这很好。但是想象一下，在一篇文章上有更多种类的操作，比如存档或复制。这些动作都会让你的模型变大。

## ...为行动中的逻辑干杯！

不要把这个逻辑留在控制器中或者放在模型中，让我们把它移到一个专用的类中。在 Spatie，我们称这些类为“动作”。

动作是一个非常简单的类。它只有一个公共方法:`execute`。您可以随意命名该方法。

```
namespace App\Actions;

use App\Services\TwitterApi;

class PublishPostAction
{
    /** @var \App\Services\TwitterApi */
    private $twitter;

    public function __construct(TwitterApi $twitter)
    {
        $this->twitter = $twitter;
    }

    public function execute(Post $post)
    {
        $post->markAsPublished();

        $this->tweet($post->title . PHP_EOL . $post->url);
    }

    private function tweet(string $text)
    {
        $this->twitter->tweet($text);
    }
} 
```

注意到在`$post`上调用了`markAsPublished`方法吗？因为我们的应用程序现在有一个专门发布帖子的地方，这个逻辑可以移到这个`PublishPostAction`，使`Post`模型变得更轻。

```
// in PublishPostAction

public function execute(Post $post)
{
  $this->markAsPublished($post);

  $this->tweet($post->title . PHP_EOL . $post->url);
}

private function markAsPublished(Post $post)
{
  $post->published_at = now();

  $post->save();
}

private function tweet(string $text)
{
  $this->twitter->tweet($text);
} 
```

在一个控制器中，你可以这样调用动作:

```
namespace App\Http\Controllers;

use App\Actions\PublishPostAction;

class PublishPostController
{
    public function __invoke(Post $post, PublishPostAction $publishPostAction)
    {
        $publishPostAction->execute($post);

        flash()->success('Hurray, your post has been published!');

        return back();
    }
} 
```

我们使用方法注入来解析`PublishPostAction`,所以 Laravel 的容器会自动将一个`TwitterApi`实例注入到`PublishPostAction`本身中。

工匠指挥官现在也可以使用这个动作了。

```
namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Actions\PublishPostAction;
use App\Models\Post;

class PublishPostCommand extends Command
{
    protected $signature = 'blog:publish-post {postId}';

    protected $description = 'Publish a post';

    public function handle(PublishPostAction $publishPostAction)
    {
        $post = Post::findOrFail($this->argument('postId'));

        $publishPostAction->execute($post);

        $this->comment('The post has been published!');
    }
} 
```

我们从提取到动作中得到的另一个好处是，代码不再依赖于 HTTP 层，现在更易于测试。

```
class PublishPostActionTest extends TestCase
{
    public function setUp(): void
    {
        parent::setUp();

        Carbon::setTestNow(Carbon::createFromFormat('Y-m-d H:i:s', '2019-01-01 01:23:45'));

        TwitterApi::fake();
    }

    /** @test */
    public function it_can_publish_a_post()
    {
        $post = factory(Post::class)->state('unpublished')->create();

        (new PublishPostAction())->execute($post);

        $this->assertEquals('2019-01-01 01:23:45', $post->published_at->format('Y-m-d H:i:s'));

        TweetterApi::assertTweetSent();
    }
} 
```

## 可排队动作

想象你有一个动作，执行一些工作需要一些时间。对此，一个简单的解决方案是创建一个队列作业并从动作中分派该作业。

让我们使用`PublishPostAction`中的一个队列来发送推文。

```
// in PublishPostAction

public function execute(Post $post)
{
    $this->markAsPublished($post);

    $this->tweet($post->title . PHP_EOL . $post->url);
}

private function markAsPublished(Post $post)
{
    $post->published_at = now();

    $post->save();
}

private function tweet(string $text)
{
    dispatch(new SendTweetJob($text));
} 
```

现在，如果您想在应用程序的其他地方发送 tweets。当然你可以这样做，用一个工作:

```
namespace App\Http\Controllers

class SendTweetController
{
    public function __invoke(SendTweetRequest $request)
    {
        dispatch(new TweetJob($request->text);

        flash()->success('The tweet has been sent');

        return back();
    }
} 
```

那会完美地工作。但是如果我们可以对任何事情都使用动作，包括异步工作，这不是很好吗？

进入[我们的 laravel-queueable-action 包](https://github.com/spatie/laravel-queueable-action)。这个包允许你很容易地将动作排队。您可以通过将提供的`QueueableAction`应用于一个动作来使其可排队。该特征增加了一个`onQueue`方法。

```
use Spatie\QueueableAction\QueueableAction;

namespace App\Actions;

class SendTweetAction
{
    use QueueableAction;

    /** @var \App\Services\TwitterApi */
    private $twitter;

    public function __construct(TwitterApi $twitter)
    {
        $this->twitter = $twitter;
    }

    public function execute(string $text)
    {
        $this->twitter->tweet($text);
    }
} 
```

现在我们可以调用这个动作，它将在一个队列上执行它的工作。

```
class SendTweetController
{
    public function __invoke(SendTweetRequest $request, SendTweetAction $sendTweetAction)
    {
        $sendTweetAction->onQueue()->execute($request->text);

        flash()->success('The tweet will be sent very shortly!');

        return back();
    }
} 
```

您还可以通过将队列的名称传递给`onQueue`来指定应该执行工作的队列。

```
$sendTweetAction->onQueue('tweets')->execute($request->text); 
```

如果你想知道更多关于可排队动作的信息，一定要查看我们的[这篇由我的同事和软件包创建者](https://stitcher.io/blog/laravel-queueable-actions) [Brent](https://twitter.com/brendt_gd) 撰写的内容丰富的博客文章。

## 在关闭

将逻辑提取到动作中，可以在应用程序的多个地方调用该动作。这也使得代码更容易测试。如果行动变大了，你可以把它们分成更小的行动。

在 [Spatie](https://spatie.be) ，我们将这个概念命名为“行动”，并使用`execute`方法。你可以随意称呼这个概念和方法。我们没有发明这种做法。已经有很多开发者在使用它了。如果您来自 DDD 世界，您可能会注意到一个动作只是一个命令和它的处理程序包装在一起。

你有任何问题或评论吗？请在下面的评论中告诉我。