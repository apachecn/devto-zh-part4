# 构建一个快捷聊天应用程序

> 原文：<https://dev.to/pubnub/how-to-build-an-ios-mobile-group-chat-app-with-swift-41f2>

各种形状和大小的手机聊天，无论是独立的[群发消息应用](https://www.pubnub.com/solutions/chat?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)，嵌入式客户服务小工具，还是约会应用中的[私人一对一聊天，无处不在。](https://www.pubnub.com/solutions/chat/dating-apps?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)

在本教程中，我们将向您展示如何使用 Swift 5 创建一个 iOS 移动聊天应用程序，允许任意数量的用户进行实时聊天。我们还将向您展示如何存储消息历史，以便当用户离开和返回时，他们的消息仍在应用程序中。

为此，我们将使用 PubNub 的几个关键特性:[发布/订阅](https://www.pubnub.com/developers/tech/key-concepts/publish-subscribe?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)和[存储&回放](https://www.pubnub.com/developers/tech/key-concepts/message-caching-persistence?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)(消息存储)。

*   **发布**是每个客户端将消息发布给外界的方式，或者至少发布给发布的渠道。这是使用发布/订阅的开始，你发送的每条消息都将被发送给订阅了你发布的频道的任何人。发布需要一个 PubNub 连接的实例(我将在后面更详细地介绍)、消息(类型为 String、NSNumber、Array 和 Dictionary)以及我们希望将消息发送到的通道。[了解更多关于 Swift 发布的信息。](https://www.pubnub.com/docs/swift/api-reference-publish-and-subscribe#publish?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)
*   **订阅**是通过 PubNub 进行即时交流的后半部分。为了订阅，我们需要一个 PubNub 连接的实例和一个订阅通道。成功订阅后，我们将收到消息，但我们仍然看不到它们，因为当它们到达时，我们没有什么可处理的。[了解更多关于订阅 Swift 的信息。](https://www.pubnub.com/docs/swift/api-reference-publish-and-subscribe#subscribe?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)
*   **事件处理或监听**更新在 PubNub 的生命周期中非常重要。Pub/Sub 引人注目，但使用 PubNub 的隐藏冠军是将[数据流网络](https://www.pubnub.com/products/global-data-stream-network?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)连接到我们的控制台和应用程序的事件处理程序。一个专门监听消息，另一个负责查看包括订阅更改和错误在内的任何其他内容。
*   存储&回放是又一个很棒的功能。如果存储和检索消息是您用例的重要部分，那么存储&回放也是您应用程序的一大亮点。其功能允许检索历史信息。应用程序消息的生存时间范围从时间开始到最后一天。一旦我们设置了 PubNub 帐户并获得了 API 密钥，我们将在 PubNub 管理仪表板中设置存储的生存时间。[在 Swift](https://www.pubnub.com/docs/swift/api-reference-storage-and-playback?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26) 中了解更多关于存储&回放的信息。

完成本教程后，你将拥有一个提供聊天室服务的应用程序，它是任何应用程序的基础和补充。

【Swift 5 的完整 iOS 聊天应用[在这里](https://github.com/SambaDialloB/PubNubChat)可用。

## 在 Xcode 中使用 PubNub 创建应用程序

### PubNub

如果你还没有，[注册一个 PubNub 账户](https://dashboard.pubnub.com/signup?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)。登录后，创建一个新应用程序。单击它，或者创建一个新的密钥集，或者单击已经可用的演示密钥集。你现在应该看到一个发布和订阅键，这些键允许我们使用 PubNub API。

在键下，我们有不同的选项可以启用！让我们在左下方启用存储和回放功能。现在，您可以决定希望您的邮件保留多长时间。我选择了一天的保留时间，并保存了更改。在保留设置下，有启用从 PubNub 历史中删除[的设置。](https://www.pubnub.com/docs/swift/api-reference-storage-and-playback#delete-messages-from-history?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-Jun-26)

### Xcode 应用程序设置

打开 Xcode 并创建一个新项目，使它成为一个单视图应用程序，命名它，然后关闭项目。使用终端导航到您的项目文件夹，运行命令 gem install cocoapods 或 gem update cocoapods(如果您已经安装了 cocoapods)。

在您的终端中输入 touch Podfile，为您的应用程序创建 Podfile，然后使用 open Podfile 打开该文件。

将这个输入到文件中，确保将“应用程序目标名称”替换为您项目的名称。

```
source ‘https://github.com/CocoaPods/Specs.git'
# optionally complete and uncomment if compilation issues arise
# project ‘<path to project relative to this Podfile>/<name of project without extension>’
# workspace ‘MyPubNubProject’
use_frameworks!
# replace next lines name in quote with your project name
target ‘application-target-name’ do
# Should only use this with projects
# that must have a minimum deployment
# target of iOS 8
platform :ios, ‘8.0’ # (or ‘9.0’ or ‘10.0’)
pod “PubNub”, “~> 4”
end 
```

之后，在您的终端中运行命令 pod install。这将为您在项目中安装 PubNub 框架。安装完成后，双击。xcworkspace 文件，而不是普通的项目文件。

## 在 Swift 中设计聊天故事板

在我们开始放入所有的逻辑之前，让我们设计并建立我们的应用程序的视图。让我们从登录视图开始。

将 ViewController.swift 重命名为 ConnectVC.swift，方法是在类声明中突出显示该名称，然后进入编辑器->重构->重命名。

当用户打开应用程序时，除了连接按钮之外，我们希望他们有一个字段来输入用户名和他们想要连接的频道。将这些添加到您的第一个视图中。此外，把一个很酷的标题，你想叫这个应用程序，我选择了局部！

然后，通过按住 control 键从故事板上的项目拖动到 ConnectVC 文件，我为我的用户名和频道文本字段制作了出口。对你的按钮做同样的事情，但是创建一个 UIButton 类型的动作，让它在被按下时运行。

[![Xcode screenshot swift storyboard chat app with PubNub](img/8cfc22ba3ae32a1ce53ca60c9d238b93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c6uR4zsO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/04/xcode-storyboard-swift-chat-pubnub.png)

接下来，让我们创建频道聊天视图。

创建一个新的 Cocoa Touch 类，并将其命名为 ChannelVC。在故事板中创建一个新的视图控制器，并将类设置为 ChannelVC。选择视图时，在屏幕顶部点击编辑器->嵌入->导航控制器。另一个视图现在应该在你的故事板中。这是导航控制器，它允许用户在视图之间移动。

将 UIBarButtonItem 添加到频道导航栏的左侧，这将是离开按钮。按住 Control 键将它拖到 ChannelVC.swift 中，并创建一个名为 leaveChannel 的 UIBarButtonItem 类型的操作。将 UITableView 视图拖到 ChannelVC 视图中。让它占据大部分屏幕，为另一个文本字段和一个包含“发送”文本的按钮留出足够的空间。也创造那些。

为 ChannelVC.swift 中的表格和 TextField 创建出口，并为 send 按钮创建另一个操作。

我们的下一步不涉及 ChannelVC，而是在表中创建自定义单元格。一旦我们有了 ChannelVC 设置的总体布局，我们就必须定制 tableView 中的单元格。创建一个名为 MessageCell 的新 cocoa touch 类，并将一个 UITableViewCell 拖动到您的表视图中。将 cells 类设置为新类，并将标识符也更改为 MessageCell。

拖动您想要的任何设计，包括您认为用例所必需的任何细节。我将用户名和消息标签放入单元格中，一旦完成，按住 control 键并拖动鼠标来创建 MessageCell 类的出口。请确保设置约束，以便表格不会挤压您的内容。

*关于如何让你的应用程序在所有屏幕尺寸下都能工作的更多信息，请参考[苹果公司关于自动布局](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/index.html)的文档或网上提供的众多指南。*

拥有所有这些视图是很好的，但是如果它们不能从一个视图移动到另一个视图就不好了。单击 ConnectVC 上方以其命名的栏，然后单击黄色圆圈。按住 Control 键将它拖到导航控制器上，然后选择“显示”选项。当导航控制器的线被选中时，点击你右边面板的属性标签，上面写着“故事板继续”。将标识符命名为“connectSegue”。这将允许您在单击 ConnectVC 上的连接按钮时执行此步骤。

我们需要的下一个也是最后一个片段是从 ChannelVC 到 ConnectVC。选择 ChannelVC，方法与我们选择 ConnectVC 的方法相同，并将其拖动到 ConnectVC。这一次，在属性检查器中选择“模态呈现”并将其命名为“leaveChannelSegue”。

[![Storyboard Xcode for PubNub Chat](img/9b834d98c0a7ddce8f1f744befbecbc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cBFk-5Tt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/04/xcode-storyboard-screenshot-chat-pubnub.png)

## 将用户连接到频道

现在我们已经完成了故事板，让我们开始编码。我们将从 ConnectVC 开始，它为我们的 ChannelVC 提供用户名和通道，在那里我们将利用我们所有的 PubNub 知识。首先，在我们的连接操作中执行一个 segue。

```
@IBAction func connectToChannel(_ sender: UIButton) {
    self.performSegue(withIdentifier: "connectSegue", sender: self)
} 
```

这利用了我们在上一节中建立的连接，将我们带到 ChannelVC 的 nav 控制器。在这个视图控制器中，我们需要做的另一件事就是为上面的场景做准备。通过覆盖这个函数，我们在视图之间发送信息。

***注:**在本教程中，如果用户没有提供用户名，我会自动给他们分配一个名字“一只淘气的驼鹿”。如果他们不提供通道，我会将他们发送到“常规”通道*

为了访问我们要去的视图，我们获取导航控制器的一个实例，然后从那里获取 ChannelVC 视图。我们检查文本字段是否为空，如果需要的话替换这些值，然后在 ChannelVC 中设置两个变量，这两个变量我们还没有创建，分别是我们的用户名和通道值。

```
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {

    //Accessing Nav Controller and ChannelVC view
    if let navigationController = segue.destination as? UINavigationController,
        let channelVC = navigationController.viewControllers.first as? ChannelVC{
        var username = ""
        var channel = ""

        //Replacing empty values with default ones
        if(usernameTextField.text == "" ){
            username = "A Naughty Moose"
        }
        else{
            username = usernameTextField.text ?? "A Naughty Moose"
        }
        if(channelTextField.text == "" ){
            print("nothing in channel")
            channel = "General"
        }
        else{
            channel = channelTextField.text ?? "General"
        }

        //Setting values in ChannelVC
        channelVC.username = username
        channelVC.channelName = channel
    }
} 
```

## 用 PubNub 创建聊天视图控制器

在我们的 ChannelVC 中，我们应该有两个出口，一个 action 和我们的 viewDidLoad 函数。最重要的是，在类定义下，我们将开始定义一些变量和资源，它们是我们在这个类的其余部分需要的。

首先，让我们的类监听 PubNub 事件，并让它与我们的表一起工作。在文件顶部导入 PubNub，在我们的类定义 UIViewController 之后放入 PNObjectEventListener、UITableViewDataSource 和 UITableViewDelegate。我们的类现在应该显示一个错误，单击该错误并添加那些建议的存根，我们将在一秒钟内填写它们。

*   就在我们的类定义下面，让我们定义一个结构，使处理我们的消息更容易一些。我的结构有三个字符串:消息、用户名和 UUID。稍后当我们发布消息时，您可以发送不同的信息并用这些更新来更新结构。
*   之后，创建一个 Message 数组并将其初始化为空，因为所有的类变量都需要有某种初始值。
*   为我们收到的最早的消息创建一个 NSNumber 类型的标记，并将该标记作为-1 开始。
*   另一个变量跟踪我们是否已经开始加载更多的消息。
*   现在是这个视图控制器最重要的变量，发布和订阅的变量，我们的 PubNub 对象！这是我们将在这个视图控制器中调用 PubNub 函数的对象。
*   然后，我们有了用户在最后一步中输入的用户名和频道，并用临时值进行了初始化。
*   之后应该是我们的消息文本字段，我们的表视图，我们的和我们的发送动作。

```
class ChannelVC: UIViewController,PNObjectEventListener, UITableViewDataSource, UITableViewDelegate {

    //Our Message struct, makes working with messages a little easier
    struct Message {
        var message: String
        var username: String
        var uuid: String
    }
    var messages: [Message] = []

    //Keep track of the earliest message we loaded
    var earliestMessageTime: NSNumber = -1

    //To keep track if we are already loading more messages
    var loadingMore = false

    //Our PubNub object that we will use to publish, subscribe, and get the history of our channel
    var client: PubNub!

    //Temporary values
    var channelName = "Channel Name"
    var username = "Username"

    //-- ALREADY SHOULD BE IN YOUR FILE
    //Where our messages come in
    @IBOutlet weak var messageTextField: UITextField!

    //We populated this with the information from our messages array
    @IBOutlet weak var tableView: UITableView!

    //...

} 
```

接下来，我们已经建立了一些可以在整个代码中使用的全局变量，让我们建立我们的 viewDidLoad 函数。调用继承的 viewDidLoad 后，将导航控制器顶部的标题更改为通道名称，并将表视图的委托和数据源设置为 self。

```
self.navigationController?.navigationBar.topItem?.title = channelName

tableView.delegate = self
tableView.dataSource = self 
```

接下来，我们配置并初始化我们的 PubNub 对象。您可以在这里插入在 PubNub 帐户中找到的发布和订阅密钥。我们将 stripMobilePayload 设置为 false，因为它已被弃用，并给这个连接一个唯一的 UUID，这将使将来开发更多功能变得更加容易。我们初始化它，将自己设置为一个监听器，并订阅用户选择的频道。然后，我们调用接下来将创建的方法 loadLastMessages。

```
//Setting up our PubNub object!
let configuration = PNConfiguration(publishKey: "INSERT PUBLISH KEY", subscribeKey: "INSERT SUBSCRIBE KEY")
//Gets rid of deprecated warning
configuration.stripMobilePayload = false
//Making each connection identifiable for future development
configuration.uuid = UUID().uuidString
client = PubNub.clientWithConfiguration(configuration)
client.addListener(self)
client.subscribeToChannels([channelName],withPresence: true)

//We load the last messages to populate the tableview
loadLastMessages() 
```

现在应该有一个错误，说我们在 viewDidLoad 末尾调用的函数是未定义的，我们来定义它吧！该函数用于在我们连接到通道时加载初始消息。它利用了我们将创建的另一个名为 addHistory 的函数。

让我们调用下一个函数，使用 nil 作为开始和结束，然后设置您希望接收的消息数量，最大为 100。我们在这个函数中的最后一个动作是将我们的表格视图向下滚动到表格底部的新消息。

```
//This function is called when this view initialy loads to populate the tableview
func loadLastMessages()
{
    addHistory(start: nil, end: nil, limit: 10)
    //Bring the tableview down to the bottom to the most recent messages
    if(!self.messages.isEmpty){
        let indexPath = IndexPath(row: self.messages.count-1, section: 0)
        self.tableView.scrollToRow(at: indexPath, at: .bottom, animated: true)
    }
} 
```

### 消息历史的存储和回放

现在我们来看看允许我们回顾频道历史的功能。用几个关键参数创建它，只需要其中的限制，然后调用允许我们查看通道历史的基本函数。

我们使用函数 historyForChannel，它有许多重载版本。我们可以使用返回最近 100 条消息的简单方法，或者使用开始和结束时间的简单方法，这两种方法都由 PNHistoryResultBlock 处理，它允许我们访问查询结果和错误。

首先，让我们检查结果是否不为空，状态是否为，并开始访问消息！一旦我们知道我们的信息至少包含了一些东西，我们就可以开始访问它们。我们需要用我们在结果中收到的最早的消息来更新我们的 earliest message 开始时间。接下来转换对象，我们得到我们可以管理的东西，一个字符串键和值的数组。

从这个新对象中，我们不用每次都努力访问它们，而是从我们的结构中创建一个消息对象，将它们添加到一个临时数组中，然后将其插入到全局消息数组的开头。确保重新加载表格，然后检查错误！

```
//Get and put the histroy of a channel into the messages array
func addHistory(start:NSNumber?,end:NSNumber?,limit:UInt){
    //The PubNub Function that returns an object of X messages, and when the first and last messages were sent.
    //The limit is how many messages are received with a maximum and default of 100.
    client.historyForChannel(channelName, start: start, end: end, limit:limit){ (result, status) in
        if(result != nil &amp;&amp; status == nil){

            //We save when the earliest message was sent in order to get ones previous to it when we want to load more.
            self.earliestMessageTime = result!.data.start

            //Convert the [Any] package we get into a dictionary of String and Any
            let messageDict = result!.data.messages as! [[String:String]]

            //Creating new messages from it and putting them at the end of messages array
            var newMessages :[Message] = []
            for m in messageDict{
                let message = Message(message: m["message"]! , username: m["username"]!, uuid: m["uuid"]! )
                newMessages.append(message)
            }
            self.messages.insert(contentsOf: newMessages, at: 0)
            //Reload the table with the new messages and bring the tableview down to the bottom to the most recent messages
            self.tableView.reloadData()

            //Making sure that we wont be able to try to reload more data until this is completed.
            self.loadingMore = false
        }
        else if(status !=  nil){
            print(status!.category)

        }
        else{
            print("everything is nil whaaat")
        }
    }
} 
```

接下来，让我们填写我们的 tableView 所需的函数。第一个，numberOfRowsInSection 是一个简单的一行程序，返回数组中的消息数。对于第二种情况，我们首先需要获得消息单元格的实例，并将单元格标签的文本设置为消息和消息数组索引的用户名。之后，只要归还细胞！

```
//Tableview functions required.
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    //change later
    return messages.count
}

func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    let cell = tableView.dequeueReusableCell(withIdentifier: "MessageCell") as! MessageCell

    cell.messageLabel.text = messages[indexPath.row].message
    cell.usernameLabel.text = messages[indexPath.row].username

    return cell
} 
```

在 Swift 中使用和调试 PubNub 最重要的部分之一是为事件和消息创建监听器。在这个应用程序中，我们使用函数 didRecieveMessage 来访问进入我们通道的消息。这个函数内部的逻辑将充当我们的 loadLastMessages 的一个较小版本。

检查收到的消息是否与我们订阅的频道匹配，以防我们订阅了其他内容。接受给我们的消息，并将其转换为字符串键和值的数组。使用该字典创建一条消息，并将其附加到 messages 数组的末尾。

再次重新加载数据，并向下滚动到新消息。该操作可以根据您的使用情况进行更改。我在我的控制台中打印消息用于调试。

```
func client(_ client: PubNub, didReceiveMessage message: PNMessageResult) {
    //Whenever we receive a new message, we add it to the end of our messages array and
    //reload the table so that it shows at thebottom.

    if(channelName == message.data.channel)
    {
        let m = message.data.message as! [String:String]
        self.messages.append(Message(message: m["message"]!, username: m["username"]!, uuid: m["uuid"]!))
        tableView.reloadData()

        let indexPath = IndexPath(row: messages.count-1, section: 0)
        tableView.scrollToRow(at: indexPath, at: .bottom, animated: false)

    }

    print("Received message in Channel:",message.data.message!)
} 
```

现在，我们可以在第一次打开频道时加载一些最后的消息，当新的消息发出时，它们会出现在底部。

如果消息比我们最初加载的多怎么办？在这个新函数 scrollViewDidScroll 中，当我们从顶部向下拉时，我们从 historyForChannel 中拉出另一个数量的消息。这也可以修改，以便当用户没有到达页面顶部时，它将尝试检索更多内容，以获得更加无限的滚动效果。

我们有一个名为 loadingMore 的全局变量，我们在开始时检查是否已经加载了更多的消息，然后检查用户是否滚动超过了某个阈值以开始加载更多的消息。谢天谢地，使用 PubNub 的速度快得可笑，所以它几乎可以立即加载。一旦这是真的，我们将 loadingMore 设置为 true，并开始调用我们的 addHistory 函数，将 earliestMessageTime 作为开始，nil 作为结束，并且限制为您喜欢的任意数量，尽管最大值将在 100 中返回。

```
//This method allows users to query for more messages by dragging down from the top.
func scrollViewDidScroll(_ scrollView: UIScrollView){
    //If we are not loading more messages already
    if(!loadingMore){

        //-40 is when you have dragged down from the top of all the messages
        if(scrollView.contentOffset.y < -40 ) {
            loadingMore = true
            addHistory(start: earliestMessageTime, end: nil, limit: 10)
        }
    }

} 
```

我们现在需要在单击发送按钮时发布消息。为此，让我们创建一个函数来发送 messageTextField 中的消息。首先检查它是否为空，如果为空则进行处理，然后用您想要发送的消息信息创建一个字典，然后在您的 PubNub 对象上使用简单的 publish 函数。

这个函数接受多种类型的变量和对象作为消息和通道名发送。您还可以随后包含一个处理程序，根据状态代码做一些事情。之后，调用我们刚刚在 sendMessage 操作中创建的函数。

```
func publishMessage() {
    if(messageTextField.text != "" || messageTextField.text != nil){
        let messageString: String = messageTextField.text!
        let messageObject : [String:Any] =
            [
                "message" : messageString,
                "username" : username,
                "uuid": client.uuid()
        ]

        client.publish(messageObject, toChannel: channelName) { (status) in
            print(status.data.information)
        }
        messageTextField.text = ""
    }

}

//When the send button is clicked, the message will send
@IBAction func sendMessage(_ sender: UIButton) {
    publishMessage()
} 
```

为了让我们的应用程序充分发挥作用，我们需要能够离开频道，回到 ConnectVC。我们已经有了一个函数，我们只需要填充它。取消订阅客户端订阅的所有频道，然后执行我们最初创建的“leaveChannelSegue”。

```
client.unsubscribeFromAll()
self.performSegue(withIdentifier: "leaveChannelSegue", sender: self) 
```

## Swift 中已完成的聊天应用

让我们运行应用程序吧！

[![Swift Chat App with PubNub](img/ae34f5db5d3facff3081a24a5d3fad1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M843aAru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/04/pubnub-swift-chat.gif)

我们现在有了基本的聊天功能。用户可以实时发送和接收消息，消息会存储一段预定义的时间。

此处提供了该项目的完整 [GitHub repo。](https://github.com/SambaDialloB/PubNubChat)

使用 PubNub 每月可免费发送多达 100 万条消息。查看 [PubNub Swift SDK 文档](https://www.pubnub.com/docs/swift/pubnub-swift-sdk)，或任何其他 [75+ PubNub 客户端 SDK](https://www.pubnub.com/developers/)。