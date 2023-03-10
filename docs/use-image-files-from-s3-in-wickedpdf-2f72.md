# 在 WickedPdf 中使用 S3 的图像文件

> 原文：<https://dev.to/jetrockets/use-image-files-from-s3-in-wickedpdf-2f72>

如果您需要在使用 WickedPdf 生成的 pdf 文件中使用来自 S3 的图像文件，那么您需要首先下载图像。您可以创建这样做的方法，并将其添加到助手中。

```
require 'open-uri'

module PdfHelper
  def embed_remote_image(url, content_type)
    asset = open(url, "r:UTF-8", &:read)
    base64 = Base64.encode64(asset.to_s).gsub(/\s+/, "")
    "data:#{content_type};base64,#{Rack::Utils.escape(base64)}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

并且用`image_tag`代替`wicked_pdf_image_tag`

```
= image_tag embed_remote_image(file.logo_url, 'image/jpeg') 
```

Enter fullscreen mode Exit fullscreen mode