# 解决 iOS 上的强制网络门户问题

> 原文：<https://dev.to/rwbutler/solving-the-captive-portal-problem-on-ios-fbe>

# 通过在没有互联网接入的情况下检测 Wi-Fi 网络，使互联网连接检测更加可靠。

在 iOS 开发中，检测互联网连接的实际方法是利用苹果的[可达性样本代码](https://developer.apple.com/library/archive/samplecode/Reachability/Introduction/Intro.html)。然而，可达性实际上无法检测连通性是否存在，*仅检测到可能允许连接的接口可用*。

考虑一个应用程序用户使用公共 Wi-Fi 热点的情况，这需要用户在建立互联网连接之前通过[强制门户](https://en.wikipedia.org/wiki/Captive_portal)注册或同意服务条款，例如在您当地的星巴克分店。该设备将显示为已连接到 Wi-Fi 网络，但任何数据请求都将失败，直到用户同意 Wi-Fi 热点的服务条款或注册为新用户，具体取决于热点的要求。在这些情况下，即使真正的互联网连接实际上不可用，可达性也将返回指示 Wi-Fi 可用的响应。

这可能会导致混乱，因为连接到这样的热点时，你的应用程序会表现得好像在线一样，因为可达性检查将指示 Wi-Fi 连接的存在。同时，你的应用程序从互联网上检索数据的尝试将会失败。这反过来可能会转化为应用商店的差评。

# iOS 如何解决强制网络门户问题

那么，我们如何确保我们的应用程序具有真正的互联网连接呢？事实证明，iOS 已经有了解决这个问题的方案。

iOS 采用了由[无线宽带联盟](https://wballiance.com)发布的名为无线互联网服务提供商漫游( [WISPr 2.0](https://wballiance.com/glossary/) )的协议。该协议定义了智能客户端到访问网关的接口，描述了如何使用[通用访问方法](https://en.wikipedia.org/wiki/Universal_access_method)对访问公共 IEEE 802.11 (Wi-Fi)网络的用户进行身份验证，在该方法中，强制网络门户向用户显示登录页面。

然后，用户必须通过网络浏览器注册或提供登录凭证，以便被授权使用 [RADIUS](https://www.cisco.com/c/en/us/support/docs/security-vpn/remote-authentication-dial-user-service-radius/12433-32.html) 或另一种提供集中认证、授权和记账的协议( [AAA](https://en.wikipedia.org/wiki/AAA_(computer_security)) )访问网络。

为了检测它已经连接到带有强制网络门户的 Wi-Fi 网络，iOS 联系了苹果托管的多个端点——一个例子是[https://www.apple.com/library/test/success.html](https://www.apple.com/library/test/success.html)。每个端点都有一个小的 HTML 页面，格式如下:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2//EN">
<HTML>
<HEAD>
    Success
</HEAD>
<BODY>
    Success
</BODY>
</HTML> 
```

如果在下载这个小的 HTML 页面时，iOS 发现它包含如上的单词`Success`,那么它知道互联网连接是可用的。但是，如果登录页面是由强制网络门户显示的，那么单词`Success`将不会显示，iOS 将意识到网络连接已被强制网络门户劫持，并将显示一个允许用户登录或注册的浏览器窗口。

Apple 拥有许多这样的页面，因此如果其中一个页面关闭，可以检查许多回退，以确定连接是否存在，或者我们的连接是否因强制网络门户的存在而被阻止。不幸的是，iOS 没有向开发者公开允许我们利用操作系统的强制门户意识的框架。

Connectivity 是一个在 MIT 许可下可用的开源框架，它包装了可达性，并努力复制 iOS 检测强制网络门户的方法。当可达性检测到 Wi-Fi 或 WWAN 连接时，连接性会联系多个端点，以确定是否存在真正的互联网连接，或者强制网络门户是否正在拦截连接。这种方法也可用于确定 iOS 设备是否连接到没有互联网接入的 Wi-Fi 路由器。

连通性提供了一个尽可能接近可达性的接口，以便习惯于使用可达性的开发人员熟悉它。这包括提供方法`startNotifier()`和`stopNotifier()`来开始检查互联网连接的变化。一旦通知程序启动，您可以使用状态属性同步查询当前的连接状态(类似于可访问性的`currentReachabilityStatus`)，或者通过注册为观察者异步查询当前的连接状态，默认为`kNetworkConnectivityChangedNotification`(在 Swift 中，通过`Notification.Name.ConnectivityDidChange`访问)，类似于可访问性的通知`kNetworkReachabilityChangedNotification`。

默认情况下，Connectivity 会联系 iOS 已经使用的许多端点，但它建议通过添加到`connectivityURLs`属性，由开发人员托管的端点来补充这些端点。通过设置`successThreshold`属性，可以进行进一步的定制，该属性确定了必须导致成功检查才能断定连通性存在的端点的百分比。默认值指定 75%的联系 URL 必须导致成功的连接性检查。

# 用法

要开始使用连接性，只需实例化一个实例，并分配一个闭包，当连接性检测到您连接到互联网时、断开连接时或两种情况下都要调用这个闭包，如下所示:

```
let connectivity: Connectivity = Connectivity()
let connectivityChanged: (Connectivity) -> Void = { [weak self] connectivity in
     self?.updateConnectionStatus(connectivity.status)
}
connectivity.whenConnected = connectivityChanged
connectivity.whenDisconnected = connectivityChanged
func updateConnectionStatus(_ status: Connectivity.ConnectivityStatus) {
    switch status {
        case .connectedViaWiFi:
        case .connectedViaWiFiWithoutInternet:
        case .connectedViaWWAN:
        case .connectedViaWWANWithoutInternet:
        case .notConnected:
    }

} 
```

然后，要开始监听连接变化，请调用:

```
connectivity.startNotifier() 
```

完成后记得打电话给`connectivity.stopNotifier()`。

# 一次性支票

有时您只想一次性检查连接状态。为此，实例化一个连接对象，然后检查 status 属性，如下所示:

```
let connectivity = Connectivity()
switch connectivity.status {
    case .connectedViaWiFi:

    case .connectedViaWiFiWithoutInternet:

    case .connectedViaWWAN:

    case .connectedViaWWANWithoutInternet:

    case .notConnected:

} 
```

或者，如果您只对某些类型的连接感兴趣，您可以直接检查`Connectivity`对象的以下属性:

```
var isConnectedViaWWAN: Bool
var isConnectedViaWiFi: Bool

var isConnectedViaWWANWithoutInternet: Bool
var isConnectedViaWiFiWithoutInternet: Bool 
```

# 连接网址

在使用`startNotifier()`开始连通性检查之前，可以通过连通性对象的`connectivityURLs`属性设置将被联系以检查连通性的 URL。

```
connectivity.connectivityURLs = [URL(string: “https://www.apple.com/library/test/success.html")!] 
```

# 通知

如果您喜欢使用通知来观察连接性的变化，您可以在默认的 NotificationCenter 上添加一个观察器:

[T2`NotificationCenter.default.addObserver(_:selector:name:object:)`](https://developer.apple.com/documentation/foundation/notificationcenter/1415360-addobserver)

监听`Notification.Name.ConnectivityDidChange`，接收到的通知的对象属性将包含`Connectivity`对象，您可以使用它来查询连接状态。

# 轮询

在某些情况下，您可能需要不断了解连接状态的变化，因此可能希望启用轮询。如果启用，连接将不会等待可达性状态的变化，而是每 10 秒轮询一次连接 URL(该值是可配置的)。只有在连接状态发生变化时，才会发出通知，并调用`whenConnected` / `whenDisconnected`关闭。

启用轮询:

```
connectivity.isPollingEnabled = true
connectivity.startNotifier() 
```

# SSL

从 Connectivity 1.1.0 开始，将 HTTPS 用于连接 URL 是默认设置。如果您的应用不使用[应用传输安全](https://developer.apple.com/security/)，并且您希望使用 HTTP URLs 以及 HTTP URLs，那么在实例化连接对象时，将`isHTTPSOnly`设置为`false`或者将`shouldUseHTTPS`设置为`false`，如下所示*:

```
let connectivity = Connectivity(shouldUseHTTPS: false) 
```

*请注意，如果您没有先在应用程序的 Info.plist 中设置`NSAllowsArbitraryLoads`标志，则不会设置该属性。

# 阈值

要设置被视为成功连接所需的成功连接数，请设置`successThreshold`属性。该值被指定为百分比，表示成功连接的百分比，即如果在`connectivityURLs`属性中设置了四个连接性 URL，并且指定了 75%的阈值，则四次检查中必须有三次成功，我们的应用程序才会被视为连接:

```
connectivity.successThreshold = Connectivity.Percentage(75.0) 
```

# 总结

连接模拟 iOS 检测强制网络门户的方法，并通过熟悉的界面向开发人员公开此功能。能够可靠地检测更细微的情况，例如在没有互联网接入的情况下，iOS 设备连接到 Wi-Fi 路由器，这使得应用程序开发人员能够为用户提供更好的信息，并开发更强大的在线功能。

* * *

在麻省理工学院的许可下，可以在 [GitHub](https://github.com/rwbutler/Connectivity) 上找到开源的连接，并且兼容 [Cocoapods](https://cocoapods.org/pods/Connectivity) 和 Carthage。

* * *

[![Connectivity Logo](img/71073bc537299731923e0212f44a1ae1.png "Connectivity Logo")](https://res.cloudinary.com/practicaldev/image/fetch/s--xPEnjWxE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/rwbutler/Connectivity/raw/master/doimg/connectivity-logo.png)

*如果你觉得这篇文章很有趣，后续文章[在 iOS 12+](https://rwbutler.github.io/2018-12-26-detecting-internet-access-on-ios-12) 上检测互联网接入解释了 iOS 12 中引入的网络框架如何用于替代可达性。*