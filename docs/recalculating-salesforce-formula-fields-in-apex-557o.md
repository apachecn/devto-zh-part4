# 在 Apex 中重新计算 Salesforce 公式字段

> 原文：<https://dev.to/katiekodes/recalculating-salesforce-formula-fields-in-apex-557o>

Salesforce Winter '20 在 Apex 中有一个新的`Formula`类-看看它能做什么。

[James Hou](https://github.com/tsalb) 指出，引自 [Windyo](https://github.com/Windyo) 在 Reddit 上的[优秀删节冬季 20 发布笔记:](https://old.reddit.com/r/salesforce/comments/d1qc3r/winter_20_release_notes_abridged_edition/)

> [APEX 中的公式重新计算](http://releasenotes.docs.salesforce.com/en-us/winter20/release-notes/rn_apex_new_classes_methods.htm#rn_apex_new_classes_methods_new_classes):那是巨大的。
> 
> 您可以批量计算没有 DML 的公式。
> 
> 让它深入人心…您基本上可以让公式提升繁重的计算，并在 Apex 类 _ **中完成，而无需保存** _。

而且是真的！

这里有一个单元测试形式的例子:

```
@isTest
public class ATest {
    static testMethod void runTest() {
        ParentObject __c parent = new ParentObject__ c(
            Name = 'Parent',
            Code__c = 'ABAB'
        );
        INSERT parent;
        ChildObject __c child = new ChildObject__ c(
            Name = 'Child',
            Code__c = 'XYXY',
            Parent__c = parent.Id
        );
        System.assertEquals(NULL, child.Concatenated_Formula_Field__c);
        Test.startTest();
        Formula.recalculateFormulas(new List<ChildObject__c>{child});
        Test.stopTest();
        System.assertEquals('ABAB|XYXY', child.Concatenated_Formula_Field__c);
    }
} 
```

注意，名为`child` ***的对象在数据库中还不存在***——它只是一个内存中的`SObject`。

我还没有用`INSERT`命令将*而不是*保存到数据库中。

然而，在运行`Formula.recalculateFormulas()`之后，字段`Concatenated_Formula_Field__c`的值是`ABAB|XYXY`。

*注:在这种情况下，公式本身就是:*

```
Parent__r.Code__c & "|" & Code__c 
```

* * *

## 错误信息解决方法

仅供参考，如果您试图在一个`BEFORE`上下文触发器中使用`Formula.recalculateFormulas()`来确保您根据公式字段的值来调整逻辑，您可能会得到一个`System.DmlException`，即`UNKNOWN_EXCEPTION`和`Cannot set the value of a calculated field`。

到目前为止，我想到的解决方法是避免实际运行`Formula.recalculateFormulas()`来对抗`SObject`,您实际上打算用触发代码来设置`SObject`的字段值。

如果您需要阅读`Contact.Formula_1__c`以决定将什么放入`Contact.Custom_Text_Field__c`，请执行以下操作:

```
private void setCustomField(List<Contact> contacts) {
    Map<Contact, Contact> contactsAndTheirClones = new Map<Contact, Contact>();
    for ( Contact c : contacts ) { contactsAndTheirClones.put(c, c.clone()); }
    Formula.recalculateFormulas(contactsAndTheirClones.values());
    for ( Contact c : contacts ) {
        Contact cClone = contactsAndTheirClones.get(c);
        if ( cClone.Formula_1__c == 'Hello' ) {
            c.Custom_Text_Field__c = 'World';
        }
    }
} 
```

有更好的主意吗？您认为这比 before-trigger 中的小型 SOQL 查询更好还是更差？请评论；我很想听听！

* * *

## 进一步阅读:

*   [使用新的公式类动态重新计算公式——塞巴斯蒂安·凯塞尔的 Salesforce Winter '20](http://thecodery.io/recalculate-formulas-on-the-fly-with-the-new-formula-class-salesforce-winter-20/) ,他深入研究了代码示例