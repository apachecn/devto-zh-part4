# 不使用 gem 创建 zip 存档的命令📁

> 原文：<https://dev.to/jetrockets/command-for-create-zip-archive-without-gem-s-1jjp>

```
class CreateZipCommand
  def call(files)
    # Create temp directory for files 
    tmp_dir = Dir.mktmpdir
    tmp_zip_path = File.join(tmp_dir, "files.zip")

    # Move files to the temporary folder you created above
    files.map do |file|
      download_file(file, tmp_dir)
    end

    # Go to the folder and archive the entire contents
    `cd #{tmp_dir} && zip #{tmp_zip_path} ./*`

    # Return zip path
    tmp_zip_path
  end
end

> CreateZipCommand.new.call(files)
=> "/var/folders/bk/0c864z710654sx555jpdpx9c0000gn/T/d20190126-7447-d27fpl/files.zip") 
```

大多数用于处理档案的 gems 在处理大文件时会消耗大量内存。这种解决方案没有这些问题。

确保你的电脑上安装了压缩工具——没有它就无法工作