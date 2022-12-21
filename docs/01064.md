# 编写易读代码的两个快速提示

> 原文：<https://dev.to/jeastham1993/two-quick-tips-for-writing-legible-code-1l1c>

我们都经历过。回顾许多个月前我们写的一段代码，想知道我们的意图到底是什么。当查看别人写的代码时，也会发生类似的事情。

虽然这两种情况都不表明是“坏”代码，但它确实浪费了你试图弄清楚到底发生了什么的宝贵时间。

今天我想分享的是我最喜欢的两个编写清晰代码的技巧。

## 故事书代码

我的第一个，也是最大的一个建议是，像写故事一样写代码。想象一下，你是一个一生中从未见过一行代码的人，你必须弄清楚一段代码在做什么。

举下面这个例子:

```
var deserializedObject = JsonConvert.DeserialzeObject&lt;MyObjectType>(aLongString); 
```

绝大多数的。NET 开发人员与 Newtonsoft 打过交道。Json，所以这很有意义。但是想象一下你以前从未见过它…你可能会弄明白，但是这不是更好吗:

```
var myObject = MyObjectType.CreateFromJsonString(aJsonString); 
```

最初开发者的意图要容易得多。他们打算从 JSON 字符串创建一个“MyObjectType”类型的对象。

## 更好的例子

这个例子很琐碎，所以让我们看一个我最近参与的项目中的例子。

在解释它做什么之前，我将编写代码的两种变体(重构前和重构后)。我们来看看:

```
namespace JEasthamDev
{
    public class Uploader
    {
        public void Upload()
        {
            var outstandingDocuments = await this._documentService.GetDocuments();
            var batchName = $"newbatch_{DateTime.Now}";

            foreach (var document in outstandingDocuments)
            {
                foreach (var file in document.Files.Where(p => p.HasSentToOcr == false){
                    var bytes = file.FileData;

                    using (var ocrResource = new OcrResource)
                    {
                        var existingBatch = ocrResource.GetBatch(batchName);

                        if (existingBatch == null)
                        {
                            existingBatch = ocrResource.CreateBatch(batchName);
                        }

                        existingBatch.AddFiles(document.FileName, bytes);

                        ocrResource.UpdateBatch(batch);
                    }
                }
            }
        }
    }
} 
```

```
namespace JEasthamDev
{
    public class RefactoredUploader
    {
        public void UploadAllOutstandingDocumentsToOcrEngine()
        {
            var outstandingDocuments = await this._documentService.GetDocuments();
            var batchName = this.generateUniqueBatchName();

            foreach (var document in outstandingDocuments)
            {
                this.uploadSingleDocumentToOcrEngine(batchName, document);
            }
        }

        private void uploadSingleDocumentToOcrEngine(string batchName, IDocument document)
        {
            foreach (var file in document.GetFilesNotSentForOcr())
            {
                var ocrBatch = this.createNewOrRetrieveExistingBatchInOcrEngine(batchName);

                ocrBatch.AddFileFromBytes(file.FileName, file.GetByteRepresentation());

                ocrBatch.Update();
            }
        }

        private IBatch createBatchInOcrEngineIfNotExists(string batchNameToCreate)
        {
            using (var ocrResource = new OcrResource)
            {
                var existingBatch = ocrResource.GetBatch(batchName);

                if (existingBatch == null)
                {
                    existingBatch = ocrResource.CreateBatch(batchName);
                }

                existingBatch.AddFiles(document.FileName, bytes);

                ocrResource.UpdateBatch(batch);
            }
        }

        private string generateUniqueBatchName(){
            return $"NewBatch_{DateTime.Now.ToString("yyyyMMdd-HHmmss")}";
        }
    }
} 
```

从没见过这个代码库的角度来看，哪个更可取？

我非常期待。阅读本文的 NET devs 可以很容易地解释这两个示例，但在我个人看来，第二个示例更容易理解。

出于以下原因，我尝试遵循这种代码风格:

1.  可读性——如果我在 12 个月后再次查看第二个样本，我可以很快确定在很高的层面上发生了什么
2.  单一和具体的责任——每种方法都有非常具体和独特的关注点。
    *   UploadAllOutstandingDocumentsToOcrEngine–Uploader 类的唯一公共 API。不需要注释来理解它的作用
    *   uploadSingleDocumentToOcrEngine–我打赌你猜不出这个方法是做什么的…
    *   createbatchinocrnengineifnotexists——再说一次，谁知道这个方法是干什么的
    *   generateUniqueBatchName——我通常会将它作为批处理对象本身的一个静态方法，但是为了清楚起见，它在方法内部
3.  小方法长度——每个方法中的行数非常有限，使得理解该方法做什么变得非常容易

这很好地把我引向了关于编写可读代码的第二个技巧

## 保持你的方法小

这个策略已经被讨论过很多次了，而且有很多经验法则。

*   不超过 20 行
*   应该可以放在一张 A4 纸上
*   不超过屏幕上显示的像素数量乘以当前正在观看的监视器的尺寸

所有这些都是很棒的选择，会让你走上正确的道路(除了最后一个，没人有时间做这个！).

不过，我个人最喜欢的还是好看简单。

> 方法中的所有代码都应该在单个视窗中可见

不用花时间在一个方法中上下滚动所节省的时间是令人难以置信的。变量声明、逻辑，所有的一切都在一个地方，这对你的开发效率和理智都是极好的！

希望这两个技巧能为您作为开发人员的生产力提供一些有用的技巧。不过，我总是着迷于听到其他人的“技巧”和保持代码清晰的技巧。在评论里留言吧，我们来讨论一下！