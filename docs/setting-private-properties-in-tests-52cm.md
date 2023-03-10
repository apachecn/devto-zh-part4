# 在测试中设置私有属性

> 原文：<https://dev.to/matthewbdaly/setting-private-properties-in-tests-52cm>

有时候当你写一个测试时，你会遇到这样的情况，你需要设置一个私有的字段，而这个字段不能通过任何现有的途径来访问。例如，我最近一直在研究教义，因为实体的 ID 是自动生成的，所以不可能通过 setter 来更改它，但同时，我们有时需要在测试中设置它。

幸运的是，有一种方法可以做到这一点。使用 PHP 的反射 API，您可以临时将对象上的属性标记为可访问的，以便能够设置它，而无需将它传递给构造函数或创建一个 setter 方法，该方法只在测试中使用。我们首先从对象创建一个`ReflectionClass`实例，然后获取属性。我们将其标记为可访问，然后在实例上设置值，如下所示:

```
<?php

declare(strict_types = 1);

namespace Tests\Unit;

use Tests\TestCase;
use Project\Document;
use ReflectionClass;

final class DocumentTest extends TestCase
{
    public function testGetId()
    {
        $doc = new Document();
        $reflect = new ReflectionClass($doc);
        $id = $reflect->getProperty('id');
        $id->setAccessible(true);
        $id->setValue($doc, 1);
        $this->assertEquals(1, $doc->getId());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你可能不止在一个地方需要这个，你可能想把这个功能提取出来作为一个特征来重用:

```
<?php

declare(strict_types = 1);

namespace Tests\Traits;

use ReflectionClass;

trait SetsPrivateProperties
{
    /**
     * Sets a private property
     *
     * @param mixed $object
     * @param string $property
     * @param mixed $value
     * @return void
     */
    public function setPrivateProperty($object, string $property, $value)
    {
        $reflect = new ReflectionClass($object);
        $prop = $reflect->getProperty($property);
        $prop->setAccessible(true);
        $prop->setValue($object, $value);
        $prop->setAccessible(false);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

那么你的测试可以简化如下:

```
<?php

declare(strict_types = 1);

namespace Tests\Unit;

use Tests\TestCase;
use Project\Document;
use Tests\Traits\SetsPrivateProperties;

final class DocumentTest extends TestCase
{
    use SetsPrivateProperties;

    public function testGetId()
    {
        $doc = new Document();
        $this->setPrivateProperty($doc, 'id', 1);
        $this->assertEquals(1, $doc->getId());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这是一个略显做作和有限的示例，而且这种情况非常罕见，但我发现在某些情况下这是一种有用的技术。