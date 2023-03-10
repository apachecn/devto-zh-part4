# 将图像从 Angular 上传到 ASP.NET 核心 Web API

> 原文：<https://dev.to/codingdefined/uploading-image-from-angular-to-asp-net-core-web-api-4enm>

[![](img/472f5f5df2526399558424756c96602e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iILhvBGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-YFj71Z8esZw/XTB0OKvoyMI/AAAAAAAAC_I/VDylqIfgRBIVO5HN2BFwFedz5RkckzkWACLcBGAs/s1600/angularerror.PNG)

在这篇文章中，我们将讨论创建一个应用程序，你可以使用 ASP.NET 样板从 angular 8 上传图片到 ASP.NET 核心网络 API。在这种情况下，我们将首先通过后端，即 ASP.NET 核心部分，然后我们将通过前端，即角。

我已经在 UserAppService 中编写了一个 UploadProfilePicture 函数，它将处理配置文件 pic 的上传，然后在预定义的位置创建图像。

其背后的逻辑非常简单，我们提供了存储文件的路径。我们检查目录是否存在，如果不存在，则创建目录。然后，我们创建文件流对象，然后将文件存储在该位置。

```
public async Task<string> UploadProfilePicture([FromForm(Name = "uploadedFile")] IFormFile file, long userId)
{
  if (file == null || file.Length == 0)
    throw new UserFriendlyException("Please select profile picture");

  var folderName = Path.Combine("Resources", "ProfilePics");
  var filePath = Path.Combine(Directory.GetCurrentDirectory(), folderName);

  if (!Directory.Exists(filePath))
  {
    Directory.CreateDirectory(filePath);
  }

  var uniqueFileName = $"{userId}_profilepic.png";
  var dbPath = Path.Combine(folderName, uniqueFileName);

  using (var fileStream = new FileStream(Path.Combine(filePath, uniqueFileName), FileMode.Create))
  {
    await file.CopyToAsync(fileStream);
  }

  return dbPath;
} 
```

### 这里要注意几件事:

1.  如果文件输入的名称属性与控制器中使用的参数名称属性不同，则 IFormFile 对象将为 null。因此，您应该将其命名为 same 或使用[FromForm(Name = " ")，然后如下所示分配名称。

2.  因为我们正在创建一个新目录，所以我们需要告诉 ASP.NET 核心服务器从这个位置提供静态文件。为此，我们需要修改 Startup.cs 类的配置方法，如下所示:

```
app.UseStaticFiles(new StaticFileOptions()
{
 FileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), @"Resources")),
 RequestPath = new PathString("/Resources")
}); 
```

1.  当使用 IFormFile 时，swagger 会给你多个文本框，如 ContentType，ContentDisposition，Headers，Length，Name，FileName 等，而不是文件上传控件。要将文本框更改为实际的文件上传控件，我们需要实现 IOperationFilter，然后实现 apply 方法，如下所示。主要部分是我们需要定义文件的类型，因为我们正在清除所有前面的参数，我们还需要添加用户 id 参数。

```
using Swashbuckle.AspNetCore.Swagger;

using Swashbuckle.AspNetCore.SwaggerGen;

namespace LetsDisc.Web.Host.Startup
{
 public class FileUploadOperation : IOperationFilter
 {
  public void Apply(Operation operation, OperationFilterContext context)
  {
   if (operation.OperationId.ToLower().Contains("upload")))
   {
    operation.Parameters.Clear();
    operation.Parameters.Add(new NonBodyParameter
        {
          Name = "uploadedFile",
          In = "formData",
          Description = "Upload File",
          Required = true,
          Type = "file"
        });
    operation.Parameters.Add(new NonBodyParameter
        {
          Name = "userId",
          In = "query",
          Description = "",
          Required = true,
          Type = "long"
        });
    operation.Consumes.Add("multipart/form-data");
   }
  }
 }
} 
```

这样我们就完成了后端，现在我们将继续前端实现。

### HTML

在 HTML 中，我们将有一个文件类型的输入，然后我们有改变和点击功能，使用户可以上传同一个图像两次。

```
<div class="col-md-3 profile-image-edit">
 <label class="hoverable" for="fileInput">
 ... img tag
  <span class="hover-text">Choose file</span>
  <span class="background"></span>
 </label>
 <br />
 <input #fileInput id="fileInput" type='file' (click)="fileInput.value = null" value="" (change)="onSelectFile($event.target.files)">
 <button class="btn btn-default" *ngIf="url" (click)="delete()">delete</button>
</div> 
```

### CSS

```
.hoverable {
    position: relative;
    cursor: pointer;
    height: 150px;
    width: 150px;
    border-radius: 50%;
}

    .hoverable .hover-text {
        position: absolute;
        display: none;
        top: 50%;
        left: 50%;
        transform: translate(-50%,-50%);
        z-index: 2;
    }

    .hoverable .background {
        position: absolute;
        display: none;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        background-color: rgba(255, 255, 255, 0.5);
        pointer-events: none;
        border-radius: 50%;
        z-index: 1;
    }

    .hoverable:hover .hover-text {
        display: block;
    }

    .hoverable:hover .background {
        display: block;
    } 
```

### 代码后面

在后面的代码中，我们将有一个函数，它有一个 fileReader ojject 来预览图像，我们还将调用我们的后端服务来上传图像。

```
onSelectFile(files: FileList) {

  if (files.length === 0)
    return;

  this.fileToUpload = files.item(0);

  const fileReader: FileReader = new FileReader();
  fileReader.readAsDataURL(this.fileToUpload);

  fileReader.onload = (event: any) => {
    this.url = event.target.result;
  };

  this.files.push({ data: this.fileToUpload, fileName: this.fileToUpload.name });

  this._userService.uploadProfilePicture(this.files[0], this.user.id)
    .subscribe((result: string) => {
      this.userDetails.profileImageUrl = result;
  });
}

delete() {
  this.url = null;
} 
```

[![](img/b694c31bfe192ff4a74436007db08130.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e1EQ4MeN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-ZixJCZe7FHI/XTBzBny_23I/AAAAAAAAC_A/Rz7nQPU6RTYu_z9BkH7a3_tPhei6mXeKQCLcBGAs/s1600/angularerror.PNG)

GitHub 提交:[https://GitHub . com/coding defined/lets disc/Commit/ab 7 af 63 ba 3c f 94 c 23278 fc 2 Fe 00d 3769672 BF 506](https://github.com/codingdefined/LetsDisc/commit/ab7af63ba3cf94c23278fc2fe00d3769672bf506)

* * *

## 也发表:【CodingDefined.com】T2