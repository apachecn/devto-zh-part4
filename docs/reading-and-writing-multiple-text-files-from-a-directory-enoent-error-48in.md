# 从目录中读取和写入多个文本文件出现错误

> 原文：<https://dev.to/captaincook888/reading-and-writing-multiple-text-files-from-a-directory-enoent-error-48in>

我在桌面上有一个名为“Main”的文件夹，其中有两个名为“Input”和“Output”的文件夹。这些文件夹是手动创建的(右键单击-新建文件夹)。名为 Verification.js 的脚本位于名为“Input”和“Output”的文件夹之外，位于“Main”文件夹之内。该脚本读取“输入”文件夹中的多个文本文件，运行所有文本文件的逻辑，将更新的文本文件写入“输出”文件夹。

到目前为止，我在 Verification.js 脚本中更新的程序如下:

var fs = require('fs ')

fs.readdir('Input '，(err，files)= > {
if(err){
//调试器；
console . log(err)；
返回；
}
files . foreach(file =>{
fs . readfile(file，' utf8 '，function (err，result){
if(err){
return console . log(err)；
}
结果=//逻辑；

```
 fs.writeFile('Output',result,'utf8', function (err) {
    if (err) {
      return console.log(err);
    }
  }); 
```

});
})；

});

我得到以下错误:它不能识别文本文件。

C:\用户>光盘 C:\用户\桌面\主

c:\ Users \ Desktop \ Main > node verification . js

{[错误:ENOENT:没有这样的文件或目录，打开' C:\ Users \ Desktop \ Main \ test file 1 . txt ']
错误号:-4058，
代码:' ENOENT '，
syscall: 'open '，
路径:
' C:\ Users \ \ Desktop \ Main \ test file 1 . txt ' }
{[错误:ENOENT:没有这样的文件或目录，打开' C:\ Users \ Desktop \ Main \ test file 2 . txt ']
错误

我该如何矫正？它不识别错误中所示的“路径”属性中的输入文件夹，也不识别文本文件。