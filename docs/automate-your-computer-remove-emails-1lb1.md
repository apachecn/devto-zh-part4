# 自动化你的电脑-删除电子邮件

> 原文：<https://dev.to/roybs2/automate-your-computer-remove-emails-1lb1>

所以我在工作中遇到了一个很烦人的问题。我们支持自动邮件，它们会给我发很多邮件。

大多数人在这种情况下会怎么做？制定一个规则，将它放在不同的文件夹中。所以现在我有 30 多万封电子邮件，我无法进入文件夹，前景一片黯淡...我们做什么呢

### 让我们写一个删除邮件的脚本。

为此，我们将使用 imaplib 库。

```
import imaplib 
```

我们也将使用日期时间，所以:

```
import datetime 
```

接下来我们需要一个新的班级..

```
class EmailCleaner:
    def deleteEmail(self):
        imap_host = 'imap-mail.outlook.com' # for gmail please use: 'imap.gmail.com'
        mail = imaplib.IMAP4_SSL(imap_host)
        mail.login('your email', 'your password') 
```

我们创建了一个名为“EmailCleaner”的新类，在里面我们创建了一个名为“deleteEmail”的新方法。接下来的 3 行定义了主机(在我的例子中是 outlook)，打开 SSL 连接并登录到你的邮箱。

登录后，让我们选择所需的文件夹，并开始运行它。为此，我们需要得到文件夹名和我们想放多少天(我的 deafult 是 50 天)。

```
class EmailCleaner:
    def deleteEmail(self, folderName, daysBack = 50):
        imap_host = 'imap-mail.outlook.com'
        mail = imaplib.IMAP4_SSL(imap_host) 
        mail.login('your email', 'your password')
        mail.select(mailbox=folderName, readonly=False)

        for i in range(0, daysBack):
            date = (datetime.date.today() - datetime.timedelta(i)).strftime("%d-%b-%Y")
            result, data = mail.search(None, '(SENTSINCE {0})'.format(date)) 
```

我们刚刚通过邮件选择了请求的文件夹。选择并设置 readonly=false，因为我们想删除它。

默认情况下，我们不能得到所有的电子邮件，但只能在一批(~1000)，所以我创建了一个循环的日子，它将删除电子邮件依赖于这一天。然后，我按日期搜索要求的电子邮件——“sent since”。
**其他查询可以在这里阅读: [RFC](https://tools.ietf.org/html/rfc1730.html)**

```
 for num in data[0].split():
     mail.store(num, '+FLAGS', r'(\Deleted)')
     print(f'adding email {num} to remove list...') 
```

我创建了嵌套循环，并用删除标志标记每封邮件。

因此，我们登录，选择一个文件夹，并在该文件夹上运行 for 循环，按日期获取电子邮件，并用已删除标志标记它们，现在是时候**删除电子邮件**了。

为了删除电子邮件，我们将使用这个:

```
mail.expunge() 
```

让我们检查所有的代码(我添加了一些打印)...

```
def deleteEmail(self, folderName, daysBack = 50):
    mail = imaplib.IMAP4_SSL(imap_host)
        mail.login('your email', 'your password')

    print("Login success")
    mail.select(mailbox=folderName, readonly=False)

    for i in range(0, daysBack):
        date = (datetime.date.today() - datetime.timedelta(i)).strftime("%d-%b-%Y")
        result, data = mail.search(None, '(SENTSINCE {0})'.format(date))

        for num in data[0].split():
            mail.store(num, '+FLAGS', r'(\Deleted)') #change flag to delete
            print(f'adding email {num} to remove list...')
        mail.expunge() # delete the emails
        print(f'Emails back from day: {i} removed.')
        # close connection and logout
    mail.close()
    mail.logout() 
```

快好了！现在让我们添加 try\catch now 处理异常。

```
 def deleteEmail(self, folderName, daysBack = 50):
        try:
            mail = imaplib.IMAP4_SSL(imap_host)
            mail.login('your email', 'your password')

            print("Login success")
            mail.select(mailbox=folderName, readonly=False)

            for i in range(0, daysBack):
                date = (datetime.date.today() - datetime.timedelta(i)).strftime("%d-%b-%Y")
                result, data = mail.search(None, '(SENTSINCE {0})'.format(date))

                for num in data[0].split():
                    mail.store(num, '+FLAGS', r'(\Deleted)')
                    print(f'adding email {num} to remove list...')
                mail.expunge()
                print(f'Emails back from day: {i} removed.')
            mail.close()
            mail.logout()
        except Exception as e:
            raise Exception(f'Unable to delete emails, e.') 
```

如果你想通过控制台启动它，你可以用 Fire 添加控制台界面。
你可以出我的帖子如何实现:[自动生成命令行界面](https://dev.to/roybs2/automatically-generate-command-line-interfaces-2b3e)

* *享受:)**