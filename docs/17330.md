# 使用 Rails 将记录导出到 CSV 文件

> 原文：<https://dev.to/victorhazbun/export-records-to-csv-files-with-rails-2778>

了解如何使用 Ruby on Rails 将记录导出到 CSV 文件中。以下是步骤。

## 步骤:

1.  添加一个控制器，并确保处理 csv 请求。
2.  添加一条指向控制器的路线
3.  使用类方法向 _csv 添加一个模型

* * *

## 让我们编码吧！

### 控制器

```
# users_controller.rb

class UsersController < ApplicationController
  def index
    @users = User.all

    respond_to do |format|
      format.html
      format.csv { send_data @users.to_csv, filename: "users-#{Date.today}.csv" }
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

### 路线

```
# routes.rb

...

resources :users, only: :index

... 
```

Enter fullscreen mode Exit fullscreen mode

### 型号

```
# user.rb

class User < ActiveRecord::Base
  def self.to_csv
    attributes = %w{id email name}

    CSV.generate(headers: true) do |csv|
      csv << attributes

      all.find_each do |user|
        csv << attributes.map{ |attr| user.send(attr) }
      end
    end
  end

  def name
    "#{first_name}  #{last_name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 核心自定义方法:

`to_csv`将映射出您的模型集合的“id，email，name”值，然后生成一个 CSV 字符串。

* * *

## Rails 此功能的核心方法:

> `#send_data`将给定的二进制数据发送到浏览器。这个方法类似于 render :text = > data，但也允许您指定浏览器是应该将响应显示为文件附件(即在下载对话框中)还是内联数据。您还可以设置内容类型、外观文件名和其他内容。[来源](http://apidock.com/rails/ActionController/Streaming/send_data)