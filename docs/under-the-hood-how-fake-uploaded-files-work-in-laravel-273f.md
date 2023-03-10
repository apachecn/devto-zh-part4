# 引擎盖下:假上传文件如何在 Laravel 工作

> 原文：<https://dev.to/daniel_werner/under-the-hood-how-fake-uploaded-files-work-in-laravel-273f>

## 简介

这是新的“引擎盖下”系列的第一篇文章，我将尝试解释事情是如何在幕后工作的。我会从我的日常工作中挑选一些话题，比如 Laravel、JavaScript、Vue.js 等等。

## 测试文件上传

web 应用程序具有文件上传功能是很常见的，因此我们希望通过测试来涵盖这一功能。Laravel 提供了一种创建假上传文件的方法，使用如下方法:**uploaded file::fake()->image(' avatar . jpg ')；**有关如何测试文件上传的更多信息，请查看 Laravel 文档[此处](https://laravel.com/docs/5.8/http-tests#testing-file-uploads)。

让我们看看 **UploadedFile::fake()** 是如何工作的。fake 方法只是创建并返回 Testing\FileFactory 类的一个新实例:

```
 public static function fake()
    {
        return new Testing\FileFactory;
    } 
```

## 文件工厂

FileFactory 有两个公共方法，我们可以创建一个具有给定名称和文件大小的通用文件，或者我们可以创建一个具有特定宽度和高度的图像。

## 创建

这个方法用 php 内置的 **tmpfile()** 函数创建一个临时文件。该资源用于实例化一个新的测试\文件，设置文件名和所需的文件大小。

```
 public function create($name, $kilobytes = 0)
    {
        return tap(new File($name, tmpfile()), function ($file) use ($kilobytes) {
            $file->sizeToReport = $kilobytes * 1024;
        });
    } 
```

当您从测试用例中发出 post 请求时，将会用到这个新创建的文件，就像这样:

```
$response = $this->json('POST', '/avatar', ['avatar' => $file,]); 
```

## 图像

image 方法的基本原理类似于 create，但它实际上创建了一个具有给定尺寸的黑色图像:

```
 public function image($name, $width = 10, $height = 10)
    {
        return new File($name, $this->generateImage(
            $width, $height, Str::endsWith(Str::lower($name), ['.jpg', '.jpeg']) ? 'jpeg' : 'png'
        ));
    } 
```

正在使用 generateImage 方法生成图像。在撰写本文时，图像生成支持 png 和 jpeg 图像。它还创建一个临时文件，根据$type 参数生成图像，并将内容写入临时文件。

```
 protected function generateImage($width, $height, $type)
    {
        return tap(tmpfile(), function ($temp) use ($width, $height, $type) {
            ob_start();

            $image = imagecreatetruecolor($width, $height);

            switch ($type) {
                case 'jpeg':
                    imagejpeg($image);
                    break;
                case 'png':
                    imagepng($image);
                    break;
            }

            fwrite($temp, ob_get_clean());
        });
    } 
```

使用伪造的上传文件使得测试文件和图像上传变得容易。我希望这篇文章有助于理解它是如何工作的。

帖子[在引擎盖下:伪造的上传文件如何在 Laravel 中工作](https://42coders.com/under-the-hood-how-fake-uploaded-files-work-in-laravel/)首先出现在 [42 Coders](https://42coders.com) 上。