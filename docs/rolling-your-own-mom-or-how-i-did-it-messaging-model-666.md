# 滚动你自己的妈妈，或者我是如何做到的-信息模型

> 原文：<https://dev.to/cppchedy/rolling-your-own-mom-or-how-i-did-it-messaging-model-666>

# 简介

虽然 **MOM 系列**的第三部分主要是关于理解 Seastar 和放置第一个里程碑，但第三部分致力于系统的消息模型:我们将展示其元素，并将在*核心模块*中实现它们。

本文既有理论又有代码，所以我希望您喜欢它，并把它作为您的模型的灵感。我们开始吧。

# 消息传递模式

有些人可能想知道为什么我们需要一个消息传递模型？在讨论模型之前，我更愿意先解决这个问题。

为了正确回答这个问题，我们需要考虑我们系统的背景。如果您还记得，我说过我们正在构建一个软件，作为企业应用程序的消息中继器/代理，根据手头的情况，企业开发人员需要使用我们的中间件来设计连接所有软件。然而，我认为一个系统的学习曲线越简单，它就越容易被采用。举个例子，看看 c++，很多人抱怨它太大了，很难学。这是一个缺点，也是它被采用的一个障碍，尽管目前它的用户数量没有反映出这一点。我认为，给出一个小而清晰的心智模型，捕捉设计过程中所需的所有细节，以及我们系统内部的其他细节，有助于解决学习问题。此外，拥有我们系统的模型使得如何解决消息交换的问题变得容易，也是表达解决方案的架构意图的关键工具。此外，展示部署在企业系统中的解决方案(使用模型的元素)的图表作为(新)团队成员的文档。

## 消息传递模型的要素

我更喜欢采用分析的方法，而不是用定义来列举元素。我认为这样更有益，所以让我们确定用户需求，并从中提取模型的概念。

我们的消息传递模型由元素组成，这些元素可以组合起来形成其组件(应用程序)之间的消息路由架构。

很明显，我们的模型需要包含一个概念/实体来表示架构中的企业应用程序。因此，我们引入了**客户端**，这是我们模型的第一个元素。

客户端位于中间件之外，它主要是一个与系统交互的实体。服务器有一个固定的已知地址，因此任何应用程序都可以访问它。然而，为了将消息路由到其目的地，服务器需要其客户端的地址，因此，客户端需要*在系统中注册*。通过这种方式，服务器可以获得完成任务所需的信息。进入**上下文**，它是我们模型的第二个元素，保存客户信息和其他相关数据。与**客户端**相比，**上下文**位于中间件中，对于与远程应用程序的通信至关重要。

我们系统的用户可能需要从一个应用程序向多个应用程序发送相同的消息，或者一些应用程序可能在语义上相关，因此将它们联系在一起是很自然的。为了支持这样的行为，我们将**组**实体添加到我们的模型中。该元素可用于根据用户意愿对**上下文**进行聚类。

**Group** 是我们消息传递模型的核心概念。它不仅将**上下文**相互联系起来，还定义了消息传递(谁确切地得到了消息)。我们将在本文的后面更多地讨论交付过程。

我们一直使用**上下文**来表示团队，但是让我们使用正确的术语:当我们谈论团队时，我更喜欢称与其相关的上下文为**成员**。

会员可以扮演消息的**消费者**或**生产者**的角色。每个**组**有一个生产者和多个**消费者**。这个设计决策是为了简化我计划用 *end2end* ack(确认)特性做的工作(但是没有实现它)。这纯粹是为了尊重最后期限，所以随时改变这一点，重新设计系统。

当应用程序请求加入(或订阅)操作时，上下文成为组的成员(具有生产者 Xor 消费者成员资格)。这就是我们**将两者**联系起来的时候。

### 消息分发策略

正如我们前面提到的，组定义了消息传递的过程。我计划支持 3 种消息分发策略:

*   单一传递:在这种模式下，消息只发送给一个消费者。

*   每匹配:在这个策略中，我们只在满足某个“条件”时才发送消息。对于与消费者的每个关联，我们检查它是否符合标准，如果符合，我们发送消息。

*   发布者/订阅者:您可能已经从名称中猜到了，在这里，消息被传递给组中的每个消费者。每个人都关心来自该组制作人的消息。

因此，我们有三种类型的团体，每种类型在上面解释的一种政治中运作。

