# 如何在 openbox 上创建多音体

> 原文：<https://dev.to/djemos/how-to-greek-polytonic-on-openbox-55a2>

不仅有希腊语用户，还有其他用户想要编写希腊语多音体，例如在大学中。这可能对任何感兴趣的人都有帮助。

在 LibreOffice 和 Openbox 上编写希腊文多音体、单调和古代、现代拼写检查。在 slackel 中，所有这些都是在实时 iso 中设置的，并且是在将系统安装到硬盘或 u 盘上之后设置的。所以对于懒鬼用户来说，没有必要遵循这些步骤。

= = set key = =
用 geany 打开文件~/。config/openbox/rc.xml
例如 geany ~/。config/openbox/rc.xml

*   转到第 342 行(或者用/keybind 找到最后一行，并添加下面的行)

    ```
     <keybind key="C-1">
               <action name="Execute">
                <command>sh ~/.polytonic</command>
                 <startupnotify>
                  <enabled>yes</enabled>
                  <name>Polytonic/Monotonic</name>
                 </startupnotify>
                </action>
               </keybind> 
    ```

*   保存文件

==创建多面体文件==
创建文件~/。多音调的

geany ~/。多面体并粘贴线条

#!/bin/sh
# Dimitris Tzemos [dijemos@gmail.com](mailto:dijemos@gmail.com)
# Αρχείο για αλλαγή μονοτονικού πολυτονικού πληκτρολογίου
# για συγγραφή αρχαίων κειμένων
# File to change from greek monotonic to greek polytonic keyboard
# to type ancient greek documents.
#
check=$(setxkbmap -print | grep 'extended')
if [ -z "$check" ];then
setxkbmap us,el -variant ,extended
zenity --info --timeout 1 --text 'Μονοτονικό πληκτρολόγιο ενεργό' 1
else
setxkbmap us,el -variant ,polytonic
zenity --info --timeout 1 --text 'Πολυτονικό πληκτρολόγιο ενεργό' 1
fi
if [ -r /etc/X11/Xmodmap ];then xmodmap /etc/X11/Xmodmap;fi
if [ -r "$HOME"/.Xmodmap ];then xmodmap "$HOME"/.Xmodmap;fi

*   保存文件

==Messages ==
Μονοτονικό πληκτρολόγιο ενεργό = Monotonic keyboard active

Πολυτονικό πληκτρολόγιο ενεργό = Polytonic keyboard active
you can change greek messages with english ones above if you like.

现在，每次按 Ctrl+1 时，在单调到多音和多音到单调之间变化

==Libreoffice==
对于现代和/或古希腊语的希腊语拼写，请从 libre office tools->extensions 下载并安装这些文件

现代希腊语
[http://SourceForge . net/projects/aoo-extensions/files/1411/2/El _ gr _ v 110 . oxt/download](http://sourceforge.net/projects/aoo-extensions/files/1411/2/el_gr_v110.oxt/download)

古希腊文
[http://SourceForge . net/projects/aoo-extensions/files/2313/1/GRC . oxt/download](http://sourceforge.net/projects/aoo-extensions/files/2313/1/grc.oxt/download)

就是这样！！！现在可以在 libreoffice 中书写希腊语现代或多音体，并且有拼写检查功能。

要书写希腊文多音体，请按 alt-shift 更改键盘，然后按 ctrl+1 转到多音体。

== LINUX 上多音字符的键盘输入= =

ά= τόνος + γράμμα (γιὰ ὁξεῖα)
ὰ=εἰσαγωγικά + γράμμα (γιὰ βαρεῖα)
ἁ=shift+εἰσαγωγικά+γράμμα
ἀ=shift+τόνος+γράμμα
ᾶ=ἀγκύλη ἀριστερή+γράμμα
ᾳ=ἀγκύλη δεξιά+γράμμα
ᾷ+αγκύλη δεξιά+ἀγκύλη ἀριστερή + γράμμα
ἆ=ἀγκύλη ἀριστερή+shift τόνος+γράμμα
ἇ=ἀγκύλη ἀριστερή+shift εἰσαγωγικά
ἄ=τόνος+shift τόνος+γράμμα
ἅ=shift εἰσαγωγικά+τόνος+γράμμα
ΐ=Alt Gr+τόνος σε μη πολυτονική γραμματοσειρά

口音

; acute accent (ὀξεῖα)
' grave accent (βαρεῖα)
[ circumflex (περισπωμένη)

；a〖t0〗a〗t1〖a〗

呼吸

:平稳的呼吸(ψιλὸν πνεῦμα)【粗暴的呼吸(δασὺ πνεῦμα)】

:a〖t0〗a

利尿

{ diaresis (διαλυτικά)

我是ϊ

结合:呼吸前的口音
[:一个ἆ
:一个ᾀ
][:一个ᾆ
]“一个ᾀ
[”一个ἆ
；:一个ἄ
’；一个ἃ
’；一个ἅ
”；一个ἂ

== ancient Greek polytonic from Homer Iliad
Μῆνιν ἄειδε θεὰ Πηληϊάδεω Ἀχιλῆος
οὐλομένην, ἣ μυρί᾿ Ἀχαιοῖς ἄλγε᾿ ἔθηκε,
πολλὰς δ᾿ ἰφθίμους ψυχὰς Ἄϊδι προΐαψεν
ἡρώων, αὐτοὺς δὲ ἑλώρια τεῦχε κύνεσσιν
οἰωνοῖσί τε πᾶσι· Διὸς δ᾿ ἐτελείετο βουλή,
ἐξ οὗ δὴ τὰ πρῶτα διαστήτην ἐρίσαντε
Ἀτρεΐδης τε ἄναξ ἀνδρῶν καὶ δῖος Ἀχιλλεύς.

== above text in modern Greek monotonic (translation)
Ψάλλε θεά, τον τρομερό θυμόν του Αχιλλέως
Πώς έγινε στους Αχαιούς αρχή πολλών δακρύων.
Που ανδράγαθες ροβόλησε πολλές ψυχές στον Άδη
ηρώων, κι έδωκεν αυτούς αρπάγματα των σκύλων
και των ορνέων – και η βουλή γενόταν του Κρονίδη,
απ’ ότ’, εφιλονίκησαν κι εχωρισθήκαν πρώτα
ο Ατρείδης, άρχος των ανδρών, και ο θείος Αχιλλέας.

歌唱，女神啊，佩琉斯之子阿喀琉斯的愤怒，给亚该亚人带来了无数的灾难。
许多勇敢的灵魂被它送进了冥府，
许多英雄被狗和秃鹫，
因为从人类之王阿特柔斯的儿子和伟大的阿喀琉斯
第一次争吵的那一天起，朱庇特的建议就这样实现了。