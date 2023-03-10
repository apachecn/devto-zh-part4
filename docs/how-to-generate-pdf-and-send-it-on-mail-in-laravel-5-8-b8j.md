# 如何在 Laravel 5.8 中生成 PDF 并通过邮件发送

> 原文：<https://dev.to/sandeepbalachandran/how-to-generate-pdf-and-send-it-on-mail-in-laravel-5-8-b8j>

你有没有想过如何生成 pdf 文件，并以附件形式随邮件发送给客户？

我在骗谁呢？甚至在你听到“邮件”这个词之前，谷歌就已经存在了。如果你是开发者，你已经谷歌过了，并且马上就做了。

无论如何，如果你从未想过，或者你必须在 laravel 上尝试一下，那就这样吧。

在此之前，如果你过得不开心，让我先珍惜你。如果你不想振作起来，就跳过来吧。

你生命中发生的一切都是有原因的。(大部分励志内容应该以此开头)。想着我会克服这一切，而不是想着为什么会发生在我身上。至少吃你能得到的。努力改善你的社交圈，每天学习。迟早一切都会好的。

# 主要内容来自这里

## 目录

*   描述
*   关于 SMTP 服务器
*   外部包装
*   视角
*   控制器
*   途径

<center>

# <u>描述</u>

</center>

[Laravel](https://laravel.com/) is an open-source PHP framework, which is robust and easy to understand. It follows a model-view-controller design pattern. Laravel reuses the existing components of different frameworks which helps in creating a web application.

<center>

# <u>关于 SMTP 服务器</u>

</center>

当然，要发送邮件，你需要一个 smtp 服务器。您需要在服务器上配置来初始化邮件服务。

在控制面板中设置好邮件服务器后(我将在另一篇文章中介绍)
我们需要将凭证添加到。laravel 项目的 env 文件。

```
MAIL_DRIVER=smtp
MAIL_HOST=mail.domain.com
MAIL_PORT=587
MAIL_USERNAME=test@test.domain.com
MAIL_PASSWORD=the one you chose to create
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS=yourmail@provider.com
MAIL_FROM_NAME="Your Name" 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>外部包装</u>

</center>

Here i used exteranal package library for generating pdf on the go. Its called
[laravel-dompdf](https://github.com/barryvdh/laravel-dompdf).
From the the project directory type

```
composer require barryvdh/laravel-dompdf 
```

Enter fullscreen mode Exit fullscreen mode

等待命令执行完毕。

成功执行后，转到配置> > app.php 并添加以下配置。

```
 'providers' => [
    ....
    Barryvdh\DomPDF\ServiceProvider::class,
],
'aliases' => [
    ....
    'PDF' => Barryvdh\DomPDF\Facade::class,
], 
```

Enter fullscreen mode Exit fullscreen mode

在控制器中添加以下内容

```
use PDF; 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>视图</u>

</center>

在资源目录下创建一个[刀片](https://laravel.com/docs/5.8/blade)文件。我在里面创建了一个名为 mail 的目录，这里的名字是 mail。

```
<h1>Mail body goes  here</h1>
<p>pass here anything you want inside $data array in your controller.</p> 
```

Enter fullscreen mode Exit fullscreen mode

<center>

# <u>控制器</u>

</center>

At the end your controller looked like this

```
 public function sendmail(Request $request){
        $data["email"]=$request->get("email");
        $data["client_name"]=$request->get("client_name");
        $data["subject"]=$request->get("subject");

        $pdf = PDF::loadView('mails.mail', $data);

        try{
            Mail::send('mails.mail', $data, function($message)use($data,$pdf) {
            $message->to($data["email"], $data["client_name"])
            ->subject($data["subject"])
            ->attachData($pdf->output(), "invoice.pdf");
            });
        }catch(JWTException $exception){
            $this->serverstatuscode = "0";
            $this->serverstatusdes = $exception->getMessage();
        }
        if (Mail::failures()) {
             $this->statusdesc  =   "Error sending mail";
             $this->statuscode  =   "0";

        }else{

           $this->statusdesc  =   "Message sent Succesfully";
           $this->statuscode  =   "1";
        }
        return response()->json(compact('this'));
 } 
```

Enter fullscreen mode Exit fullscreen mode

# <u>解释</u>

*   控制器采用 3 个 post 参数，例如
    *   `$data["email"]=$request->get("email");`
    *   `$data["client_name"]=$request->get("client_name");`
    *   `$data["subject"]=$request->get("subject");`
*   `$pdf = PDF::loadView('mails.mail', $data)`从邮件目录为 pdf 内容加载了刀片模板。`$data`也传递给了刀片文件。所以我可以根据我们的需要在那个文件上使用`$data`
*   `Mail::send('mails.mail', $data, function($message)use($data,$pdf) {

    $message->to($data["email"], $data["client_name"])

    ->subject($data["subject"])

    ->attachData($pdf->output(), "invoice.pdf");

    });` ——为了岗位使用同一把刀片锉刀。您可以使用任何 blade 文件作为电子邮件的正文。剩下的是一种自我解释。只是使用 post 请求中的所有变量。
*   输出文件名为“invoice.pdf ”,它将附在邮件中

<center>

# 途径

</center>

```
Route::post('/sendmail','BasicController@sendmail'); 
```

<center>Rest Client</center>

![Rest Client](img/f34b4647e985aee998a76244309e6104.png)

没错。就是这样。

[![Thumbs up](img/cce5f8148e21a9424dc617f43c97420b.png)](https://i.giphy.com/media/JPsFUPp3vLS5q/giphy.gif)