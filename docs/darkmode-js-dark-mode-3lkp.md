# Dark Mode.js–Add Darkmode dark theme, dark version and automatic night mode conversion function to Quick Help website.

> 原文：<https://dev.to/slivenred/darkmode-js-dark-mode-3lkp>

Nowadays, many apps or websites support **[dark theme](https://techmoon.xyz/darkmode/)** , such as Youtube, Chrome, Facebook Messenger… and so on, all of which provide **night mode theme switching** .

If you want to **quickly help your website to add the dark theme conversion function** of DarK Mode, then using [T2】 Darkmode.Js 【T3] introduced today can help you **quickly realize the function of automatically converting dark modes with one button** . Let your website, like other big companies' websites, switch to night mode **quickly** .

[![Darkmode.Js - 快速幫網站加入 Dark Mode 深色主題、黑暗版本、夜間模式自動轉換功能 1](img/731f4a11074f1cb779a2048f29e60758.png "Darkmode.Js - 快速幫網站加入 Dark Mode 深色主題、黑暗版本、夜間模式自動轉換功能 2")](https://res.cloudinary.com/practicaldev/image/fetch/s--mjjVfNWh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/practicaldev/image/fetch/s--d-toqjZt--/c_limit%252Cf_auto%252Cfl_progressive%252Cq_66%252Cw_880/https://i.imgur.com/vzexItp.gif)

網站名稱:黑暗模式。Js

網站連結:【https://darkmodejs.learn.uno/ T2】

**directory**

[1 黑暗模式。射流研究…教學](https://techmoon.xyz/darkmode/#DarkmodeJs_jiao_xue)

[2 黑暗模式。Js 与 WordPress](https://techmoon.xyz/darkmode/#DarkmodeJs_with_WordPress)

[T0】 2.1 Related Contents

[T0】 2.2 Guess you like

## 黑暗模式。射流研究…教學

It is very simple to use [T0】 Darkmode.Js 【T1] to realize the dark theme function of Dark Mode. First, just add the following code to the website:

```
<script src="https://cdn.jsdelivr.net/npm/darkmode-js@1.4.0/lib/darkmode-js.min.js"></script>
<script>
  new Darkmode().showWidget();
</script> 
```

After joining, you can use the Option function in Darkmode.Js to set the style of your toggle button. Therefore, you can change the position, color, trigger time or displayed icon… of the button for switching dark themes at will … and so on.

Then you can use option code to personalize the style and position of the mode switch button, such as left or right, background color, trigger time, icon inside and so on.

```
var options = {
  bottom: '64px', // default: '32px'
  right: 'unset', // default: '32px'
  left: '32px', // default: 'unset'
  time: '0.5s', // default: '0.3s'
  mixColor: '#fff', // default: '#fff'
  backgroundColor: '#fff',  // default: '#fff'
  buttonColorDark: '#100f2c',  // default: '#100f2c'
  buttonColorLight: '#fff', // default: '#fff'
  saveInCookies: false, // default: true,
  label: '🌓', // default: ''
  autoMatchOsTheme: true // default: true
}

const darkmode = new Darkmode(options);
darkmode.showWidget(); 
```

Therefore, through the above simple ways, you can quickly add the dark version mode of dark theme to your website.

## 黑暗模式。使用 WordPress 的 Js

If you are currently using a WordPress website, then at present, some people **use the WordPress plug-in** developed based on Darkmode.js, so that your WordPress website can quickly **apply the dark theme mode of Darkmode.js** :

【WordPress 的黑暗模式

作者: [Optimocha](https://optimocha.com/)

当前版本:1.0.0

最后更新时间:2019-06-26

[darkmode.1.0.0.zip](https://downloads.wordpress.org/plugin/darkmode.1.0.0.zip)

[80% *收视率*](https://wordpress.org/support/view/plugin-reviews/darkmode) [10+ *装机*](https://downloads.wordpress.org/plugin/darkmode.1.0.0.zip) [WP 2.1+ *需求*](https://wordpress.org/plugins/darkmode/)

[![Blackout: Dark Mode Widget](img/3a31c4f7724811d897c1310400f10b12.png "Darkmode.Js - 快速幫網站加入 Dark Mode 深色主題、黑暗版本、夜間模式自動轉換功能 3")停电:黑暗模式小工具](https://wordpress.org/plugins/blackout-darkmode-widget/)

作者:[何塞·索特罗](https://inboundlatino.com/)

当前版本:1.2.0

最后更新时间:2019-08-13

[blackout-dark mode-widget . zip](https://downloads.wordpress.org/plugin/blackout-darkmode-widget.zip)

[100% *收视率*](https://wordpress.org/support/view/plugin-reviews/blackout-darkmode-widget) [60+ *装机*](https://downloads.wordpress.org/plugin/blackout-darkmode-widget.zip) [WP 4.7+ *要求*](https://wordpress.org/plugins/blackout-darkmode-widget/)

These two WordPress plug-ins can help your WordPress website quickly realize the night mode based on Darkmode.Js After installation, you can immediately display the switch button.

Let's take a look at the actual display in the TechMoon website:

[![é»‘æš—æ¨¡å¼ GIF](img/031b6df98a5b497cdc468910231ff5e7.png "Darkmode.Js - 快速幫網站加入 Dark Mode 深色主題、黑暗版本、夜間模式自動轉換功能 4")](https://i.giphy.com/media/KEf9KHwdwd1352rICi/giphy.gif)

If you want to know more about [Darkmode.Js, you can go to official website to learn more.](https://darkmodejs.learn.uno/?ref=techmoon)

That's all for today's introduction. If you like today's introduction, you might as well help share it and let more people know. Please also leave your achievement display below!