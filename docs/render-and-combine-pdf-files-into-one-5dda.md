# 渲染并合并 PDF 文件为一个

> 原文：<https://dev.to/jetrockets/render-and-combine-pdf-files-into-one-5dda>

使用 [WickedPDF](https://github.com/mileszs/wicked_pdf) 可以在 [Rails](https://rubyonrails.org/) 中从 HTML 模板呈现 PDF 文件。

首先，您需要使用`ActionView`将 HTML 呈现为一个字符串:

```
def render_to_string(data)
  action_view = ActionView::Base.new
  action_view.view_paths = ActionController::Base.view_paths

  action_view.class_eval do
    include ApplicationHelper
    include PDFHelper
    # or other helpers
  end

  action_view.render template: 'pdf/template.html',
                     layout: 'layout/pdf.html',
                     locals: { data: data }
end 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以用如下的设置渲染 pdf:

```
# first pdf file with some view settings and values from data1...
pdf1 = WickedPdf.new.pdf_from_string(
        render_to_string(data1), {
            pdf: 'report1',
            page_size: 'Letter',
            orientation: 'Portrait'
        })
# ...and second pdf file with some view settings and values from data2
pdf2 = WickedPdf.new.pdf_from_string(
        render_to_string(data2), {
            pdf: 'report2',
            page_size: 'Letter',
            orientation: 'Landscape'
        }) 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以将它与 [CombinePDF](https://github.com/boazsegev/combine_pdf) gem 结合起来，gem 为您提供了获取 PDF 内容的`parse`方法和将结果渲染回 PDF 的`to_pdf`方法。

```
combiner = CombinePDF.new
combiner << CombinePDF.parse(pdf1)
combiner << CombinePDF.parse(pdf2)
combiner.to_pdf 
```

Enter fullscreen mode Exit fullscreen mode