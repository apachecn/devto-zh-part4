# 尝试从 URL 加载数据时，物化模式立即关闭

> 原文：<https://dev.to/tylerhs/materialize-modal-closing-instantly-when-attempting-to-load-data-from-a-url-3no4>

这是我第一次使用物化...一直用自举。无论如何，我正在尝试将数据动态加载到物化模型中。

我的问题是，它只出现了一秒钟，然后关闭。任何帮助都将不胜感激。我不确定这是不是我做错了，以及是否有更好的方法来做这件事。

Javascript:

```
 $(document).ready(function () {
        $('.modal').modal();
        $('.openEditor').on('click',function(){
            var dataURL = $(this).attr('data-href');
            $('#editorModal').load(dataURL);
            $('#editorModal').modal('open');
        });
    }); 
```

..我还尝试了以下方法..

```
 $('.openEditor').on('click',function(){
        var dataURL = $(this).attr('data-href');
        $('#editorModal').load(dataURL,function(){
            $('#editorModal').modal('open'); 
        });
    }); 
```

两者结果相同，情态几乎瞬间关闭。

初始化按钮(HTML)

```
 <a href='javascript:void(0);' data-target='editorModal' data-href='/ajax/editorModal.php' class='btn btn-small waves-effect waves-light orange modal-trigger openEditor'>Edit</a> 
```

和实际模态

```
 <div id="editorModal" class="modal"></div> 
```

editorModal.php

```
 <!DOCTYPE html>
    <html lang="en" dir="ltr">
      <head>
        <meta charset="utf-8">
        
      </head>
      <body>
        Body...
      </body>
    </html> 
```