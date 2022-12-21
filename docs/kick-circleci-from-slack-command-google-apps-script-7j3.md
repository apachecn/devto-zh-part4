# 从 Slack command + Google Apps 脚本踢 CircleCI

> 原文：<https://dev.to/acro5piano/kick-circleci-from-slack-command-google-apps-script-7j3>

我已经用 CircleCI + Slack + Google Apps 脚本实现了应用部署的自动化。

1.  创建空闲机器人

2.  获取 Slack Webhook 令牌

3.  创建 CircleCI 作业

4.  获取 CircleCI 令牌

5.  创建 Google 企业应用套件脚本:

```
const GITHUB_REPO = 'account/repo'
const CIRCLE_TOKEN = '***'
const SLACK_WEBHOOK_URL = 'https://hooks.slack.com/services/***/***/***'

function doPost(e) {
  const branch = e.parameters.text[0];

  _deployToCircle('deploy', branch);

  return ContentService.createTextOutput('ok');
}

function _deployToCircle(circleJob, branch) {
  const url = 'https://circleci.com/api/v1.1/project/' + GITHUB_REPO + 'tree/' + branch + '?circle-token=' + CIRCLE_TOKEN;
  const payload = {
    build_parameters: {
      CIRCLE_JOB: circleJob,
      STAGING: 'true', // additional params
    }
  }
  const circleRes = UrlFetchApp.fetch(url, {
    method: 'post',
    muteHttpExceptions: true,
    contentType: 'application/json',
    payload: JSON.stringify(payload),
  });
  Logger.log(circleRes.getResponseCode());
  Logger.log(circleRes.getContent());

  const message = 'branch deploy start: ' + branch + '\nCircleCI response: ' + circleRes.getResponseCode()

  const slackRes = UrlFetchApp.fetch(SLACK_WEBHOOK_URL, {
    method: 'post',
    contentType: 'application/json',
    payload: JSON.stringify({ text: message }),
  });
  Logger.log(JSON.stringify(slackRes));
}

function doPostTest() {
  doPost({
    parameters: {
      text: ['branch_name_to_deploy'],
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  设置 Slack 命令来启动 Google Apps 脚本