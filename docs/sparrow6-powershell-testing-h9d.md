# 用 Tomty 框架测试 Powershell 代码。

> 原文：<https://dev.to/melezhik/sparrow6-powershell-testing-h9d>

> 测试它的方法不止一种...

如何使用 Perl6 和 [Tomty](https://github.com/melezhik/Tomty) 测试 Powershell 模块

# [欢迎，圣诞老人](#welcome-tomty)

Tomty 是一个基于 [Sparrow6](https://github.com/melezhik/Sparrow6) 的测试框架，用 Perl6 编写。我们用它来写测试。

# 安装

```
zef install Tomty 
```

Enter fullscreen mode Exit fullscreen mode

# 用法

## 编写 Powershell 模块

`modules/hello/hello.ps1`

```
function Hello {

  param(
    [Parameter(Mandatory=$true)]
    [String]$name
  )

  Write-Host Hello $name
} 
```

Enter fullscreen mode Exit fullscreen mode

## 写包装材料

```
mkdir -p .tomty/tasks/test-hello 
```

Enter fullscreen mode Exit fullscreen mode

`.tomty/tasks/test-hello/task.ps1`:

```
Import-Module hello
$name = config 'name'
Hello -name $name 
```

Enter fullscreen mode Exit fullscreen mode

`.tom/tasks/test-hello/task.check`:

```
generator: print "Hello ".( config()->{name} ) 
```

Enter fullscreen mode Exit fullscreen mode

## 写测试

`tomty --edit test-hello`

```
task-run ".tomty/tasks/test-hello", %( name => "Sparrow6" ) 
```

Enter fullscreen mode Exit fullscreen mode

## 运行测试

`PSModulePath=$PWD/modules tomty test-hello`

```
02:47:26 07/27/2019 [repository] index updated from file:///home/melezhik/repo/api/v1/index
02:47:26 07/27/2019 [.tom/tasks/test-hello] Hello Sparrow6
[task check] stdout match <Hello Sparrow6> True 
```

Enter fullscreen mode Exit fullscreen mode

## 运行更多测试

一旦你有更多的测试，你运行它们:

`tomty --all`

有关其他选项，请参见 [Tomty](https://github.com/melezhik/Tomty) 文档。

* * *

感谢您的阅读。我很感激你的评论。