# 用 Java 保护演示文稿幻灯片

> 原文：<https://dev.to/eiceblue/protect-presentation-slides-in-java-5gde>

借助[自由尖顶。Java](https://www.e-iceblue.com/Introduce/free-presentation-for-java.html) 的演示，开发者可以用密码保护 PowerPoint 文件或者只允许演示幻灯片只读，读者不能编辑或更改幻灯片上的内容。本文将从以下三个方面向您展示如何保护 Java 应用程序中的演示幻灯片:

*   加密演示幻灯片
*   将演示文稿幻灯片设置为只读
*   加载加密的 PowerPoint 文档并更新其密码

加密 PowerPoint 文件。用户必须输入密码才能打开和观看演示幻灯片。

```
 Presentation presentation = new Presentation();
presentation.loadFromFile("Sample.pptx");

presentation.encrypt("e-iceblue");

presentation.saveToFile("output/Encrypted.pptx", FileFormat.PPTX_2010); 
```

将演示文稿幻灯片设置为只读。用户无需密码即可阅读演示幻灯片。如果他们想编辑演示文稿，那是不可能的。演示文稿幻灯片已被设置为只读。

```
Presentation presentation = new Presentation();
presentation.loadFromFile("Sample.pptx");

presentation.protect("123456");

presentation.saveToFile("output/Readonly.pptx", FileFormat.PPTX_2010); 
```

修改受保护的 PowerPoint 文档的密码。更新现有密码。

```
Presentation presentation = new Presentation();

//load the encrypted document with password
presentation.loadFromFile("Encrypted.pptx", FileFormat.PPTX_2010,"e-iceblue");

//Remove the Encryption
presentation.removeEncryption();
//Set the new password to encrypt the document
presentation.encrypt("Newpass");

presentation.saveToFile("output/Modifypass.pptx", FileFormat.PPTX_2010); 
```