既然我们已经看到了策略(并因此定义了我们的系统可以拥有的组的类型)，我们可以继续讨论每匹配模式中使用的相关概念:**注释**。

回想一下，**每场比赛**组不会向消费者传递消息，除非他们符合标准。在我们的模型中，我们通过放置在每个**关联**上的**注释**来表达这一点。如果消息中的文本与**注释**匹配，系统将消息发送给消费者。

让我们用一个系统架构的例子来结束这一部分，在这个例子中，我们使用模型的元素来描述消息交换所需的设置。

[![](img/7bdefffa98d8eeca57bd7826673fe1b4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Twpga1fJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a2xkq6248rmcoyfawguy.png)

此图显示了一个场景，其中我们根据日志的严重级别(信息、错误等)通过网络将*日志消息*发送到不同的目的地...).如图所示，我们有一个日志生产者和消费者，他们都是“MyGr”组的成员。从图中，您可以看到每个关联都被**标注了严重性级别**，通过它，系统可以将消息发送到适当的目的地。

### TL；速度三角形定位法(dead reckoning)

*   **Group** :将语义相关的应用程序聚集在一起的实体。

*   **客户端**:代表企业应用的实体。

*   **上下文**:中间件内部代表应用的实体。

*   **关联**:这个实体代表加入一个群体的行为。

*   **Annotation** :表示条件的实体，附属于关联。

我希望我成功地解释了系统的消息传递模型。如果你发现有不清楚的地方，请在评论中提出问题。

# 实现

既然我们已经讨论了消息传递模型并了解了它的元素，我们可以开始讨论如何实现它了。

在本节中，我们将探讨两个模块:*身份模块*和*核心模块*。后者取决于第一个。我们将看到如何。

## 身份模块

身份的概念是如此的基本，以至于你几乎在任何地方都能看到它，我们的系统也不例外。具体来说，我们在模型中使用了它，尽管我们没有明确提到。正如我们将在本文的下一节中看到的，模型中的两个元素依赖于**身份模块**的身份类。

现在让我们专注于编写模块。我们从文件`src/identity/identity.hh`中的`Identity class`开始:

```
#pragma once 
#include <string> 
// we will need these for upcomming functions
#include <boost/functional/hash.hpp>
#include <boost/uuid/string_generator.hpp>
#include <boost/uuid/uuid.hpp>
#include <boost/uuid/uuid_io.hpp> 
template <typename T>
class Identity {
  T id_;

public:
  using value_type = T;

  Identity() = default;
  Identity(const Identity &) = default;
  Identity(Identity &&) = default;
  ~Identity() = default;

  Identity &operator=(const Identity &) = default;
  Identity &operator=(Identity &&) = default;

  Identity(T id) : id_{id} {
  }

  friend bool operator==(const Identity &lhs, const Identity &rhs) {
    return lhs.id_ == rhs.id_;
  }

  friend bool operator!=(const Identity &lhs, const Identity &rhs) {
    return !(lhs == rhs);
  }

  std::string toString() const {
    return idToString(id_);
  }

  const value_type &value() const {
    return id_;
  }
}; 
```

正如你所看到的，这是一个简单的值包装器，我认为它不需要解释，但我们必须编写`idToString`，所以让我们在类`Identity`之前添加头，并在`src/identity/identity.cpp` :
中实现它

```
// src/identity/identity.hh
#pragma once //...

std::string idToString(boost::uuids::uuid uuid_);

std::string idToString(std::string str);

template <typename T>
class Identity {
//...
};

// src/identity/identity.cpp 
```

```
#include "identity.hh" 
std::string idToString(boost::uuids::uuid uuid_) {
  return boost::uuids::to_string(uuid_);
}

std::string idToString(std::string str) {
  return str;
} 
```

这里我们使用了`boost::uuids`和将`uuid`转换成`std:: string`的函数。Seastar 依赖于 boost，所以我们从那里使用它，你不需要给柯南添加任何东西。

剧透一下，我们将在核心模块中使用 uuids 作为客户端的`Identity`类的模板参数，使用 std::string 作为组的模板参数。我们将更详细地研究它。

接下来，我们添加一个实用函数来从字符中生成身份:

```
//src/identity/identity.hpp
Identity<boost::uuids::uuid> makeIdentity(const char *id,
                                          boost::uuids::string_generator &gen) {
  return gen(std::string{id});
} 
```

