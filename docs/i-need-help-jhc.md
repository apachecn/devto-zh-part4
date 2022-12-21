# 我需要帮助！

> 原文：<https://dev.to/venatusdev/i-need-help-jhc>

所以我和我的朋友为了好玩正在做一个 RPG 网站，我们遇到了一个问题。我们不擅长 PHP。我们太糟糕了，甚至不知道如何从 SQL 查询中提取一个值。请有人能帮助我们从 PHP7 的这个 SQL 查询中获得“name”值。谢谢！

session _ start()；
$ email = $ _ POST[' email ']；
$ password = sha1($ _ POST[' password '])；

$con = mysqli_connect('审查'，'审查'，'审查')；
mysqli_select_db($con，" BATTLE ")；

$ q = " SELECT * FROM players WHERE email = = ' $ email ' "；
$result = mysqli_query($con，$ q)；
$ num = mysqli _ num _ rows($ result)；
if($ num = = 0){
$ _ SESSION[' email ']= $ email；
//取名字！！
}
？>