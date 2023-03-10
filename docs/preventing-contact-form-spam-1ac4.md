# 阻止联系人发送垃圾邮件

> 原文：<https://dev.to/mdgeus/preventing-contact-form-spam-1ac4>

# 联系人表单

每个在网站上有联系方式的人都会收到垃圾邮件。这是事实。

[![Alt Text](img/6b96cecffc2850c1d3d1b018065fc499.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9wZs2Brh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lmytkh3rmdvucanvb5m.jpg)

我不喜欢验证码。尤其是当你在经营一家 B2B 网络商店时，你不希望你的客户点击所有的汽车或桥梁，他们会停止填写表格，转到另一个网站，在那里他们可以输入他们的问题并点击发送按钮，这样你就失去了一笔潜在的销售。

我想我已经通过增加一些检查把垃圾信息减少到了最低限度。

## 表单

第一步是在表单上。添加一个输入字段，但是使用 css 来隐藏它。

当一个机器人抓取你的网站并填写表格时，它会填写这个字段。这表明它是垃圾邮件，因为访问者不会填写此字段。
在我的例子中，我只需要客户姓名、电话、电子邮件和消息，所以我添加了一个隐藏的 url 字段。

[https://codepen.io/mdgeus/embed/zYOzdMy?height=600&default-tab=html,result&embed-version=2](https://codepen.io/mdgeus/embed/zYOzdMy?height=600&default-tab=html,result&embed-version=2)

这是一条关于
的线

`<div id="antispam"><label for="url">URL :</label><input id="url" class="form-control" autocomplete="off" name="url" type="text" /></div>`

此外，不要忘记自动完成="off"
我们不希望我们的访问者自动填充系统自动填充这个领域

您只需在发送消息前检查该字段。所以在我的例子中，我在处理表单的控制器中做这件事。

`if (
(null != $this->input->post(‘url’)) ||
(‘’ == $this->input->post(‘message’)) ||
(preg_match(“/\[url=/i”, $this->input->post(‘messasge’))) || (preg_match(“/^[0–9 ]*$/”, $this->input->post(‘message’))) || (preg_match(“/(girls|sex|qualify|viagra|dating|blackjack|cryptocurrency|money|gagner|Weight|cbd|cannabis|fuck|surveys|forex|invest|australians|Madchen|Marihuanan)/i”, $this->input->post(‘message’)))
) { // mark as spam !`

(是的，我使用 codeigniter 框架)

首先检查 url 字段是否不为空(这意味着 url 字段有值，并且必须是垃圾邮件)。第二个检查是消息本身是否为空。
当然，我会在发送表单前检查这一点，但我注意到有时(可能是旧浏览器或禁用了 javascript 等。)，它将通过该测试，并且可以发送带有空消息字段的表单

第三个检查是看正文中是否有“[url=”。这就是你如何在 markdown 标记语言中显示一个 url，并且你网站的普通访问者不会在你的 contactform 中使用 markdown。

然后，我检查消息是否不仅包含数字。我知道这很奇怪，但是我们收到了很多没有文字，只有数字组合的联系表单条目。因此，当消息字段中有一个单词时，它将通过这一测试，并且不会被定义为垃圾邮件。

最后一行是别的。这些是我们收到的信息中的独特词汇。
诀窍是找出一个只在垃圾短信中使用的独特的词，当你的客户想问你一些事情时，他们不会使用这个词。
这个单词列表会随着时间的推移而增长。我只是在另一条垃圾邮件通过测试时添加新单词。

到目前为止，我已经将垃圾短信的数量减少到了零。