不要忘记将这个方法的头添加到 identity.hh 中。

我们需要给这个模块的用户生成 uuids，所以我们实现了两个函数，一个生成一个`Identity<bst::uuid>`，另一个一次生成多个。我们会把这些放到其他文件中，`src/identity/generate_identity.hh`和`src/identity/generate_identity.cpp` :

```
//src/identity/generate_identity.hh
#pragma once 
#include <boost/uuid/uuid.hpp>            // uuid class
#include <boost/uuid/uuid_generators.hpp> // generators
#include <boost/uuid/uuid_io.hpp> 
#include "identity.hh" 
Identity<boost::uuids::uuid> generateIdentity();

std::vector<Identity<boost::uuids::uuid>> generateRange(int nb);

//src/identity/generate_identity.cpp
#include "identity_generator.hh" 
Identity<boost::uuids::uuid> generateIdentity() {
  static boost::uuids::random_generator generator{};
  boost::uuids::uuid uuid_ = generator();
  return Identity<boost::uuids::uuid>{uuid_};
}

std::vector<Identity<boost::uuids::uuid>> generateRange(int nb) {
  std::vector<Identity<boost::uuids::uuid>> uuids;
  uuids.reserve(nb);

  while (--nb) {
    uuids.push_back(generateIdentity());
  }

  return uuids;
} 
```

清除了这些，我们(几乎)完成了身份模块。我们现在将进入核心模块，在这里我们将实现我们的消息传递模型。

## 核心模块

在看到代码之前，我想分享一下我是如何通过问题驱动的方法设计这个模块的思路(至少在开始的时候)。

我们从这个一般性的问题开始:模块的意图是什么？它会做什么？。简单来说，核心模块**管理模型的** **实体**。

我们所说的**管理**到底是什么意思，我们所指的实体是什么？。所以这里的管理是对客户端和组(实体)执行以下操作:添加、加入、离开、注册、取消注册、删除...

我们已经看到了模块的用例，现在让我们想想“API”，我们到底要公开什么，我们要如何设计？

首先，我们已经在属于这个模块的`server_tcp`中使用了连接类，所以我们知道它将是公共的。

另外，我建议为我们的实体引入一个类似容器的类，它公开了上面讨论的操作。这个类的作用是控制我们实体的生命周期和关系。`Topology`是这个类的名字。

总结一下，核心模块公开了两个类:连接和拓扑。我们来看看拓扑声明:

```
class Topology {
  std::unordered_map<Identity<boost::uuids::uuid>, Client> clients;
  std::unordered_map<Identity<std::string>, Group> groups;

public:
  Topology() = default;
  Topology(const Topology &) = default;
  Topology(Topology &) = default;
  Topology &operator=(Topology &) = default;
  ~Topology() = default;

  bool addGroup(Identity<std::string> id, const std::string &grpType);
  bool removeGroup(Identity<std::string> id);

  bool addClient(Identity<boost::uuids::uuid> id,
                 socket_address sock,
                 uint16_t portRemoteConn);

  std::string updateClient(Identity<boost::uuids::uuid> id,
                           socket_address sock,
                           uint16_t port);

  std::string registerClientAs(Identity<std::string> idGrp,
                               Identity<boost::uuids::uuid> idClt,
                               const std::string &membershipType,
                               const std::string &ann);
  std::string unregisterClientFrom(Identity<std::string> idGrp,
                                   Identity<boost::uuids::uuid> idClt,
                                   const std::string &membershipType);

  std::string pushMessageTo(Identity<std::string> idGrp,
                            Identity<boost::uuids::uuid> idClt,
                            const std::string &msg);

  size_t groupsSize() const;
  size_t clientsSize() const;

  bool clientExists(Identity<boost::uuids::uuid> id) const;
  bool groupExists(Identity<std::string> id) const;
}; 
```

我更喜欢把这个类的方法的定义留到下一部分，因为其中一些涉及 MOZA 协议的细节。我认为最好在下面的文章中展示实现。

现在让我们把重点放在实现我们将通过`Topology`类控制的消息传递模型元素上。`Client`班级是我们要讨论的第一个班级。该类保存消息交换所需的外部应用程序的状态。更准确地说，这是我们消息传递模型的**上下文**元素。我选择的名字可能看起来令人困惑，因为我们有一个**客户端**的概念，那么为什么我们要将**上下文**映射到`Client`类呢？我添加**客户端**概念只是为了方便，所以我们在实现中不需要它。以下是`Client` :
的代码

