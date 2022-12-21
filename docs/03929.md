# 验证整个输入数据文件内容一致的最佳方式！！

> 原文：<https://dev.to/chanduthedev/best-way-to-validate-content-of-the-entire-input-data-file-is-consistent-42k8>

在机器学习中，准备数据是关键步骤之一。下面是检查输入数据文件格式是否正确的简单且最好的方法。下面的命令应该总是返回一个唯一的值，否则文件格式不正确。
 `cat file_name | awk -F',' '{print NF}' | sort -u` 
详见原[帖](http://www.chanduthedev.com/2019/09/how-to-validate-data-file-is-formatted.html)。