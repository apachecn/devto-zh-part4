# PHP 包的 Github 动作

> 原文：<https://dev.to/shivammathur/github-action-for-php-packages-2pii>

GitHub 最近推出了 [GitHub Actions](https://github.com/features/actions) ，它允许应用程序基于`yml`文件中定义的工作流在 GitHub 本身中进行 CI/CD，该文件与`.github/workflows`中的代码共存，就像如果你使用 Travis CI，你会有一个`.travis.yml`文件。

任何想要测试 PHP 包的人都可以使用 [setup-php](https://github.com/shivammathur/setup-php) 动作，运行测试并执行 CI/CD。该操作设置 PHP，以及所需的扩展、工具和配置。setup-php 动作适用于所有虚拟环境，即 GitHub actions 支持的`linux`、`macOS`和`windows`。更多信息请查看 [PHP 动作](https://github.com/shivammathur/setup-php)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[设置-php](https://github.com/shivammathur/setup-php)

### GitHub action 使用所需的扩展、php.ini 配置、代码覆盖支持和各种工具(如 composer)设置 PHP...

这里有一个你如何使用它的例子

```
name: Test
on: [push]
jobs:
  build:
    strategy:
      matrix:
        operating-system: [ubuntu-latest, windows-latest, macos-latest]
        php-versions: ['7.2', '7.3', '7.4']
    runs-on: ${{ matrix.operating-system }}
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: ${{ matrix.php-versions }}
          extensions: mbstring, intl
          ini-values: post_max_size=256M, log_errors=1
          coverage: pcov
          tools: pecl
      .... Add more steps to install dependencies and then test 
```

Enter fullscreen mode Exit fullscreen mode