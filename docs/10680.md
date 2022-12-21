# PHP 上的业务规则引擎库

> 原文：<https://dev.to/prohalexey/business-rules-engine-library-on-php-eih>

几个月前，我写了一个实现 BRE(业务规则引擎)的库。

这个图书馆是做什么用的？

该库允许您简化业务流程规则的编写，例如复杂的折扣计算或向客户发放奖金或任何其他逻辑。

如果您经常更改代码中的某些条件，这对您会很有用。

这个库提供了什么功能？

它允许您将这些条件移动到配置文件中，或者创建可以编辑配置的 web 界面。

您可以用 JSON 或 YAML 格式编写规则，并将它们存储到文件或某个数据库中。

Yaml 配置示例

```
node: condition
if:
  node: collection
  type: and
  elements:
  - node: context
    context: withdrawalCount
    operator: equal
    value: 0
  - node: context
    context: inGroup
    operator: arrayContain
    value:
      - testgroup
      - testgroup2
then:
  node: context
  context: getDepositSum
  description: "Giving 10% of deposit's sum as discount for the next order"
  modifiers: 
    - "$context * 0.1"
  params:
    discountType: "VIP client"
else:
  node: value
  description: "Giving 5% for the next order"
  value: 5 
```

PHP 中的进程规则

```
// Create a parser 
$parser = new YamlBuilder(new OperatorFactory());

// Load rules from a file or other sources
$node = $parser->parseFile('Yaml/testOneNodeWithRuleGreaterThan.yaml');

// Define contexts
$contextFactory = new ContextFactory([
    'getDepositSum' => InGroup::class,
    'withdrawalCount' => WithdrawalCount::class,
    'depositCount' => DepositCount::class,
    'utmSource' => UtmSource::class,
]);

// Here you can use PSR-11 container to resolve objects, callable or just class names
$contextFactory->setContainer($container);

// Instantiating tree(rules) processor
$treeProcessor = (new TreeProcessor())->setContextFactory($contextFactory);

// And process the rules
$result = $treeProcessor->process($node); 
```

如果你懂 PHP，你有一点时间，也许你对这样一个库感兴趣——给我的代码留下评论。

参见[https://github.com/prohalexey/TheChoice](https://github.com/prohalexey/TheChoice)上的完整代码