# 跳过特定于环境的 PHPUnit 测试

> 原文：<https://dev.to/matthewbdaly/skipping-environment-specific-phpunit-tests-3c1o>

如果您在做客户端工作，除了那些将安装在您的生产环境中的服务之外，您通常不必太担心使用任何其他服务。例如，如果您使用 Memcached 作为您的缓存后端，您就不必费心检查它是否也能与 Redis 一起工作，除非项目主动切换。然而，对于可能部署到各种不同环境中的更通用的软件，您可能必须在所有这些环境中测试它，这可能是一件麻烦的事情。

最近我一直在为一个个人项目做一个微型 CMS，遇到了一点问题。这个 CMS 使用 Stash 缓存库，我希望它积极支持 Stash 提供的所有缓存后端。CMS 是使用 YAML 配置的，我编写了一个工厂类，它接受缓存配置并返回一个适配器。问题是有三种适配器需要安装额外的软件，即 APC、Redis 和 Memcached 适配器。安装所有的包以使用所有三个适配器是繁重的工作，尽管测试它们是一个好主意，但是通常不值得将它们添加到您的本地开发环境中，因为您需要您的测试尽可能快地运行。相反，您最好推迟那些需要额外依赖于您的持续集成服务器的测试，这可能会慢很多。

幸运的是，PHPUnit 允许您通过调用`markTestSkipped()`将测试标记为跳过。过去，当一个测试没有完成时，我使用过这种或者类似的`markTestIncomplete()`方法，但是它对于根据环境跳过测试也很有用。我们可以测试依赖项的存在，如果不存在就标记为跳过，或者在 try…catch 块中设置测试，如果测试由于缺少依赖项而抛出异常，就调用`markTestSkipped()`，如下例:

```
<?php declare(strict_types = 1);

namespace Tests\Unit\Factories;

use Tests\TestCase;
use App\Factories\CacheFactory;
use Stash\Exception\RuntimeException;
use Mockery as m;

final class CacheFactoryTest extends TestCase
{
    public function testRedis()
    {
        $factory = new CacheFactory;
        try {
            $pool = $factory->make([
                'driver' => 'redis',
                'servers' => [[
                    '127.0.0.1',
                    '6379'
                ]]
            ]);
        } catch (RuntimeException $e) {
            $this->markTestSkipped('Dependency not installed');
        }
        $this->assertInstanceOf('Stash\Pool', $pool);
        $this->assertInstanceOf('Stash\Driver\Redis', $pool->getDriver());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，在本地运行测试时，测试套件的快速运行比提供 100%的覆盖率更重要。速度较慢或者需要安装多个服务的测试仍然可以由您的持续集成服务器来运行，它可以运行得更慢，因为它不是一个阻塞程序。此外，我只对 CI 服务器上的覆盖率统计感兴趣，因为启用它会大大降低 PHPUnit 的速度，所以由于覆盖率在本地不是问题，我们可以很高兴地将这种依赖性留给 CI 服务器。在这种情况下，项目托管在 Github 上，并使用 Travis CI 来运行测试和记录覆盖率，因此我们可以让完整的测试套件在 Travis CI 上运行，以确保完整的覆盖率，同时跳过那些需要 Redis、Memcached 或 APC 的本地测试。

拥有一个全面的测试套件，并在开发过程中定期运行它是很重要的，但这并不意味着你必须定期运行每个测试。在这种情况下，对于相同的基本功能有多个适配器，您通常可以避免运行那些以更严格的要求测试适配器的适配器。