# 阻止 Spotify 应用程序上的广告

> 原文：<https://dev.to/ashwinkshenoy/block-ads-on-spotify-app-3147>

每个人都喜欢 Spotify，有时在开发阶段，但厌倦了它在歌曲之间不断出现的广告。这里有一种方法可以阻止广告通过你的主机文件，不管是 Windows 还是 Mac。

主持人内容:

```
# — Spotify Ad Block
127.0.0.1 media-match.com
127.0.0.1 adclick.g.doublecklick.net
127.0.0.1 googleads.g.doubleclick.net
127.0.0.1 http://www.googleadservices.com
127.0.0.1 pagead2.googlesyndication.com
127.0.0.1 desktop.spotify.com
127.0.0.1 pubads.g.doubleclick.net
127.0.0.1 audio2.spotify.com

127.0.0.1 crashdump.spotify.com
127.0.0.1 adeventtracker.spotify.com
127.0.0.1 log.spotify.com
127.0.0.1 analytics.spotify.com
127.0.0.1 ads-fa.spotify.com
127.0.0.1 audio-ec.spotify.com
127.0.0.1 heads-ec.spotify.com
127.0.0.1 prod.spotify.map.fastlylb.net
127.0.0.1 sto3.spotify.com
127.0.0.1 spclient.wg.spotify.com
127.0.0.1 upgrade.spotify.com
# — Spotify Ads Block End 
```

将上述内容添加到您的主机文件中。

在 Windows 中

转到`C:\Windows\System32\drivers\etc`。以管理员身份打开主机文件，并将上述内容添加到其中。(您可以以管理员身份打开 notepad/Sublime/Atom，并浏览打开主机文件。)

注意:如果新添加的映射不起作用，请尝试重新启动 Windows 以使其生效

在 Mac 中

若要查找主机文件，请打开 Finder，并在 Finder 的菜单栏中选择“前往”>“前往文件夹”。在框中，键入以下位置，然后按 Return 键。

`/private/etc/hosts`
要打开它，只需双击，它将在文本编辑(或您选择的文本编辑器)中显示文件的内容。在其中添加主机内容并保存。

在大多数情况下，新的映射应该立即工作，但是如果您没有看到正确的行为，您可能需要刷新您的 DNS 缓存。对于 OS X 狮子和 OS X Mountain Lion，请打开“终端”并使用以下命令。请注意，您需要输入您的管理员密码来执行它:

`sudo killall -HUP mDNSResponder`

对于 OS X Mavericks +，请改用以下命令:

`dscacheutil -flushcache; sudo killall -HUP mDNSResponder`

您也可以启动“终端”，键入以下命令，然后按下 Return 键。与所有 sudo 命令一样，您还需要输入您的管理员密码来执行它:

`sudo nano /private/etc/hosts`

完成更改后，按 Control-X 键退出编辑器，按 Y 键保存，然后按 Return 键覆盖现有主机的文件。如前所述，如果发现新的映射不能正常工作，请确保刷新 DNS 缓存。

请在下面的评论部分让我知道它是否有效或者是否需要主机文件内容的任何改变。

写吧。分享你所知道的/如何修理/打破/修理东西:)

I love the Web. ❤️❤️❤️