# 如何在 Xamarin Android 中创建 zip

> 原文：<https://dev.to/omarrodriguez15/how-to-create-a-zip-in-xamarin-android-5g65>

这个解决方案是专门针对使用 Xamarin 的 Android 的，因为我使用的是`Java.Util.Zip Class`。这个解决方案可以很容易地移植到 Java 上。

如果您只想查看代码，请跳到下面的解决方案部分。

## 我为什么需要这个功能？

我正致力于开发一个 Android 工具来帮助解决一个不容易使用的设备上的问题。我正在实现一个功能，收集重要的本地文件和文件夹，然后将它们打包并通过电子邮件发送给感兴趣的人。压缩文件很重要，因为许多使用该应用程序的设备的互联网连接可能很差。

## 处理问题的不同方法

### 纯 C#方式

最初，我试图寻找一个通用的 C#解决方案，我可能会跨平台。然而,`System.IO.Compression` API 有点受限。例如，我可以轻松地压缩一个目录，但我无法控制文件夹中压缩的内容，也就是说，如果我想跳过特定的文件夹，api 不允许这样做。

### nu get 包方法

我试图寻找一个 NuGet 包，它可以简单地为我压缩一个文件夹。这让我想到了使用 7z nuget 的可能性，但由于缺乏易于查找/阅读的文档，我决定暂时不走这条路。

### 安卓的具体做法

然后我试图用 Java 找到一个 Android 特有的解决方案，这将我引向了`Java.Util.Zip`类。我发现了一个很好的压缩和解压 zip 文件的例子。C#的移植非常简单。

我做了一些修改，比如可以跳过文件夹，以及为了可读性做了一些重构。修正了一些文件意外问题，因为我不能保证我正在压缩的文件不会被写入，因为我正在读取它们来压缩它们。

## 溶液用法举例

下面是该解决方案的一个简单用法示例。请注意，zip 的目的地可以是任何地方。如果不想排除任何文件夹，最后一个参数可以是一个空集。

```
ZippingAround.Compress("/sdcard/foo/", "/sdcard/destination/foo.zip", new HashSet<string>{ "large-files-folder" }); 
```

## 解

```
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

using Java.Util.Zip;

namespace foo
{
   public class ZippingAround
   {
      public static void Compress(string from, string to, HashSet<string> excludeFolders)
      {
         if (File.Exists(to)) File.Delete(to);

         using (var filestream = new FileStream(to, FileMode.OpenOrCreate, FileAccess.Write))
         {
            using (var zipStream = new ZipOutputStream(filestream))
            {
               CompressDirectory(from, zipStream, excludeFolders);
               zipStream.Finish();
            }
         }
      }

      private static void CompressDirectory(string dir2zip, ZipOutputStream zipStream, HashSet<string> foldersToExclude, string parent = "")
      {
         if (!Directory.Exists(dir2zip)) throw new ArgumentException($"Directory given does not exists. [{dir2zip}]");

         var zipDir = new DirectoryInfo(dir2zip);

         foreach (var file in zipDir.GetFiles())
         {
            var relativePath = string.IsNullOrEmpty(parent) ? file.Name : $"{parent}/{file.Name}";
            AddFileToArchive(zipStream, relativePath, file.FullName);
         }

         var directories = from directory in zipDir.GetDirectories()
                           where !foldersToExclude.Contains(directory.Name)
                           select directory;

         foreach (var directory in directories)
         {
            var relativeParentPath = string.IsNullOrEmpty(parent) ? directory.Name : $"{parent}/{directory.Name}";
            CompressDirectory(directory.FullName, zipStream, foldersToExclude, relativeParentPath);
         }
      }

      private static void AddFileToArchive(ZipOutputStream zipStream, string relativePath, string fullPath)
      {
         var tempFileName = $"{fullPath}.{DateTime.UtcNow.Ticks}.temp";

         try
         {
            File.Copy(fullPath, tempFileName);

            zipStream.PutNextEntry(new ZipEntry(relativePath));
            byte[] bytes = File.ReadAllBytes(tempFileName);
            zipStream.Write(bytes, 0, bytes.Length);
            zipStream.CloseEntry();
         }
         catch (Exception ex)
         {
            Console.WriteLine($"Problem compressing {ex.Message}");
         }

         if (File.Exists(tempFileName)) File.Delete(tempFileName);
      }
   }
} 
```

## 提升空间

这个类可以通过增加跳过文件和文件扩展名的能力来改进。