# 如何在 Linux 上安装“Go”

> 原文：<https://dev.to/buzzedison/how-to-install-go-on-linux-4j1i>

* * *

1.  查看你的软件包是否是最新的<u>在终端输入</u> **sudo apt-get 更新** **`This will bring in all the repo list and update them. It will also tell you how many of the packages can be upgraded`**

```
 **Now we are getting ready to install go**
2\. Go to the Go website and download the linux package. https://golang.org/

3\. Create the folder **usr** and inside that the folder another folder called **local**. 
<code>Extract downloaded file to usr/local.</code>
If you CD to **usr/local/go** and type <code>go version</code> you can see go version is installed. 

**But we want to be able to use go from everywhere on our computer.**
To do that we need to set the path in our Vim profile or your preferred editor.

4\. Type **Vim ~/.profile** and then add to path
   PATH = $PATH: /usr/local/go/bin 
   <code>Save and close! </code>

5\. Type **go version** anywhere on the terminal. You should see the version of **go** you installed displayed on your screen. 
<code>Hurray now you have go installed. </code>

6\. Let us create our first file for testing purposes. 
   Type what you see below

    **CD /tmp **
Then create a new file. 
     **Vim test.go **
Type what you see below inside the test.go file you just created. 
```

Enter fullscreen mode Exit fullscreen mode

package main
Import ' fmt '
Func main(){
fmt . println("欢迎来到我的第一个 go 代码！")
}

```
 7\. Save the file and close it then run it. 
type what you see below to run it
** Go run test.go **
<code> You should see the result <i>Welcome to my first go code!</i> displayed.

<h2>Congratulations!.</h2>

Side note
<code>  
I am taking up the challenge to learn Go in 100 days. Will share from time to time what the journey is like. Want to build an application at the end of the 100 days. Please share your experience with Go and resources you have so we can cross reference etc.</code> 
```

Enter fullscreen mode Exit fullscreen mode