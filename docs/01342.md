# 改变发件人的名字和电子邮件地址

> 原文：<https://dev.to/kashifraza/wordpress-change-sender-name-and-email-address-from-wp-54bn>

这段代码只是用你自定义的发件人姓名和电子邮件地址替换了默认的发件人姓名和电子邮件地址。

你可以通过添加一个新用户，更改密码，或者任何其他发送 WordPress 通知邮件的动作来测试。

//更改电子邮件地址的功能

函数 wpb _ sender _ email(＄original _ email _ address){
return '[tim.smith@example.com](mailto:tim.smith@example.com)'；
}

//更改发件人姓名的函数
函数 wpb _ sender _ name($ original _ email _ from){
返回' Tim Smith '；
}

//将我们的函数挂接到 WordPress filters
add _ filter(' WP _ mail _ from '，' wpb _ sender _ email ')；
add _ filter(' WP _ mail _ from _ name '，' wpb _ sender _ name ')；