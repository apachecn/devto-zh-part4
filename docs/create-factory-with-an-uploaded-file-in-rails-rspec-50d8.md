# 用 Rails rspec 中上传的文件创建工厂

> 原文：<https://dev.to/jetrockets/create-factory-with-an-uploaded-file-in-rails-rspec-50d8>

要用 Rails rspec 中上传的文件创建工厂，需要在`rails_helper.rb`文件中包含`ActionDispatch::TestProcess`模块，以便在工厂中使用`#fixture_file_upload`方法。

```
# spec/rails_helper.rb
include ActionDispatch::TestProcess 
```

Enter fullscreen mode Exit fullscreen mode

在工厂中，当设置属性值时，使用方法`#fixture_file_upload`，指定路径和文件类型。

```
# spec/factories/import_file.rb
FactoryBot.define do
  factory :import_file, class: ImportFile do
    data { fixture_file_upload 'spec/fixtures/test_file.pdf', 'application/pdf' }
  end
end 
```

Enter fullscreen mode Exit fullscreen mode