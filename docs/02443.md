# 专家指南，一个自动与终端中运行的应用程序交互的强大工具

> 原文：<https://dev.to/thecodersblog/guide-to-expert-an-awesome-tool-to-automatically-interact-with-applications-that-run-in-terminal-166e>

Expect 是一个很棒的工具，可以自动与终端中运行的应用程序进行交互。 **Expect** 脚本允许我们启动一个应用程序，然后监控 **stdout** ，并在检测到某个 **stdout** 模式发生时向该应用程序的 **stdin** 发送消息。

## 基本概念

**Expect** 的核心功能包括: **spawn** 、 **expect** 和 **send** 。

1.  **spawn** :启动我们想要监视/控制的进程。
2.  **expect** :等待，只有当其他人检测到被监控的 stdout 中的某个模式或触发超时时，才执行下一个动作。
3.  **send** :向被监控应用的 stdin 发送文本。

其他内置变量和函数:

*   **超时**:设置 expect 超时。( *-1* )表示禁用超时。
*   **puts** :将文本写入交互屏幕。
*   **等待**:等待被监控的进程停止。
*   **sleep** :让 expect 脚本休眠一段时间。
*   **$argv** :输入参数数组。
*   **eof** :产卵进程关闭时出现的模式。
*   lindex :访问数组元素。
*   **交互**:给用户交互控制权。

**Expect** 还提供了全局和局部变量和函数的概念:

*   下面这段伪代码用三个参数定义了函数**run cmd**:**X**， **Y** ， **Z** 。
*   关键字 **global** 用于从 **runcmd** 内部访问 **VAR** 。
*   **$X** 指变量 **X** 。
*   以 **#** 开头的行表示注释。
*   **arg1** 和 **arg2** 捕捉输入参数。

```
set arg1 [lindex $argv 0]
set arg2 [lindex $argv 1]

set VAR "..."

# define function runcmd
proc runcmd{ X , Y , Z } {

    global VAR
    ...
    puts "X=$X"
}
# call function runcmd with X=A,Y=B,Z=C
runcmd A,B,C 
```

## 例子

下面的函数尝试访问一个 ssh 服务器，该服务器需要输入密码并在某个目录下运行脚本。脚本、目录和服务器是该函数的输入参数。在主脚本中，调用该函数来*停止 */opt/tomcat* 中的 tomcat 服务*。

```
SET SSHPWD "xxxxxx"
#--------------------------------------------------------------------
# run cmd in directory of remote server 
#--------------------------------------------------------------------
proc runcmd { script , dir , server } {

    global SSHPWD

    puts "------------------------------------------------------------"
    puts " run - $script - in - $dir - of - $server -"
    puts "------------------------------------------------------------"

    # never timeout
    set timeout -1 

    # login remote server
    spawn ssh $server
    expect "password:"
    send "$SSHPWD\r"

    # change to root
    expect "\[user@"  
    send "sudo -s\r"
    expect "root@"

    # run cmd  
    send "cd $dir\r"
    send "pwd\r"
    send "\r"
    send "$script \r"
    send "\r"

    # logout    
    send "exit\r"
    send "exit\r"

    # connection is closed
    expect "closed\."

    puts "------------------------------------------------------------"
    puts " finish - $script -"
    puts "------------------------------------------------------------"

    sleep 10

}

runcmd "service tomcat7 stop" , "/opt/tomcat/" , "server" 
```