# 使用 Git 挂钩执行常规提交

> 原文：<https://dev.to/craicoverflow/enforcing-conventional-commits-using-git-hooks-1o5p>

我是[传统承诺](https://www.conventionalcommits.org)的超级粉丝。这个相对年轻的规范为创建一个明确的、标准化的提交历史提供了一套规则。

我已经尽力在我所有的项目中遵循惯例。当我的咖啡因越来越少时，我有时会忘记遵守惯例，或者让一个错别字从我眼前溜走。为了解决这个问题，我决定创建一个动态的`commit-msg` Git 钩子，它自动验证我的所有提交消息，并防止它们在配置文件中的任何条件失败时被提交。

## 创建钩子

**注意:**为了让钩子从 JSON 文件中读取配置值，你需要安装 [jq](https://stedolan.github.io/jq/) 。

在您的 Git 项目中，通过重命名已经存在的示例脚本，在`.git/hooks`中添加一个`commit-msg`钩子。

```
$ mv .git/hooks/commit-msg.sample .git/hooks/commit-msg 
```

首先，让我们从一个配置文件中读取配置值，该文件稍后将被添加到项目的根目录中。

```
config=commit-msg.config.json

# set variables
enabled=$(jq -r .enabled $config)
revert=$(jq -r .revert $config)
types=($(jq -r '.types[]' $config))
min_length=$(jq -r .length.min $config)
max_length=$(jq -r .length.max $config) 
```

如果配置文件不存在，或者如果`enabled`属性为假，我们可以退出钩子并跳过验证。

```
if [[ ! -f $config || ! $enabled ]]; then exit 0
fi 
```

## 构建动态正则表达式字符串

让我们用一个帽子符号(`^`)开始正则表达式，它表示字符串的开始。

```
regexp="^(" 
```

如果`$revert`被设置为 true，提交消息可以选择以“revert:”作为前缀。

```
if $revert; then regexp="${regexp}revert: )?(\w+)("
fi 
```

让我们从`commit-msg.config.json`中获取所有允许的类型。

```
for type in "${types[@]}"
do regexp="${regexp}$type|"
done 
```

下一个作品做了两件事。如果左括号紧跟在类型后面，它必须是封闭的，并且包含一个范围。第二件事是类型和/或范围后面必须跟一个冒号(:)。

```
regexp="${regexp})(\(.+\))?: " 
```

这里设置了紧跟在冒号后面的消息的最大和最小长度。

```
regexp="${regexp}.{$min_length,$max_length}$" 
```

### 验证提交消息

现在正则表达式已经构建好了，让我们看一些提交消息的例子。

```
revert: docs: updated table of contents
feat(user): added constructor to model
fix: incorrect URL in link 
```

获取提交消息的第一行:

```
msg=$(head -1 $1) 
```

在下面的语句中，我们检查提交消息是否通过了正则表达式验证。如果失败，打印出定制的错误消息并退出脚本。

```
if [[ ! $msg =~ $regexp ]]; then echo -e "\n\e[1m\e[31m[INVALID COMMIT MESSAGE]"
  echo -e "------------------------\033[0m\e[0m"
  echo -e "\e[1mValid types:\e[0m \e[34m${types[@]}\033[0m"
  echo -e "\e[1mMax length (first line):\e[0m \e[34m$max_length\033[0m"
  echo -e "\e[1mMin length (first line):\e[0m \e[34m$min_length\033[0m\n"

  # exit with an error
  exit 1
fi 
```

### 配置

传统提交规范的伟大之处在于它的灵活性。您可以选择最符合您要求的规则。这种灵活性是我决定让我的钩子可配置的原因。下面是一个示例配置文件。

将它添加到项目的根目录，并确保它与脚本中的文件名匹配；在这种情况下是`commit-msg.config.json`。

```
{  "enabled":  true,  "revert":  true,  "length":  {  "min":  1,  "max":  52  },  "types":  [  "build",  "ci",  "docs",  "feat",  "fix",  "perf",  "refactor",  "style",  "test",  "chore"  ]  } 
```

## 让你的 Git 挂钩全球化

可以创建全局 Git 挂钩，并在`git init`添加到所有项目中。

启用 Git 模板:

```
git config --global init.templatedir '~/.git-templates' 
```

创建一个目录来保存您的全局钩子:

```
mkdir -p ~/.git-templates/hooks 
```

在`~/.git-templates/hooks/commit-msg`中创建一个`commit-msg`钩子，并使其可执行:

```
touch ~/.git-templates/hooks/commit-msg
chmod u+x ~/.git-templates/hooks/commit-msg 
```

您可以重复从 [**开始制作挂钩**](#making-the-hook) 的步骤，并将它们添加到全局`commit-msg`挂钩中。

### Sailr

如果你不想做上面的任何事情，并且想要一个现成的解决方案，我已经把它放到了自己的项目中，名为 [Sailr](https://github.com/craicoverflow/sailr) 。

以下是安装 Sailr 的方法:

```
git clone https://github.com/craicoverflow/sailr
cd sailr
make install 
```