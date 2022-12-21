# 如何在没有临时文件的情况下动态创建 zip 文件

> 原文：<https://dev.to/jetrockets/how-to-create-zip-files-on-the-fly-w-o-tempfile-160g>

有许多文章介绍了如何从服务器存档文件，并向客户机发送 zip 文件，而不在服务器上保存它。但是通常他们不会真的这么做，因为他们使用的是临时文件。

有一个简单的方法可以做到这一点，但不需要创建任何文件。你只需要把文件直接放到`Zip::OutputStream`中，然后从中读取。注意:你必须在阅读之前倒带。

```
# some files objects
def download(files)
  zip_stream = Zip::OutputStream.write_buffer do |zip|
    files.each.with_index(1) do |file, index|
     # file name must be uniq in archive
      zip.put_next_entry("#{file.name}--#{index}.#{file.extension}")
      zip.write(file.read.force_encoding('utf-8'))
    end
  end
  # important - rewind the steam
  zip_stream.rewind
  send_data zip_stream.read, 
            type: 'application/zip', 
            disposition: 'attachment', 
            filename: 'files-archive.zip'
end 
```

Enter fullscreen mode Exit fullscreen mode