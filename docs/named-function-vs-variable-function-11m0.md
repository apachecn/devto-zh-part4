# 命名函数 vs .变量函数？

> 原文：<https://dev.to/aaron_powell/named-function-vs-variable-function-11m0>

嗨，伙计们，我正在读一本 JavaScript 书，看到了这个例子，但是它并没有完全解释什么时候使用它们，以及除了命名函数被提升以外的用例是什么？

有人能解释一下是否一个总是比另一个好，或者每个的用例是什么吗？

fun(){
console . log(" Hello from func 1 "
)

var fun = function(){
console . log(" Hello from func 2 "
)

两者都可以用作回调函数，对我来说，给函数赋值变量就像是一个命名函数？谢谢！

抱歉没有格式化，在我的手机上写了这个。似乎也不能通过手机添加图片？