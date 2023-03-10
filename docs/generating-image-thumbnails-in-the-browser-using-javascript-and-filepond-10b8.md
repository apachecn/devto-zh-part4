# 使用 JavaScript 和 FilePond 在浏览器中生成图像缩略图

> 原文：<https://dev.to/pqina/generating-image-thumbnails-in-the-browser-using-javascript-and-filepond-10b8>

[FilePond](https://pqina.nl/filepond/) 是一个免费的 **JavaScript 文件上传库**。在本文中，我们将探索 FilePond 的功能以及如何使用插件来扩展它。我们将结合一些插件在客户端生成图像缩略图。

如果你想编码，在你喜欢的文本编辑器中打开一个空的 HTML 文件。

让我们开始吧。

*赶时间？[在这里查看最终结果](https://codepen.io/rikschennink/pen/ydYxOE)*

## 设置文件池

我们将从一个基本的 HTML 大纲开始，并添加一个`<input type="file"/>`。

*请注意，你也可以`import`和[使用 FilePond 作为 ES6 模块](https://pqina.nl/filepond/docs/patterns/installation/#using-npm)，但对于本教程，我们将坚持普通的 HTML，因为它需要较少的项目设置*

```
<!doctype html>
<html>
  <head>
    Hello World
  </head>
  <body>
    <input type="file">
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

让我们添加所需的 FilePond 脚本和样式。

```
<!doctype html>
<html>
  <head>
    Hello World

    <!-- FilePond styles -->
    <link href="https://unpkg.com/filepond/dist/filepond.css" rel="stylesheet">

  </head>
  <body>
    <input type="file">

    <!-- FilePond scripts -->
    <script src="https://unpkg.com/filepond/dist/filepond.js"></script>

  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们用文件池初始化逻辑来扩展它。

```
<!doctype html>
<html>
  <head>
    Hello World
    <link href="https://unpkg.com/filepond/dist/filepond.css" rel="stylesheet">
  </head>
  <body>
    <input type="file">
    <script src="https://unpkg.com/filepond/dist/filepond.js"></script>

    <!-- FilePond initialisation logic -->
    <script>
    const inputElement = document.querySelector('input[type="file"]');
    const pond = FilePond.create(inputElement);
    </script>

  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这将告诉 FilePond 在我们的文件输入的位置`create`一个 FilePond 实例。

如果您在浏览器中运行此程序，将会出现文件池拖放区。它可以处理单个文件。我们可以将`multiple`属性添加到`input`元素中，以允许添加多个文件。

```
<!doctype html>
<html>
  <head>
    Hello World
    <link href="https://unpkg.com/filepond/dist/filepond.css" rel="stylesheet">
  </head>
  <body>

    <!-- Add 'multiple' attribute -->
    <input type="file" multiple>

    <script src="https://unpkg.com/filepond/dist/filepond.js"></script>
    <script>
    const inputElement = document.querySelector('input[type="file"]');
    const pond = FilePond.create(inputElement);
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这很好。让我们添加一些插件。

## 添加插件

让我们添加[图像预览](https://pqina.nl/filepond/docs/patterns/plugins/image-preview/)、[图像调整](https://pqina.nl/filepond/docs/patterns/plugins/image-resize/)和[图像变换](https://pqina.nl/filepond/docs/patterns/plugins/image-transform/)插件。

图像预览插件将显示一个拖放图像的预览。图像调整插件将调整信息添加到 FileItem 元数据中，最后是图像转换插件，它将使用调整信息来调整实际图像的大小。

```
<!doctype html>
<html>
  <head>
    Hello World
    <link href="https://unpkg.com/filepond/dist/filepond.css" rel="stylesheet">

    <!-- Add plugin styles -->
    <link href="https://unpkg.com/filepond-plugin-image-preview/dist/filepond-plugin-image-preview.css" rel="stylesheet">

  </head>
  <body>
    <input type="file" multiple>

    <!-- Add plugin scripts -->
    <script src="https://unpkg.com/filepond-plugin-image-preview/dist/filepond-plugin-image-preview.js"></script>
    <script src="https://unpkg.com/filepond-plugin-image-resize/dist/filepond-plugin-image-resize.js"></script>
    <script src="https://unpkg.com/filepond-plugin-image-transform/dist/filepond-plugin-image-transform.js"></script>

    <script src="https://unpkg.com/filepond/dist/filepond.js"></script>
    <script>
    const inputElement = document.querySelector('input[type="file"]');
    const pond = FilePond.create(inputElement);
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

刷新页面，什么都没变。奇怪。要让 FilePond 使用插件，我们需要将它们注册到库中，这不是自动完成的。

让我们扩展我们的底层初始化`<script>`,如下所示。

```
// register the plugins with FilePond
FilePond.registerPlugin(
  FilePondPluginImagePreview,
  FilePondPluginImageResize,
  FilePondPluginImageTransform
);

const inputElement = document.querySelector('input[type="file"]');
const pond = FilePond.create(inputElement); 
```

Enter fullscreen mode Exit fullscreen mode

要看到这个工作，把一个图像放在文件池拖放区，它现在显示图像的预览。

## 显示调整大小后的预览

现在是时候告诉池塘我们的意图了。我们可以通过向`FilePond.create`方法传递一个配置对象来做到这一点。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256
}); 
```

Enter fullscreen mode Exit fullscreen mode

这将指示 resize 插件向 FileItem 元数据添加一个`resize`条目。我们可以通过添加`onaddfile`回调来查看这些元数据。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256,

  // add onaddfile callback
  onaddfile: (err, fileItem) => {
    console.log(err, fileItem.getMetadata('resize'));
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

以下内容将被记录到开发人员控制台。

```
null, { mode: "cover", upscale: true, size: { width: 256, height: 256 } } 
```

Enter fullscreen mode Exit fullscreen mode

`null`表示添加文件时没有错误，其余数据与图像调整插件添加的`resize`信息有关。

现在让我们展示图像转换插件的输出。我们可以通过添加`onpreparefile`回调函数来做到这一点，当图像转换插件已经“准备好”一个文件时，就会调用这个函数。它接收图像变换过程的`fileItem`和`output`斑点对象。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256,
  onaddfile: (err, fileItem) => {
    console.log(err, fileItem.getMetadata('resize'));
  },

  // add onpreparefile callback
  onpreparefile: (fileItem, output) => {
    // create a new image object
    const img = new Image();

    // set the image source to the output of the Image Transform plugin
    img.src = URL.createObjectURL(output);

    // add it to the DOM so we can see the result
    document.body.appendChild(img);
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

调整后的图像现在出现在文件池拖放区下方的页面上。

它应该是 256 像素宽，根据图像的长宽比，它的高度可能会超过 256 像素。这是因为`imageResizeMode`被设置为`'cover'`，将其设置为`'contain'`将确保输出图像始终包含在调整大小目标尺寸内。

现在让我们将`imageResizeMode`设置为`'contain'`。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256,

  // set contain resize mode
  imageResizeMode: 'contain',

  onaddfile: (err, fileItem) => {
    console.log(err, fileItem.getMetadata('resize'));
  },
  onpreparefile: (fileItem, output) => {
    const img = new Image();
    img.src = URL.createObjectURL(output);
    document.body.appendChild(img);
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经完成了生成一个缩略图，现在让我们生成多个。

## 生成多个缩略图

图像转换插件有一些自己的配置值。

通过设置`imageTransformOutputQuality`属性，我们可以控制图像输出质量，并且我们可以通过将`imageTransformOutputMimeType`设置为`'image/jpeg'`来将图像转换为 JPEGs

我们现在需要的属性是`imageTransformVariants`，它用于创建文件的附加版本。我们将生成图像的两个附加版本，一个 512 像素宽，一个 64 像素宽。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256,
  imageResizeMode: 'contain',

  // add imageTransformVariant settings
  imageTransformVariants: {
    thumb_medium_: transforms => {
      transforms.resize.size.width = 512;
      return transforms;
    },
    thumb_small_: transforms => {
      transforms.resize.size.width = 64;
      return transforms;
    }
  },

  onaddfile: (err, fileItem) => {
    console.log(err, fileItem.getMetadata('resize'));
  },
  onpreparefile: (fileItem, output) => {
    const img = new Image();
    img.src = URL.createObjectURL(output);
    document.body.appendChild(img);
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

坚果！我们的脚本抛出了一个错误。

```
Failed to execute 'createObjectURL' on 'URL': No function was found that matched the signature provided. 
```

Enter fullscreen mode Exit fullscreen mode

它告诉我们，无论我们试图创建一个网址，它不工作。

这是因为我们现在正在生成多个文件。我们在`onpreparefile`回调中的`output`参数已经变成了一个数组。让我们修改代码，使它能够处理文件列表。

```
const pond = FilePond.create(inputElement, {
  imageResizeTargetWidth: 256,
  imageResizeMode: 'contain',
  imageTransformVariants: {
    thumb_medium_: transforms => {
      transforms.resize.size.width = 512;
      return transforms;
    },
    thumb_small_: transforms => {
      transforms.resize.size.width = 64;
      return transforms;
    }
  },
  onaddfile: (err, fileItem) => {
    console.log(err, fileItem.getMetadata('resize'));
  },

  // alter the output property
  onpreparefile: (fileItem, outputFiles) => {
    // loop over the outputFiles array
    outputFiles.forEach(output => {
      const img = new Image();

      // output now is an object containing a `name` and a `file` property, we only need the `file`
      img.src = URL.createObjectURL(output.file);

      document.body.appendChild(img);
    })
  }

}); 
```

Enter fullscreen mode Exit fullscreen mode

现在，拖放一个文件会导致三个图像被添加到 DOM 中，所有图像都与提供的宽度相匹配。

我们可以进一步添加[图像裁剪插件](https://pqina.nl/filepond/docs/patterns/plugins/image-crop/)，然后我们可以告诉 FilePond 以特定的纵横比自动裁剪输出图像。

让我们快点做这件事，然后今天就到此为止。

```
<!doctype html>
<html>
  <head>
    Hello World
    <link href="https://unpkg.com/filepond/dist/filepond.css" rel="stylesheet">

    <!-- Add plugin styles -->
    <link href="https://unpkg.com/filepond-plugin-image-preview/dist/filepond-plugin-image-preview.css" rel="stylesheet">

  </head>
  <body>
    <input type="file" multiple>

    <script src="https://unpkg.com/filepond-plugin-image-preview/dist/filepond-plugin-image-preview.js"></script>
    <script src="https://unpkg.com/filepond-plugin-image-resize/dist/filepond-plugin-image-resize.js"></script>
    <script src="https://unpkg.com/filepond-plugin-image-transform/dist/filepond-plugin-image-transform.js"></script>

    <!-- add the Image Crop plugin script -->
    <script src="https://unpkg.com/filepond-plugin-image-crop/dist/filepond-plugin-image-crop.js"></script>

    <script src="https://unpkg.com/filepond/dist/filepond.js"></script>
    <script>
    FilePond.registerPlugin(
      // register the Image Crop plugin with FilePond
      FilePondPluginImageCrop,
      FilePondPluginImagePreview,
      FilePondPluginImageResize,
      FilePondPluginImageTransform
    );

    const inputElement = document.querySelector('input[type="file"]');
    const pond = FilePond.create(inputElement, {
      // add the Image Crop default aspect ratio
      imageCropAspectRatio: 1,
      imageResizeTargetWidth: 256,
      imageResizeMode: 'contain',
      imageTransformVariants: {
        thumb_medium_: transforms => {
          transforms.resize.size.width = 512;

          // this will be a landscape crop
          transforms.crop.aspectRatio = .5;

          return transforms;
        },
        thumb_small_: transforms => {
          transforms.resize.size.width = 64;
          return transforms;
        }
      },
      onaddfile: (err, fileItem) => {
        console.log(err, fileItem.getMetadata('resize'));
      },
      onpreparefile: (fileItem, outputFiles) => {
        outputFiles.forEach(output => {
          const img = new Image();
          img.src = URL.createObjectURL(output.file);
          document.body.appendChild(img);
        })
      }
    });
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

您可以在下面观看现场演示。放下一个图像文件，你会看到三个不同的输出文件。

*   一个 256 x 256 像素的大正方形。
*   宽度为 512 像素、高度为 256 像素的横向矩形。
*   一个 64x64 像素的小正方形。

[https://codepen.io/twhite96/embed/ydYxOE?height=600&default-tab=result&embed-version=2](https://codepen.io/twhite96/embed/ydYxOE?height=600&default-tab=result&embed-version=2)

图像在单独的线程上调整大小，因此图像生成过程相当快，不会阻塞用户界面。

我们现在可以使用[图像过滤器插件](https://pqina.nl/filepond/docs/patterns/plugins/image-filter/)通过对每个像素应用坐标矩阵来生成图像的灰度版本，但我认为我们现在已经完成了基础工作。我们已经了解了如何在客户机上生成缩略图，以及如何利用 FilePond 来实现这一点。

我希望这对你有用，如果你有任何问题，请告诉我，我很乐意在下面回答。