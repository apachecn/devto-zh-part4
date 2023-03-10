# 为什么选择 PHP...？？？

> 原文：<https://dev.to/kevinhch/why-php-4h4f>

你好，今天我在使用一些 PHP 和 AJAX，我遇到了一个问题，最后我修复了它，但我问自己，为什么 PHP 会这样工作。这是我的 JS 文件。

```
<html>
<script>
        (() => {
            const formData = new FormData();
            formData.append('orange', false)
            formData.append('purple', true)
            formData.append('someData', 'lorem ipsum...you know right')
            formData.append('numbers', [1,1,2,3,5,8])

            const sendData = async (url, method, data) => {
                let response = await fetch(url, {method: method, body: data})
                let dataRes = await response.json()
                console.log(dataRes)
            };//senData
            const url = window.location

            sendData(`${url}back.php`, 'post', formData)

        })()    
</script>
<html> 
```

我正在验证一个表单，准备数据发送到我的后端，并在我的数据库中使用这些数据，没问题。但是当我试图在后台验证数据时...很可怕，因为我试图验证布尔值，而 PHP 对布尔值做了奇怪的事情。

```
<?php
// This is my "validation"
if(isset($_POST['orange']) && $_POST['orange'] == true){
    echo "Why im here, orange is false!!";
} 
```

这是服务器的回应。

[![](img/e012c5ae495128b840a37f8cc059d9c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OzMv_a4s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ihyi7kiqmf2ibxkz2vc.png) 

好的，没问题，让我检查一下我有哪些类型的变量:

[![](img/df414838ef21bbacc937bb2f80fc3dcc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvPBttg0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/71mdpan9yscktk2u8zbk.png) 

我明白了你的意思，我不能用一个字符串和一个布尔进行比较，好吧，让我来转换。

```
<?php
echo "<pre>";
var_dump($_POST['orange']);
echo "</pre>";

$orange = (bool) $_POST['orange'];
echo "casting to boolean...";
echo "<pre>";
var_dump($orange);
echo "</pre>";

// This is my "validation"
if($orange == true){
    echo "Why im here, orange is false!!";
    echo "<pre>";
    var_dump($orange);
    echo "</pre>";
}//if 
```

但还是真的，为什么？我不能变量，现在应该是假的，但仍然是真的。

```
<?php

$orange = (bool) $_POST['orange'];
echo "casting to boolean...";
echo "<pre>";
var_dump($orange);
echo "</pre>";

// This is my "validation"
if($orange == true){
    echo "Why im here, orange is false!!";
    echo "<pre>";
    var_dump($orange);
    echo "</pre>";
}//if
echo "<pre>";
print_r($_POST);
echo "</pre>"; 
```

[![](img/88e2aaaff3b896bdc97f84e42d93dcec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u7djgRIB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8zutcnqp2n7lr7ayuog.png) 

这就是问题所在，如果我要将一个布尔值强制转换成我需要使用 json_decode(什么？)，我有一个字符串，我强制转换成一个 boolean，但是 PHP 不强制转换变量，PHP 检查他是否可以强制转换，如果可以，他们将返回 true，而不是变量的内容。

```
<?php
$orange = (bool) json_decode($_POST['orange']);
//finally
if($orange == true){
    echo "Why im here, orange is false!!";
}else{
    echo "win win for me.<br>";
    var_dump($orange);
} 
```

终于！

[![](img/760df4a04501fb4e5f07676c9e1eab20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D6KepabH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ppcfyy3dxiegpv6ogj7c.png) 

问题是:为什么 PHP 处理布尔值的方式如此怪异？。我确信其他编程语言可以很好地解决这类问题。

解决这个问题的其他方法是:

```
<?php
if($orange == "true"){
    echo "do something";
} 
```

但这不是我的风格。告诉我发生过的类似情况，以避免它们。