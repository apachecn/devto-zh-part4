# 如何在移动应用中设计离线优先的方法

> 原文：<https://dev.to/netguru/how-to-design-offline-first-approach-in-a-mobile-app-58g7>

现在，几乎每个人都在家里、工作场所，甚至通勤时使用 WiFi。当没有无线网络可用时，我们连接到移动网络。这是否意味着我们在制作定制移动应用时不应该关心网络的可用性？不完全是。有很多情况下，短时间的无连接会破坏你的应用程序的“流畅度”。

## **在线第一方法**

在线优先的方法是自然的。我们从远程服务器下载数据，显示一个奇特的加载指示器，然后以一种吸引人的方式呈现数据。用户可以看到所有的数据，修改它，然后我们把它发送回服务器。这里，我们再次向他们展示了某种进度条。看起来很简单，对吧？的确如此。这就是我们通常构建应用程序的方式。但是让我们停一会儿，看看这种方法有什么问题。

首先，我们强迫用户在每次从远程服务器获取数据时等待。即使数据没有变化，我们也在不断下载数据，浪费我们的网络带宽。最后，在所描述的场景中，我们至少有两个地方的网络连接可能会失败。

当网络通信出现问题时，我们该怎么办？当下载失败时，我们应该显示一个错误信息和一个空视图吗？这是一个流行的解决方案，但并不意味着它是正确的。

最坏的情况是在上传内容时出现故障。想象一下，写一篇博文、一篇脸书评论或一封电子邮件:你点击发送，出现了一个错误，你无法上传。你会让你的用户丢失他们的数据吗？你会因为显示“无法上传你的内容”而中断你的应用程序的体验吗？请稍后再试"？那是不应该发生的事情。用户不应该负责重新下载或重新上传数据，我们应该负责。我们应该开始考虑采取“离线优先”的方法来构建移动应用的用户体验。

## **离线-先接近**

需要明确的是:离线优先的方法并不是解决你在不可靠的网络连接中遇到的所有问题的通用解决方案——它在很大程度上取决于你的应用程序的需求。它更像是一种设计方法，让您专注于对最终用户真正重要的东西:一个具有出色用户体验的强大应用程序。

让我们来看看在以客户为中心的产品的在线优先方法中我们可以改变什么。

### **真理的单一来源**

首先要改变我们单一的真理来源。在前面的例子中，是远程服务器——我们将直接通过网络下载和上传数据。为了切换到离线优先的方法，我们需要一个本地数据库。如今，我们在可用的实现方面有很大的选择余地，所以只要挑选最适合你的就行了(例如[领域](https://realm.io/)、[房间](https://developer.android.com/topic/libraries/architecture/room)或[沙克姆](http://sharkorm.com/))。

我们的数据库将是我们拉或推数据的唯一地方。它比网络快得多，因此它将解决每次下载或上传数据时迫使用户等待的问题。有了数据库作为唯一的事实来源，我们的应用不应该关心数据来自哪里——不管是来自数据库还是网络。但是要使它工作，我们必须处理从服务器接收的内容。

### **数据抓取**

我们的应用程序现在只依赖于数据库。我们没有加载指示器，但是我们也没有要加载的内容。我们应该设法获取数据并将其放入数据库。实现的细节将根据应用程序的要求而变化，但对于这个简单的例子，假设我们在第一次运行时将所有内容下载到应用程序，然后每天在后台重新下载一次。在我们的服务器成功响应后，我们只需将所有收到的数据推送到我们的本地数据库，应用程序将从那里提取数据。这给了我们一个很好的应用行为:用户只看到一次加载指示器——当他们第一次运行我们的应用时。为了改善用户体验，我们可以使用闪屏来隐藏下载过程。

### **数据同步**

通过获取数据，我们已经涵盖了下载内容的情况，但是上传内容呢？当用户修改某些内容或添加新内容时，我们也应该将其保存到我们的数据库中。最流行的技术是用一个标志保存我们最近编辑/添加的数据(姑且称之为‘pending’)。我们可以实施一个定期同步过程，将所有标记为“待定”的实体上传到我们的服务器。如果响应成功，我们应该将我们的数据库实体与刚刚从后端接收到的实体进行交换——这将保证数据的完整性。通过同步我们的数据，我们不会向用户显示任何错误。如果第一次尝试失败，我们的定期同步机制将尽快上传数据。一个好的做法是通过显示一些图标来告诉用户最近添加的内容还没有上传，以避免混淆，但这取决于您。

### **把碎片拼在一起**

结合单一来源的事实，数据获取和数据同步将为我们提供良好的用户体验，没有加载指标，也没有错误的空视图。我们将节省网络带宽，因为我们的数据下载一次(并定期更新)，用户可以通过我们的应用程序移动，没有任何中断。他们将能够毫无顾虑地写一篇很长的评论，我们将确保即使网络故障，他们的内容也不会丢失——我们的同步过程会负责上传。

实施细节可能会根据您的应用程序的要求而有所不同。为了帮助您处理这一过程，以下是您在应用程序中处理离线优先方法时可能会遇到的一些问题:

*   您需要多久获取一次数据？您的应用程序的关键要求是始终拥有最新的内容吗？如果答案是肯定的，那么考虑在每次需要向用户显示数据时获取内容。当请求失败时，您应该显示来自数据库的缓存结果。

*   如果您的应用程序很大，您将如何设法获取数据？您可能不需要重新下载所有数据——考虑在后端实现缓存，只下载自上次读取以来发生变化的数据部分。

*   需要马上上传用户内容吗？

*   您是否需要向用户显示他们当前处于离线模式？

*   每当数据同步正在进行时，你需要通知用户吗？

*   如何处理可以被许多用户同时编辑的数据？

*   当您的应用程序处于离线模式，但有一种机制允许用户手动刷新(例如拉刷新)时，您将如何处理获取数据的场景？

正如你可能看到的，立即回答这些问题是一项相当具有挑战性的任务。但是不要担心，慢慢来准备一个定制的解决方案。相信我，你的用户会对结果满意的！