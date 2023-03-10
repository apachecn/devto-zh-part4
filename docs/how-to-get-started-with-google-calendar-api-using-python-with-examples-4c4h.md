# 如何使用 Python 开始使用 Google Calendar API 并提供示例

> 原文：<https://dev.to/karenapp/how-to-get-started-with-google-calendar-api-using-python-with-examples-4c4h>

谷歌日历提供了一种最方便、最流行的方式来管理日程安排、
活动、会议，甚至计划你的假日活动。一些你认为简单明了的事情最终会因为定制需求而变得复杂。谷歌日历
应用编程接口让你自动化你的日历，满足那些定制需求。

## 1。如何在 Google 开发者控制台中创建项目？

首先，让我们带领谷歌开发者控制台创建一个新项目。
我们把这个项目命名为自动化日历，进一步来说，就是获取凭证。

## 2。设置 Google API 服务

```
import pickle
import os.path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request

# If modifying these scopes, delete the file token.pickle.
SCOPES = ['https://www.googleapis.com/auth/calendar']

CREDENTIALS_FILE = 'credentials.json'

def get_calendar_service():
    creds = None
    # The file token.pickle stores the user's access and refresh 
    tokens, and is
    # created automatically when the authorization flow completes for 
      the first
    # time.
    if os.path.exists('token.pickle'):
        with open('token.pickle', 'rb') as token:
            creds = pickle.load(token)
    # If there are no (valid) credentials available, let the user log 
      in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                CREDENTIALS_FILE, SCOPES)
            creds = flow.run_local_server(port=0)
        # Save the credentials for the next run
        with open('token.pickle', 'wb') as token:
            pickle.dump(creds, token)

    service = build('calendar', 'v3', credentials=creds)
    return service 
```

## 2。如何使用日历 API 从用户帐户获取所有日历的列表

让我们用下面的代码片段在与`cal_setup.py`相同的目录中创建一个名为`list_calendars.py`的文件:

```
from cal_setup import get_calendar_service

def main():
   service = get_calendar_service()
   # Call the Calendar API
   print('Getting list of calendars')
  calendars_result = service.calendarList().list().execute()

   calendars = calendars_result.get('items', [])

   if not calendars:
       print('No calendars found.')
   for calendar in calendars:
       summary = calendar['summary']
       id = calendar['id']
       primary = "Primary" if calendar.get('primary') else ""
       print("%s\t%s\t%s" % (summary, id, primary))

if __name__ == '__main__':
   main() 
```

上面的代码使用已经设置好的凭证调用日历 API，它将获取所有的日历。

当第一次运行上述代码时，将在默认浏览器中打开一个 google auth 链接，寻求访问日历的许可，在获得许可后，将保存一个令牌，该令牌可用于将来向 Google Calendar API 发出请求。

代码运行后，您将在终端的控制台上看到您创建的所有日历的名称。

如果你想做更多的事情，我们已经创建了一篇带有截图的深度文章- [如何用 python 自动化 google 日历](https://karenapp.io/articles/2019/07/how-to-automate-google-calendar-with-python-using-the-calendar-api/)

完整的源代码可以在我们的 github 上找到[。](https://github.com/karenapp/google-calendar-python-api)