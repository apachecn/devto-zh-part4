# 在 CakePHP 3.x 中将日志改为每日日志

> 原文：<https://dev.to/msamgan/changing-the-log-to-daily-in-cakephp-3-x-1537>

您可能知道，根据 CakePHP 的默认配置，调试和错误日志是在同一个文件中生成的。但是这可以通过几个简单的步骤来改变。你所要做的就是跟随。

1.  转到 config/app.php
2.  找到“日志”配置数组
3.  在 debug 和 error 中，用“debug-”替换“file”。日期(' Y-m-d ')，”和“'错误-”。日期('年-月-日')，”分别为
4.  保存文件，你就可以开始了。

现在，每天都会生成日志。