```
//src/lib/core/client.hh
#include <algorithm>
#include <iostream>
#include <map>
#include <string> 
#include <boost/uuid/uuid.hpp> 
#include <seastar/core/future-util.hh>
#include <seastar/core/sleep.hh>
#include <seastar/core/queue.hh>
#include <seastar/core/reactor.hh>
#include <seastar/core/temporary_buffer.hh>
#include <seastar/net/api.hh>
#include <seastar/net/inet_address.hh> 
#include "connection.hh"
#include "lib/identity/identity.hh" 
using namespace seastar;
using namespace net;

class Client {
  Identity<boost::uuids::uuid> m_id;
  socket_address m_remoteAddress;
  uint16_t m_remotePort;
  std::map<std::string, seastar::queue<std::string>> m_channels;
  bool m_connected;

  //...

public:
  Client() = default;
  Client(const Client &) = delete;
  Client(Client &&) = default;
  ~Client() = default;

  Client &operator=(const Client &) = delete;
  Client &operator=(Client &&) = default;

  Client(Identity<boost::uuids::uuid> id, socket_address addr, uint16_t rPort)
    : m_id{id}, m_remoteAddress{addr}, m_remotePort{rPort}, m_connected{false} {
  }

  uint16_t getClientPort() const {
    return m_remotePort;
  }

  void setClientPort(uint16_t prt) {
    m_remotePort = prt;
  }

  socket_address getClientAddress() const {
    return m_remoteAddress;
  }

  void setClientAddress(socket_address addr) {
    m_remoteAddress = addr;
  }

  const Identity<boost::uuids::uuid> &identity() const {
    return m_id;
  }

  std::string addChannel(const std::string &groupNme) {
    this->m_channels.try_emplace(groupNme, 128);
    return groupNme;
  }

  bool push(const std::string &grpNme, std::string msg) {
    auto &qu = this->m_channels.at(grpNme);
    return qu.push(std::move(msg));
  }

  void startDelivery() {
    //...
  }
}; 
```

如您所见，每个`Client`实例都由一个 uuid 唯一标识，该 uuid 作为其属性之一存储。此外，我们存储它的远程地址和端口。传递消息时需要这样做。此外，当客户端作为消费者加入一个组时，它会添加一个专用通道(这里是一个`seastar::queue`)并使用组的名称作为`std::map`中的一个键。

请注意，我省略了部分代码(开始交付，等等..)因为它实现了下一篇文章中讨论的协议的一个方面。

在我们展示了`Client`类之后，让我们从我们的模型传递到组概念的实现。

小组是我们模型的中心元素。它是由许多其他元素构成的。我遵循自上而下的方法来实现这一概念。这意味着，我们将从小组的顶层概述开始，然后深入到组成小组的其他概念。

先说群体的属性。首先，一个组有一个唯一的名称来区别于其他组。其次，一个组有成员，每个成员都有一个特定的角色:它有一个成员*产生*消息，可以有许多*消费者*成员。这是代表该组的类的状态。除此之外，组的主要职责是将收到的消息传递给消费者。然而，正如我们上面讨论的，我们有 3 个发送消息的策略:单次传递、每次匹配和发布者/订阅者。

在前面的段落中，我们已经收集了一种组概念的规范，在此基础上我们可以开始做一些设计决策并开始编码。我们需要问的一个问题是，我们将如何实现交付过程的不同策略？为此，我建议使用多态性:拥有一个实现公共事物的基类，并将每个策略的差异委托给其他派生类。此外，我更喜欢通过工厂方法公开一个 facade 来隐藏基类(包装器)和控件实例化。我用`Group`作为 facade 的名字，用`GroupImpl`作为基类。对于派生类，我们有 3 个:GroupSingleDelivery、GroupPerMatch、GroupPubSub。下图显示了`Group`中的每个方法如何调用`GroupImpl`中的方法，以及`Group`如何为`GroupImpl`保存一个 unique_ptr。

