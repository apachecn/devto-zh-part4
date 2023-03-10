# 单元测试 Zend 框架表单

> 原文：<https://dev.to/ethanbray/unit-testing-zend-framework-forms-m1h>

Zend Framework [关于单元测试的文档](https://docs.zendframework.com/tutorials/unit-testing/)关注于测试控制器，而不是诸如表单、过滤器或验证器之类的组件。

测试这些组件对于捕捉 web 应用程序中关键表单行为的错误和回归是至关重要的。

可能不清楚如何以最不脆弱的方式测试这些组件。具有特殊行为的元素，比如文件上传，测试起来可能特别棘手。

## 可测试的行为

可以测试表单的几个部分。可以编写测试来确保表单及其元素配置正确，并确保过滤和验证行为正确。

可以通过简单地实例化该类来测试表单和过滤器的构造。然后，您可以测试单个元素的特定配置选项，如它们的类型、标签或所需状态。任何选项或属性都可以通过使用表单元素上的`getOption`或`getAttribute`方法来检索。

```
use Foo\Bar\ExampleForm;
use PHPUnit\Framework\TestCase;
use Zend\Form\Element\Text;
use Zend\Form\Element\Password;

class ExampleFormTest extends TestCase {
    public function test_username_configuration(): void
    {
        $exampleForm = new ExampleForm();   
        $usernameInput = $exampleForm->get('username');

        $this->assertInstanceOf(Text::class, $usernameInput);
        $this->assertEquals('Username: ', $usernameInput->getOption('label'));
        $this->assertTrue($usernameInput->isRequired());
    }

    public function test_password_configuration(): void
    {
        $exampleForm = new ExampleForm();   
        $passwordInput = $exampleForm->get('password');

        $this->assertInstanceOf(Password::class, $passwordInput);
        $this->assertEquals('Password: ', $passwordInput->getOption('label'));
        $this->assertTrue($passwordInput->isRequired());
    }
} 
```

可以通过实例化输入过滤器、设置测试数据，然后检索您正在测试的特定验证器来测试过滤和验证。然后，您可以断言验证是否成功、错误消息是什么以及过滤后的输入值是什么。

```
use Foo\Bar\ExampleFormFilter;
use PHPUnit\Framework\TestCase;

class ExampleFormFilterTest extends TestCase {
    public function test_username_validator(): void
    {
        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['username' => 'foobar']);
        $validator = $formFilter->get('username');

        $this->assertTrue($validator->isValid());
        $this->assertEquals([], $validator->getMessages());
    }

    public function test_username_filter(): void
    {
        // For this test, we're testing that our input is having whitespace trimmed
        // and any tags removed

        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['username' => ' foobar <script>']);
        $validator = $formFilter->get('username');

        $this->assertTrue($validator->isValid());
        $this->assertEquals('foobar', $validator->getValue());
    }
} 
```

我建议使用一个数据提供者来尝试不同值的数组。这将有助于减少您必须编写的重复测试的数量。

```
use Foo\Bar\ExampleFormFilter;
use PHPUnit\Framework\TestCase;

class ExampleFormFilterTest extends TestCase {
    /**
     * @dataProvider getUsernameData
     */
    public function test_username_validator($username, bool $expected, array $messages): void
    {
        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['username' => $username]);
        $validator = $formFilter->get('username');

        $this->assertEquals($expected, $validator->isValid());
        $this->assertEquals($messages, $validator->getMessages());
    }

    private function getUsernameData(): array
    {
        return [
            ['user', true, []],
            ['User', false, ['username' => ['notLowercase' => 'Username must be lower case']]],
            ['1234', true, []],
            ['', false, ['username' => ['isEmpty' => 'Username is required.']]],
            ['^9bhy&', false, ['username' => [
                'notAlnum' => 'Username not valid. Use only alphanumeric characters.',
            ]]],
            ['bl ah', false, ['username' => [
                'notAlnum' => 'Username not valid. Use only alphanumeric characters.',
            ]]],
            [000, false, ['username' => ['stringLengthTooShort' => 'Username not valid. Minimum length 4 characters.']]],
            ['a', false, ['username' => ['stringLengthTooShort' => 'Username not valid. Minimum length 4 characters.']]],
            ['123456789012345678901234567890123456789012345678900', false, ['username' => [
                'stringLengthTooLong' => 'Username not valid. Maximum length 15 characters.']]],
        ];
    }
} 
```

## 测试文件元素和验证器

由于
更复杂的验证器，如`Size`或`WordCount`，文件上传等测试元素可能会稍微复杂一些。

您可以通过使用提交到存储库中的示例文件来测试这些验证器。然而，这会使您的存储库因大量文件而膨胀。

我们可以使用 [vfsStream](https://github.com/bovigo/vfsStream) 作为虚拟文件系统的流包装器。这允许我们用程序创建虚拟文件，然后在我们的测试中使用。

```
use Foo\Bar\ExampleFormFilter;
use org\bovigo\vfs\content\LargeFileContent;
use org\bovigo\vfs\vfsStream;
use PHPUnit\Framework\TestCase;

class ExampleFormFilterTest extends TestCase {
    public function test_file_size_validator_fails(): void
    {
        $exampleFile = vfsStream::newFile('example_file.csv')
            ->withContent(LargeFileContent::withMegabytes(2));

        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['file' => $exampleFile]);
        $validator = $formFilter->get('file');

        $this->assertFalse($validator->isValid());
        $this->assertEquals(
            ['size' => 'File size must be under 1MB'],
            $validator->getMessages()
        );
    }

    public function test_file_size_validator_passes(): void
    {
        $exampleFile = vfsStream::newFile('example_file.csv')
            ->withContent(LargeFileContent::withMegabytes(1));

        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['file' => $exampleFile]);
        $validator = $formFilter->get('file');

        $this->assertTrue($validator->isValid());
        $this->assertEquals([], $validator->getMessages());
    }

    public function test_file_extension_must_be_csv(): void
    {
        $exampleFile = vfsStream::newFile('example_file.zip')
            ->withContent(LargeFileContent::withMegabytes(1));

        $formFilter = new ExampleFormFilter();
        $formFilter->setData(['file' => $exampleFile]);
        $validator = $formFilter->get('file');

        $this->assertFalse($validator->isValid());
        $this->assertEquals(
            ['fileExtensionFalse' => 'File has an incorrect extension'],
            $validator->getMessages()
        );
    }

    public function setUp(): void
    {
        vfsStream::setup();
    }
} 
```

虚拟文件可以像普通文件一样对待，允许您轻松地测试诸如`WordCount`之类的验证器。vfsStream 的文档可以在这里找到[。](https://github.com/bovigo/vfsStream/wiki)