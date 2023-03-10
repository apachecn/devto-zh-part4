# 使用 Ruby on Rails 5.2 活动存储

> 原文：<https://dev.to/rafaelcpalmeida/using-ruby-on-rails-5-2-active-storage-h97>

**活动存储**，现在与 **Ruby on Rails 5.2** 捆绑在一起，允许你轻松处理你的文件上传，要么上传到云平台，如**亚马逊 S3** 或**谷歌云存储**，甚至上传到你的应用程序目录，轻松地将你的上传链接到你的**活动记录**模型。

为了开始使用**活动存储**，你应该有一个现有的 **Ruby on Rails 5.2** 或更高版本的应用程序(新安装或升级的)，在你的应用程序目录中打开你的终端并键入以下命令:

```
rails active_storage:install 
```

Enter fullscreen mode Exit fullscreen mode

这将为两个必要的数据库表生成一个全新的迁移。在这之后，您需要做的就是使用:
运行迁移

```
rails db:migrate 
```

Enter fullscreen mode Exit fullscreen mode

此后，一切准备就绪，可以开始使用**活动存储**。

## 设置

首先，您应该在您的`config/storage.yml`文件中声明您的**活动存储**服务，每个服务对应一个您在应用程序中使用的服务。

> 请注意，出于本文的考虑，我将只展示将文件上传到您的应用程序所在的目录中的配置。如果您喜欢使用它上传到任何云提供商，请参考[文档](https://guides.rubyonrails.org/v5.2/active_storage_overview.html)。

```
local:
    service: Disk
    root: <%= Rails.root.join("storage") %> 
```

Enter fullscreen mode Exit fullscreen mode

在您声明了将要使用的每个服务之后，是时候告诉您的应用程序应该在每个环境中使用什么服务了。在本文中，我将配置我的**开发**环境，以使用我的**本地**存储服务。为了实现这一点，在`config/environments/development.rb` :
中添加以下行

```
# Store files locally.
config.active_storage.service = :local 
```

Enter fullscreen mode Exit fullscreen mode

## 使用它

现在，我们已经成功配置了**活动存储**，是时候开始使用它了！它的一个优点是可以轻松地在上传的资产和您的**活动记录**模型之间建立联系，正如本文开头所述，所以我们要利用它。我有一个**扬声器**模型，其中我打算添加一个头像到每个**扬声器**。

首先，我们将通知我的**扬声器**模型，它将与相应的上传头像有一个`one-to-one`关系。我的模型将看起来像:

```
class Speaker < ApplicationRecord
    has_one_attached :avatar
end 
```

Enter fullscreen mode Exit fullscreen mode

然后我的**扬声器**控制器将接受一个头像上传。我的`speaker_params`现在看起来像:

```
def speaker_params
    params.permit(:name, :company, :email, :avatar)
end 
```

Enter fullscreen mode Exit fullscreen mode

文件现在应该是这样的:

```
class SpeakersController < APIController
    before_action :set_speaker, only: %i[show update destroy]    def index
        @speakers = Speaker.all
        render json: @speakers, status: :ok
    end    def create
        @speaker = Speaker.create!(speaker_params)
        render json: @speaker, status: :created
    end    def show
        render json: @speaker, status: :ok
    end    def update
        @speaker.update(speaker_params)
        head :no_content
    end    def destroy
        @speaker.destroy
        head :no_content
    end    private    def speaker_params
        params.permit(:name, :company, :email, **:avatar**, :social_media)
    end    def set_speaker
        @speaker = Speaker.find(params[:id])
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

我必须在我的**请求**主体上指定一个名为**头像**的字段，它的值将是一个文件，它将被用作说话者头像。

完成 **POST** 请求并导航到`storage`目录后，您会看到一个新创建的目录，其中包含您上传的文件。如果您希望验证文件是否链接到您的扬声器，您可以启动`rails console`并键入:

```
irb(main):001:0> speaker = Speaker.find(1)
=> #<Speaker id: 1, name: “Rafael”, email: “rafael.almeida@xing.com”, company: “XING”, social_media: nil, created_at: “2018–10–02 09:58:54”, updated_at: “2018–10–02 09:58:54”>
irb(main):002:0> app.url_for(speaker.avatar)
=> “http://localhost:3000/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBCZz09IiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--372a32ffdde03c2f2a37f407e63d10c909fd52ba/xing-photo.jpg" 
```

Enter fullscreen mode Exit fullscreen mode

*Et voilá* ，**主动存储**配置完毕，准备使用。

## 奖励提示

如果您在您的 **API** 上执行一个 **GET** 请求，您将获得您在迁移中定义的关于您的**演讲者**的信息，如下例:

```
{  "id":  2,  "name":  "Rafael",  "email":  "rafael.almeida@xing.com",  "company":  "XING",  "social_media":  null,  "created_at":  "2018-10-02T09:59:28.494Z",  "updated_at":  "2018-10-02T09:59:28.497Z"  } 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可能也想获得相关的头像。我们可以使用一个**串行器**来达到我们的目的。您需要添加`gem 'active_model_serializers'`，然后在您想要用**串行化器** : `include ActionController::Serialization`覆盖的每个**控制器**中包含以下行。之后你可以使用`rails g serializer speaker`和 **Rails** 会为你生成文件。

为了得到相关的**头像**，你的`serializer`应该是:

```
class SpeakerSerializer < ActiveModel::Serializer
  attributes :id, :name, :email, :company, :avatar

  def avatar
    rails_blob_path(object.avatar, only_path: true) if object.avatar.attached?
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您重新运行您的 **GET** 请求，您将看到类似于
的内容

```
{  "speaker":  {  "id":  2,  "name":  "Rafael",  "email":  "rafael.almeida@xing.com",  "company":  "XING",  "avatar":  "http://localhost:3000/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBCdz09IiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19–515a0de8817b3529b5d3d168871cebf6ccee0463/xing-photo.jpg"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以看到与每个发言者相关联的头像。