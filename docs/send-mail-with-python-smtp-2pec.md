# 使用 Python SMTP 发送邮件

> 原文：<https://dev.to/furkangulsen/send-mail-with-python-smtp-2pec>

在这篇文章中，我将向你展示如何用 smtp 发送邮件。

```
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
import sys

message = MIMEMultipart()
message['From'] = "get mail address"
message['To'] = "send mail address"
message['Subject'] = "Smtp mail send control"

messageText = """
PYTHON smtp mail control
message 1
message 2
message 3
"""
message_content = MIMEText(messageText,"plain")
message.attach(message_content)

try:
    mail = smtplib.SMTP("smtp.gmail.com",587)
    mail.ehlo()
    mail.starttls()
    mail.login("mail address","password")
    mail.sendmail(message["From"],message["To"],message.as_string())
    print("Mail successfully sent")
    mail.close()
except:
    sys.stderr.write("There is a problem.")
    sys.stderr.flush() 
```

Enter fullscreen mode Exit fullscreen mode