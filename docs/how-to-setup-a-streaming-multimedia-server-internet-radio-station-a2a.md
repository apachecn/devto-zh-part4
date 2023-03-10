# 如何设置流媒体服务器(网络电台)

> 原文：<https://dev.to/djemos/how-to-setup-a-streaming-multimedia-server-internet-radio-station-a2a>

这是针对 slackel linux 的。相同的设置可以在任何 linux 发行版中使用。您必须在您的 linux 发行版中安装 icecast、mpd 和 gmpc(可选)。

什么是 Icecast

Icecast 是免费的流媒体服务器软件。Icecast 是一个向听众传输音频数据的程序。

什么是 mpd

mpd 是一个音乐播放器守护程序-一个播放音乐的守护程序

什么是 gmpc

Gnome 音乐播放器客户端(gmpc)是 mpd 的 gui 前端

让我们开始:

*   获取一个 dns 域

“获得一个 dns 域”并创建一个动态 DNS 提供商(如[https://www.noip.com](https://www.noip.com))
端口转发 tcp，udp 端口 8000 使用您的路由器接口
我希望您知道如何做到这一点。例如，如果您的笔记本电脑具有内部 ip 192.168.2.4，您必须将路由器端口转发 192.168.2.4 设置为 8000 端口，以便用户可以从外部通过 192.168.2.4 内部 ip 联系到您

*   获取和安装 icecast mpd 及其 gui 前端 gmpc

    ```
     sudo slapt-get -u
     sudo slapt-get -i icecast mpd gmpc 
    ```

*   设置 icecast 配置文件

打开文件/etc/icecast.xml 并更改一些设置:

```
<source-password>hackme</source-password> to whatever you like
<relay-password>hackme</relay-password> to whatever you like
<admin-user>admin</admin-user> to whatever you like
<admin-password>hackme</admin-password> to whatever you like
<shoutcast-mount>/live</shoutcast-mount> to whatever you like, if it is not set then default /stream will be used e.g. set it to live

<hostname>localhost</hostname> to your dynamic domain name e.g. mediaserver.noip.me

 <!-- add this if you want your station listing on icecast directory, so people all over the world can find you -->
 <directory>
    <yp-url-timeout>15</yp-url-timeout>
    <yp-url>http://dir.xiph.org/cgi-bin/yp-cgi</yp-url>
    <public>1</public>
 </directory>
 <security>
  <chroot>0</chroot>
   <changeowner>
     <user>nobody</user>
     <group>nogroup</group>
   </changeowner>
  </security> 
```

保存文件/etc/icecast.xml

*   准备好你的 mp3 文件目录

将您的 mp3 文件放在一个目录下，例如~ ~/Desktop/MP3

*   创建您的。mpdconf
    运行

    ```
     mpd-initial.sh 
    ```

一个文件~/。mpdconf 将在您的主目录中创建

*   编辑该文件，并将其更改如下

    ```
    music_directory     "~/Desktop/mp3"
    playlist_directory      "~/.mpd/playlists"
    log_file            "~/.mpd/log"
    pid_file            "~/.mpd/pid"
    state_file          "~/.mpd/state"
    bind_to_address "localhost"
    port                "6600"
    input {
        plugin "curl"
     }
    audio_output {
    type        "shout"
    encoding    "mp3"           # optional
    name        "Ariadni Greek Radio"
    host        "localhost"
    port        "8000"
    mount       "/live" # same as above in icecast.xml
    password    "hackme" # password_same_as icecast_server_password_above"
    bitrate     "128"
    format      "44100:16:2"
    description "Watever you like e.g Rock" # optional
    url  "http://mediaserver.noip.me"   # optional but same as above in icecast.xml
    genre   "your genre e.g. Greek"     # optional
    public  "yes"     # optional
    mixer_type    "software"  # optional
    }
    # Need this so that mpd still works if icecast is not running
    audio_output {
    type "alsa"
    name "fake out"
    driver "null"
    mixer_type      "software"
    }
    # Character Encoding
    # If file or directory names do not display correctly for your locale then you 
    # may need to modify this setting.
    #
    filesystem_charset "UTF-8"
    #
    # This setting controls the encoding that ID3v1 tags should be converted from.
    #
    id3v1_encoding  "ISO-8859-1"
    # 
    ```

*   创建播放列表

    ```
    find ~/Desktop/mp3/*.mp3 > ~/.mpd/playlists/myplaylist.m3u 
    ```

*   运行 icecast 和 mpd

    ```
    sudo icecast -c /etc/icecast.xml -b
    mpd ~/.mpdconf 
    ```

*   从菜单运行 gmpc，停止或播放您喜欢的歌曲。
    检查控制菜单下的“重复模式”和“随机模式”

提示:
可以创建一个文件~/icecast.sh 包括这几行

```
 sudo icecast -c /etc/icecast.xml -b
    mpd ~/.mpdconf 
```

并运行它，而不必每次都要运行您的媒体服务器时再次键入。只是打字

```
 sh ~/icecast.sh 
```

你和你的听众可以通过在浏览器或 vlc 或 clementine 或 smplayer 中输入 URL
[http://mediaserver.noip.me:8000/live](http://mediaserver.noip.me:8000/live)来收听你的网络电台

在 slackel.gr 站点，ariadni radio[http://ariadni.noip.me:8000/live](http://ariadni.noip.me:8000/live)已经如上所述设置好，并从我的笔记本电脑上传。