# 如何从 PHP 变量创建 Javascript 变量？

> 原文：<https://dev.to/supunkavinda/how-to-create-javascript-variables-from-php-variables-11jn>

有时，您可能需要使用 PHP 声明动态 JS 变量。

您可以做的一件事是手动编写 JS 代码，如下所示。

```
var x = "<?php echo $name ?>"; 
```

Enter fullscreen mode Exit fullscreen mode

但是，我们可以创建一个函数，通过更好的数据类型支持来轻松做到这一点。

```
function phpVarsToJs($vars) {
    echo '<script>';
    foreach ($vars as $key => $val) {
        echo "var $key =";
        if (is_int($val)) echo $val;
        else if (is_bool($val)) echo $val ? 'true' : 'false';
        else if (is_string($val)) echo '"' . $val . '"';
        else if (is_array($val)) echo json_encode($val);
        echo ';';
    }
    echo '</script>';
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数需要一个 PHP 数组，其中包含键/值对，即 Javascript 变量名和值。此外，这个函数根据 PHP 变量的数据类型自动创建 Javascript 变量。

PHP 数组将被转换成 JSON 格式。如果您不需要'

## 举例

```
<?php
    require_once 'functions.php'; // assuming this includes our function
?>
<html>
<head>
    My App
</head>
<body>
    This is my app!

    <?php
        phpVarsToJs(array(
            'name' => 'John',
            'age' => 23,
            'meta' => [
                'height' => 168, // cm
                // and more ... 
            ]
        )); 
    ?>

</body> 
```

Enter fullscreen mode Exit fullscreen mode

谢谢！

我之前在我的新网站 Hyvor Groups 上发表了这篇关于 PHP 团队的文章。