# 从您的 Google 联系人中删除 ms-outlook URL

> 原文：<https://dev.to/merill/remove-ms-outlook-urls-from-your-google-contacts-18kh>

微软的 Outlook 手机版是最好的手机邮件客户端之一，如果不是最好的话。然而，如果你在 iOS 上使用保存联系人功能，并且有一个第三方联系人同步应用程序来保持 iCloud 和 GMail 联系人同步，事情可能会有点混乱。我使用谷歌的牛逼[联系人同步](https://apps.apple.com/au/app/contacts-sync-for-google-gmail/id454390333)

如果你有 100 多个联系人有奇怪的网址，比如“ms-outlook://people/”或者写满了“这个联系人是只读的”这样的文字。若要进行更改，请点击上面的链接在 Outlook 中进行编辑。那你就有点麻烦了，尤其是如果你像我一样有强迫症的话。

不要害怕,[谷歌脚本](https://script.google.com)来拯救我们了。一个简单的黑客和五分钟后，你可以享受一个完全清理联系人名单，没有所有的 Outlook 垃圾。

```
function myFunction() {
  var outlookJunkNote = "This contact is read-only. To make changes, tap the link above to edit in Outlook."
  var contacts = ContactsApp.getContacts();
  for (var c = 0; c < contacts.length; c++) {
    var cnt = contacts[c];

    //Clean outlook:// urls
    var fields = cnt.getUrls();
    for (var i = 0; i < fields.length; i++) {
      if(fields[i].getLabel() == "Outlook"){
        fields[i].deleteUrlField();
      }
    }

    //Clean outlook notes
    var notes = cnt.getNotes();
    if(notes.indexOf(outlookJunkNote) !== -1) {
       var regex = new RegExp(outlookJunkNote, 'g');
       var cleanNotes = notes.replace(regex, '');
       cnt.setNotes(cleanNotes);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经在 Google Script 上发布了这个脚本，所以你可以简单地把它保存到你的帐户上，然后运行它来清理你的 Google 联系人。[删除 Outlook 垃圾脚本](https://script.google.com/d/17QekwOHQwWAbELmO2xgiHnrRLkzMyaXo-1Fe2L_cBhGitMhsXJvL3asF/edit?usp=sharing)