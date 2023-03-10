# C 和 C++中如何搬起石头砸自己的脚？俳句操作系统食谱

> 原文：<https://dev.to/alexastva/how-to-shoot-yourself-in-the-foot-in-c-and-c-haiku-os-cookbook-1i5j>

这个故事要追溯到 2015 年，当时 Haiku OS 和 PVS-Studio static analyzer 开发人员决定联手改进这个 OS 的代码质量。起初，这更像是一个实验，因为那时没有 Linux 分析器，团队只能使用编译好的可执行分析器文件。用于解析编译器参数、运行预处理器、并行分析等的整个基础设施取自 C#中的[编译器监控 UI](https://www.viva64.com/en/m/0031/) 实用程序，该实用程序被部分移植到 Mono 平台，以便在 Linux 中运行。

[![HaikuOS](img/62b00d32f6a9e2181476dae705d38eeb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y4SqveHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/EAU3U7xXUAAN4EQ%3Fformat%3Djpg%26name%3Dlarge)

这个故事要追溯到 2015 年，当时 Haiku OS 和 PVS-Studio static analyzer 开发人员决定联手改进这个 OS 的代码质量。起初，这更像是一个实验，因为那时没有 Linux 分析器，团队只能使用编译好的可执行分析器文件。用于解析编译器参数、运行预处理器、并行分析等的整个基础设施取自 C#中的[编译器监控 UI](https://www.viva64.com/en/m/0031/) 实用程序，该实用程序被部分移植到 Mono 平台，以便在 Linux 中运行。

现在的俳句项目是在各种 OS 下用交叉编译器搭建的，除了 Windows。我想再一次提到与 Haiku OS 构建相关的便利性和文档完整性，并感谢 Haiku OS 开发人员在构建项目中给予的帮助。

有趣的是，编程错误的本质是，如果你不去寻找它们，不去关注代码质量，它们不会消失。俳句开发者试图使用 Coverity Scan，但是，最可悲的是，最后一次分析是在两年前。尽管该分析已经在 2014 年使用 Coverity 进行了配置，但这并没有阻止我们在 2015 年写两篇关于错误回顾的长文([第一部分](https://www.viva64.com/en/b/0317/)、[第二部分](https://www.viva64.com/en/b/0318/))。现在四年后，一篇关于检查这个项目的新文章出现了。

***注:*** *这里会有一些来自项目的有趣错误，更完整的报告可以查看文章“ [*如何在 C 和 C++中搬起石头砸自己的脚”。俳句 OS 食谱*T8】](https://www.viva64.com/en/b/0644/)*T10】。

所以让我们来看看错误:

## 正式保安

编译器可以删除“memset”函数调用，该函数用于刷新“f_key”对象。应该使用 memset_s()函数来擦除私有数据。dst_api.c 1018

```
#ifndef SAFE_FREE
#define SAFE_FREE(a) \
do{if(a != NULL){memset(a,0, sizeof(*a)); free(a); a=NULL;}} while (0) ....
#endif 
DST_KEY *
dst_free_key(DST_KEY *f_key)
{
  if (f_key == NULL)
    return (f_key);
  if (f_key->dk_func && f_key->dk_func->destroy)
    f_key->dk_KEY_struct =
      f_key->dk_func->destroy(f_key->dk_KEY_struct);
  else {
    EREPORT(("dst_free_key(): Unknown key alg %d\n",
       f_key->dk_alg));
  }
  if (f_key->dk_KEY_struct) {
    free(f_key->dk_KEY_struct);
    f_key->dk_KEY_struct = NULL;
  }
  if (f_key->dk_key_name)
    SAFE_FREE(f_key->dk_key_name);
  SAFE_FREE(f_key);
  return (NULL);
} 
```

分析仪检测到可疑代码，旨在安全清除私人数据。不幸的是，扩展成*memset 、*FREE *调用和* NULL *赋值的*SAFE _ FREE *宏并没有让代码变得更安全，因为在用*O2* 进行优化时，它们都被编译器删除了。*

顺便说一下，这不是别的，正是 [CWE-14](https://cwe.mitre.org/data/definitions/14.html) :编译器删除代码以清除缓冲区。

## 杂项

“strncat”函数调用可能导致“输出”缓冲区溢出。边界不应该包含缓冲区的大小，而是它可以容纳的字符数。NamespaceDump.cpp 101

```
static void
dump_acpi_namespace(acpi_ns_device_info *device, char *root, int indenting)
{
  char output[320];
  char tabs[255] = "";
  ....
  strlcat(tabs, "|--- ", sizeof(tabs));
  ....
  while (....) {
    uint32 type = device->acpi->get_object_type(result);
    snprintf(output, sizeof(output), "%s%s", tabs, result + depth);
    switch(type) {
      case ACPI_TYPE_INTEGER:
        strncat(output, "     INTEGER", sizeof(output));
        break;
      case ACPI_TYPE_STRING:
        strncat(output, "     STRING", sizeof(output));
        break;
      ....
    }
    ....
  }
  ....
} 
```

对于不熟悉这些函数描述的人来说，* strlcat *和* strncat *函数之间的区别不是很明显。* strlcat *函数期望整个缓冲区的大小作为第三个参数，而* strncat *函数-缓冲区中空闲空间的大小，这需要在调用函数之前评估所需的值。但是开发者经常忘记或者不知道这件事。将整个缓冲区大小传递给* strncat *函数会导致缓冲区溢出，因为该函数会将该值视为可接受的复制字符数。* strlcat *函数没有这样的问题。但是您必须传递以终端 null 结尾的字符串，这样它才能正常工作。

## 自由函数的错误

空指针被传递到“自由”函数中。检查第一个参数。package fileheapwriter . CPP 166

```
void* _GetBuffer()
{
  ....
  void* buffer = malloc(fBufferSize);
  if (buffer == NULL && !fBuffers.AddItem(buffer)) {
    free(buffer);
    throw std::bad_alloc();
  }
  return buffer;
} 
```

有人在这里犯了一个错误。必须使用| |运算符来代替&&。只有在这种情况下，如果内存分配(使用*malloc 函数)失败，才会抛出*STD::bad _ alloc()*异常。*

## 删除操作符出错

[V611](https://www.viva64.com/en/w/v611/) 内存是使用“new T[]”操作符分配的，但使用“delete”操作符释放了内存。请考虑检查这段代码。使用“delete[]fout buffer；”可能更好。检查线:26，45。PCL6Rasterizer.h 26

```
class PCL6Rasterizer : public Rasterizer
{
public:
  ....
  ~PCL6Rasterizer()
  {
    delete fOutBuffer;
    fOutBuffer = NULL;
  }
  ....
  virtual void InitializeBuffer()
  {
    fOutBuffer = new uchar[fOutBufferSize];
  }
private:
  uchar* fOutBuffer;
  int    fOutBufferSize;
}; 
```

使用*delete *操作符代替*delete[]是一个常见的错误。写类时最容易出错，因为析构函数的代码通常远离内存位置。这里，程序员错误地释放了析构函数中* fOutBuffer *指针存储的内存。*

关注我们的 PVS-Studio 团队博客,看看另一个俳句操作系统错误评论即将为那些从头到尾阅读第一部分的人发布。完整的 analyzer 报告将在发布此错误检查之前发送给开发人员，因此在您阅读本文时，一些错误可能已经被修复。为了打发文章之间的时间，我建议为你的项目下载并尝试 [PVS-Studio](https://www.viva64.com/en/pvs-studio-download/) 。