[![Methods calls](img/17416ef7b25f2893e19c23a20a8b30b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Moiifshm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g508u5h86j5n1j5yk6tw.png)

我们还展示了`GroupImpl`的方法和属性以及这张图片中的派生类:
[![GroupImpl and its childreen](img/10f78e4fde742524baae32ea2e662cec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0jin85Go--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/scgt82ud9fj0zip69iu2.png)

注意`_distributeMessage(...)`是一个纯虚拟成员函数。

既然我们已经展示了组概念的设计，我们接下来展示实现，所以这里是`Group`代码:

```
//src/lib/core/group.hh
#pragma once 
#include <algorithm>
#include <iostream>
#include <map>
#include <memory>
#include <string>
#include <string_view> 
#include <boost/uuid/uuid.hpp> 
#include <seastar/core/future-util.hh>
#include <seastar/core/sleep.hh>
#include <seastar/core/queue.hh>
#include <seastar/core/reactor.hh>
#include <seastar/core/temporary_buffer.hh>
#include <seastar/net/api.hh>
#include <seastar/net/inet_address.hh> 
#include "lib/identity/identity.hh"
#include "member.hh" 
using namespace seastar;
using namespace net;
//...
class Group {
  std::unique_ptr<GroupImpl> m_impl;

public:
  Group(std::unique_ptr<GroupImpl> grp) : m_impl{std::move(grp)} {
  }

  Group() = default;
  Group(Group &&) = default;
  Group &operator=(Group &&) = default;
  ~Group() = default;

  bool isProducer(Identity<boost::uuids::uuid> id) {
    return m_impl->_isProducer(id);
  }
  bool isConsumer(Identity<boost::uuids::uuid> id) {
    return m_impl->_isConsumer(id);
  }

  bool setProducer(Member prod) {
    return m_impl->_setProducer(prod);
  }

  void unsetProducer() {
    m_impl->_unsetProducer();
  }

  bool addConsumer(Member cons) {
    return m_impl->_addConsumer(cons);
  }

  void removeConsumer(Identity<boost::uuids::uuid> id) {
    m_impl->_removeConsumer(id);
  }

  std::string distributeMessage(std::string msg) {
    return m_impl->_distributeMessage(msg);
  }

  bool producerIsSet() const {
    return m_impl->_producerIsSet();
  }

  Identity<std::string> identity() const {
    return m_impl->_identity();
  }
};
//... 
```

如您所见，所有方法都从`GroupImpl`开始镜像其对应的前缀为`_`的方法。

`GroupImpl`保存状态并实现除了`_distributeMessage(...)`之外的所有成员函数，所以我们来看看代码:

```
//src/lib/core/group.hh
#pragma //includes...

class GroupImpl {
protected:
  Identity<std::string> m_identity;
  Member m_producer;
  std::vector<Member> m_consumers;
  bool m_prodSet = false;

public:
  GroupImpl() = default;
  GroupImpl(const GroupImpl &) = delete;
  GroupImpl(GroupImpl &&) = default;
  ~GroupImpl() = default;

  GroupImpl &operator=(const GroupImpl &) = delete;
  GroupImpl &operator=(GroupImpl &&) = default;

  GroupImpl(const std::string &str)
    : m_identity{str}, m_producer{}, m_consumers{}, m_prodSet{false} {
  }

  GroupImpl(Identity<std::string> id)
    : m_identity{id}, m_producer{}, m_consumers{}, m_prodSet{false} {
  }

  bool _isProducer(Identity<boost::uuids::uuid> id);
  bool _isConsumer(Identity<boost::uuids::uuid> id);

  bool _setProducer(Member prod);
  bool _addConsumer(Member cons);

  void _unsetProducer();
  void _removeConsumer(Identity<boost::uuids::uuid> id);

  bool _producerIsSet() const;

  Identity<std::string> _identity() const;

  void notify();

  virtual std::string _distributeMessage(std::string msg) = 0;
};

class Group {
//...
};

//... 
```

请注意，`GroupImpl`在`Group`之前，所以请确保遵守顺序。这是因为我们在头文件中声明和定义了 Group，所以编译器需要知道`GroupImpl`的完整类型，因为我们在`Group`的定义中使用了它的方法。

数据成员被标记为受保护的，这样我们可以在派生类中访问它们。此外，我们在模型中引入了代表**成员**概念的`Member`结构。我们将在处理完来自`GroupImpl`的派生类后讨论它的实现。现在让我们来看看`GroupImpl`成员函数的定义:

```
//src/lib/core/group.cpp
#include "group.hh" 
bool GroupImpl::_isProducer(Identity<boost::uuids::uuid> id) {

  if (!this->_producerIsSet())
    return false;
  return m_producer.clt->identity() == id;
}
bool GroupImpl::_isConsumer(Identity<boost::uuids::uuid> id) {
  return std::any_of(
      begin(m_consumers), end(m_consumers),
      [&id](const Member &mbr) { return mbr.clt->identity() == id; });
}

bool GroupImpl::_setProducer(Member prod) {
  if (_isConsumer(prod.clt->identity()))
    return false;

  m_producer = prod;
  m_prodSet = true;
  return true;
}
bool GroupImpl::_addConsumer(Member cons) {
  if (_isProducer(cons.clt->identity()) || _isConsumer(cons.clt->identity()))
    return false;

  m_consumers.push_back(cons);
  return true;
}

void GroupImpl::_unsetProducer() {
  m_prodSet = false;
  m_producer.clt = nullptr;
}

void GroupImpl::_removeConsumer(Identity<boost::uuids::uuid> id) {
  this->m_consumers.erase(std::remove_if(begin(m_consumers), end(m_consumers),
                                         [&id](const Member &mbr) {
                                           return mbr.clt->identity() == id;
                                         }),
                          m_consumers.end());
}

bool GroupImpl::_producerIsSet() const {
  return m_prodSet;
}

Identity<std::string> GroupImpl::_identity() const {
  return m_identity;
} 
```

我认为这里给出的代码是清楚的，但如果不是，请随意留下评论，我会很乐意解释。

随着`GroupImpl`的方式，让我们传递给派生类。我们从`GroupSingleDelivery`开始，但我将删除与协议相关的部分代码:

```
//src/lib/core/group.hh
//includes
//class GroupImpl
//class group
//...
class GroupSingleDelivery : public GroupImpl {
  int nextDst = 0;

public:
  using GroupImpl::GroupImpl;
  std::string _distributeMessage(std::string msg) {
    auto consSize = m_consumers.size();
    if (consSize == 0)
      return //...;
    if (!this->m_consumers[nextDst % consSize].clt->push(m_identity.toString(),
                                                         msg))
      return //...;
    using namespace std::chrono_literals;
    seastar::sleep(1s).then([this, consSize] {this->m_consumers[nextDst % consSize].clt->startDelivery();});
    ++nextDst;
    return //...;
  }
};
//... 
```

`GroupSingleDelivery`类只将收到的消息发送给它的一个消费者，并使用`nextDst`数据成员模数(当前组中消费者的大小)跟踪下一个目的地。

`std::string _distributeMessage(std::string msg)`获取我们想要传递的消息，并将其推送到一个消费者队列中，然后触发传递流程。

接下来是`GroupPubSub`课。下面是代码:

```
//src/lib/core/group.hh
//...
//class GroupSingleDe...
//...
class GroupPubSub : public GroupImpl {

public:
  using GroupImpl::GroupImpl;
  std::string _distributeMessage(std::string msg) {

    auto consSize = m_consumers.size();
    if (consSize == 0)
      return //...;
    bool queuedForAtLeastOne = false;
    for (const auto &elm : m_consumers) {
      queuedForAtLeastOne =
           elm.clt->push(m_identity.toString(), msg) || queuedForAtLeastOne ;
      elm.clt->startDelivery();
    }
    if (!queuedForAtLeastOne)
      return //...;
    return //...;
  }
}; 
```

在这个版本中，消息被传递给所有消费者，因此基于范围的 for 循环迭代消费者向量，并将消息推入每个客户端通道，并为每个消费者启动传递过程。

我不会展示`GroupPerMatch`的代码，而是从我们正在使用的模型跳到其他概念，我们没有定义它们。`GroupPerMatch`的`_distributeMessage`用多一点`Member`所以我觉得深入一点(会员，协会，...)然后返回以完成最后一个派生类。

`Member`类是一个抽象，它指向一个`Group`的一个实际的`Client`成员，并保存推送和交付过程所需的信息:

```
//src/lib/core/member.hh
#pragma once 
#include <string> 
#include "client.hh" //...
struct Member {
  Client *clt;
  Association ass;

  Member() : clt{nullptr}, ass{""} {
  }
  ~Member() = default;
}; 
```

正如您可能已经猜到的，`Association`是保存按匹配分发的业务逻辑所需数据的类，并提供对标准的验证。它是这样实现的:

```
//src/lib/core/member.hh
class Association {
  std::string m_annotation;
  std::string m_queueName;

public:
  Association(std::string qn, std::string ann)
    : m_annotation{std::move(ann)}, m_queueName{std::move(qn)} {
  }
  Association(std::string qn) : m_annotation{}, m_queueName{std::move(qn)} {
  }

  bool hasAnnotation() const {
    return m_annotation != "";
  }

  bool verify(const std::string &ann) const {
    return m_annotation == ann;
  }

  const std::string &dstChannelName() const {
    return m_queueName;
  }
};

//struct Member... 
```

Association 保存在每匹配传递策略中使用的注释和要将消息推送到的队列的名称。至此，我们已经完成了与**组**相关的概念的实施，但我们还必须完成`GroupPerMatch`。

**免责声明**:在设计协议和开发模块的时候，我忘记了一件对`GroupPerMatch`的`_distributeMessage`非常重要的事情。这导致了一个丑陋的解决方案，或者更确切地说，一个使它工作的黑客，而不是干净和可感知的东西。因此，我在这里提出的是我的错误，而不是你会采取，而是避免。我不能做一个合适的修复，因为我几乎没有时间了，我需要它来工作。

我忘记了在协议中将注释作为参数传递，我已经实现了解析，所以我实际上用数据对它进行了编码，因此，我不得不在`_distributeMessage`方法中注入一个解析函数，而不是从协议解析器中免费获得。此外，我没有考虑`_distributeMessage`方法中额外的注释参数。在理想情况下，签名应该是`virtual std::string _distributeMessage(std::string msg, std::string ann=0) = 0;`，参数将使用协议解析器提取。

我最终做的是将注释编码在实际数据中，如下所示:`*****info-Hello`其中 Hello 是要交付的数据，info 是由`*****`和`-`分隔的注释。

```
//...
class GroupPerMatch : public GroupImpl {

public:
  using GroupImpl::GroupImpl;
  std::string _distributeMessage(std::string msg) {

    auto consSize = m_consumers.size();
    if (consSize == 0)
      return //...;

    auto extractCritere =
         -> compat::optional<std::string> {
      const std::string opMark = "******";
      auto first = begin(data);
      auto last = begin(data) + 7;
      std::string_view str{data.c_str(), 7};

      if (str == opMark)
        return {};
      std::string ann;
      ann.reserve(256);

      auto _pos = std::find(begin(data) + 7, end(data), '-');
      if (_pos == end(data))
        return {};

      std::copy(begin(data) + 7, _pos, std::back_inserter(ann));

      data.erase(std::rotate(begin(data), _pos + 1, end(data)));

      return {ann};
    };

    auto critere = extractCritere(msg);

    if (!critere.has_value())
      return //...;
    auto crt = critere.value();

    auto start = begin(m_consumers);

    bool matchedOne = false;

    do {

      auto cons =
          std::find_if(start, end(m_consumers), [&crt](const Member &meb) {
            return meb.ass.verify(crt);
          });
      if (cons == end(m_consumers)) {
        if (!matchedOne)
          return //....;
        return //...;
      }
      cons->clt->push(m_identity.toString(), msg);
      cons->clt->startDelivery();
      matchedOne = true;
      start = cons + 1;
    } while (true);
  }
};
//...class GroupPubSub 
```

所以我们从通常的检查消费者数量开始，然后我们定义一个 lambda 来解析和提取编码的注释(如果有的话)。这个 lambda 返回一个`optional`。`do {} while();`循环是为了检查验证标准的消费者，以便我们可以推动和开始交付过程。

正如我在免责声明中所说，这不是正确的做法。只是一些能让工作的东西。在以后的文章中，我将讨论这个问题。

我设计中的另一个缺陷是核心模块包含与协议模块直接相关的代码，因此在看到它们之前需要理解协议。这就是为什么我们将把`Topology`类方法的定义留给下一篇文章和协议模块。此外，我将建议一种方法来读取这个*直接*依赖。

# 结论

我们已经讨论了面向消息的中间件的消息传递模型，并且展示了大部分实现。我希望你在设计自己的模型时，把这个模型作为灵感的来源。

# 下一部分

在下一篇文章中，我们将讨论 MOZA 协议，它支持应用程序和中间件之间的通信。