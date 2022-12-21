# 如何检查 Makefile 中的强制变量

> 原文：<https://dev.to/rlespinasse/how-to-check-a-mandatory-variable-in-a-makefile-1k9n>

当您在 Makefile 任务中使用一个变量并希望使它成为强制变量并在运行有效任务之前检查它时，

你能做到。

```
task-who-need-SPECIFIC_ENVVAR: guard-SPECIFIC_ENVVAR
    @echo ${SPECIFIC_ENVVAR} 
```

Enter fullscreen mode Exit fullscreen mode

您只需要添加以下任务

```
guard-%:
    @ if [ "${${*}}" = "" ]; then \
        echo "Environment variable $* not set"; \
        exit 1; \
    fi 
```

Enter fullscreen mode Exit fullscreen mode

所以当你运行它时，`guard`会阻止运行任务

```
$ make task-who-need-SPECIFIC_ENVVAR
Environment variable SPECIFIC_ENVVAR not set make: *** [guard-SPECIFIC_ENVVAR] Error 1 
```

Enter fullscreen mode Exit fullscreen mode

```
$ export SPECIFIC_ENVVAR=value
$ make task-who-need-SPECIFIC_ENVVAR
value 
```

Enter fullscreen mode Exit fullscreen mode

参见[https://stackoverflow.com/a/7367903/1848685](https://stackoverflow.com/a/7367903/1848685)