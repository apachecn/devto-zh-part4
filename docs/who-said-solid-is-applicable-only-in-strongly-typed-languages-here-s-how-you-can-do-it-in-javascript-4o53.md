# 谁说 SOLID 只适用于强类型语言？下面是如何用 Javascript 实现的！

> 原文：<https://dev.to/ganesh_upshotly/who-said-solid-is-applicable-only-in-strongly-typed-languages-here-s-how-you-can-do-it-in-javascript-4o53>

SOLID 是五个设计原则，旨在使软件设计更容易理解、灵活和可维护。它帮助我们思考构建软件系统的正确方法

## Javascript 中的坚实原则

SOLID 只是一组设计原则，所有编码人员和开发人员都应该遵循这些原则，以在他们构建的软件中实现良好的设计。不是别人，正是罗伯特·C·马丁提出的。坚实的原则解释了如何安排我们的功能和数据结构，以及它们如何相互连接。

坚实原则的主要目标是任何软件都应该容忍变化，并且应该易于理解。如果我们希望构建高质量的软件，遵循坚实的原则是必不可少的。

[![SOLID](img/2077c4f8e5f81e0c891c8936149806ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WaZjDe-E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1400/1%2A5iTuZMJT4QFt8gaq11XCig.png)

大多数人认为 SOLID 只适用于强类型语言或面向对象语言，如 JAVA 等。尽管 Javascript 是一种松散类型的语言，但我们可以很容易地将坚实的原则应用于 Javascript 代码。让我们看看如何在本文中做到这一点。

## 固体代表什么？

### S —单一责任原则

组织中的任何软件模块都应该有且只有一个改变的理由。这意味着任何功能都应该只负责做一件事。例如，在 react 中，我们可以将它与无状态功能组件相关联。我们来看一个例子。

```
 function createUser(name, email){
   let re = /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i;
   let isEmailValid = re.test(email);
   if(isEmailValid){
    createUserInDabase(name, email)
   }
 } 
```

在上面的例子中，如果电子邮件的逻辑改变了怎么办。函数 createUser 应该只有一个改变的理由。在我们的例子中，只有当创建用户的逻辑改变时，函数才会改变。那么，让我们看看如何将它改为 SRP

```
function validateRequest(req){
  let re = /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i;
  let isEmailValid = re.test(req.email);
  if(isEmailValid){
     createUser(req.name, req.email)
  }
}

function createUser(name, email){
  createUserInDabase(name, email)
} 
```

### O —开闭原理

任何软件系统都应该对扩展开放，对变化封闭。这意味着应该允许软件系统通过添加新代码而不是改变现有代码来改变它们的行为。

让我们看一个开闭原理的例子

```
let allowedRoles = [“admin”, “owner”]
function checkPrivilege(user){
  if(allowedRoles.includes(user.role)){
   return true; //user has privilege
  }else{
   return false
  }
} 
```

现在，如果软件系统引入了一个名为 guestAdmin 的新角色，并且拥有 guestAdmin 角色的用户也应该被授予特权，那会怎么样呢？因此，这里我们必须修改现有代码，将 guestAdmin 添加到列表中。所以我们可以像下面的例子一样，让它通过开闭原则。

```
let allowedRoles = [“admin”, “owner”]
function checkPrivilege(user){
  if(allowedRoles.includes(user.role)){
    return true; //user has privilege
  }else{
    return false
  }
}
addRoles(role){
  allowedRoles.push(role)
} 
```

所以在这里我们不必修改现有的代码，而是可以扩展它来添加一个新的角色

### L —利斯科夫替代原理

按照 LSP，使用基类引用的函数必须能够在不知道的情况下使用派生类的对象。简而言之，派生类必须可以替换基类。让我们用一个例子来看看利斯科夫替代原理:

```
var License = function(user){
  this.calculateFee = function (){
  //Logic to calculate Fee
  }
}
License.prototype.PersonalLicense = function(user){
  this.calculateFee(user)
}
License.prototype.BusinessLicense = function(user){
  this.calculateFee(user)
  this.getUsers = function(){
  //Logic to get all users in Business
  }
} 
```

上面的例子在 LSP 中是完美的，因为即使我们替换扩展函数 PersonalLicense 而不是 License 函数。

因此，LSP 的结论是:

*如果对于每一个 S 类型的对象 o1，都有一个 T 类型的对象 o2，使得对于根据 T 定义的所有程序 P，当 o1 代替 o2 时，P 的行为不变，则 S 是 T 的子类型。*

### I —界面偏析原理

接口分离原则建议避免依赖模块或函数或任何他们不使用的东西。

但是在 Javascript 中我们没有默认的接口。但是我们都可能会遇到这样的情况，我们想在一个类的构造函数上做很多事情。先说一些我们要在构造函数里做的设置。我们所做的设置应该与构造函数中其他不需要的设置分开。例如:

```
class User{
  constructor(user){
    this.user = user;
    this.initiateUser();
  }
  initiateUser(){
    this.name = this.user.name
    this.validateUser()
   }
}
const user = new User({userProperties, validateUser(){}}); 
```

这里，validateUser()函数将在 initiateUser 构造函数调用中被调用，即使它并不总是需要的。界面分离原则来了。我们可以用下面的代码将它带入 ISP:

```
 class User{
   constructor(user){
     this.user = user;
     this.initiateUser();
     this.setupOptions = user.options
   }
   initiateUser(){
     this.name = this.user.name
     this.setupOptions()
   }
 }
 const user = new User({userProperties, options: {validateUser()}{}}); 
```

这里，我们传入选项，只有当我们传入选项时，它才会验证用户。因此，我们将不需要的逻辑从承包商中分离出来。

### D —依存倒置原则

依赖倒置原则声明高级功能不应该依赖于实现低级功能的代码。

[![DIP](img/5e12304beebde132b6c72b391790e664.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nb9t99jl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/1280/1%2Am-CMwIOkrY6-Y1lkE3kLfw.jpeg)

这个原则告诉我们，最灵活的系统是那些源代码依赖仅指抽象而非具体的系统。在像 Java 这样的静态类型语言中，这意味着 use、import 和 include 语句应该只引用包含接口和抽象类的源模块。对于 Javascript，我们可以看到下面的例子:

```
axios.get(“/someAddress/someResource”, function (response) {
 this.setState({
 value1: response.value1,
 value2: response.value2
 });
}); 
```

以上代码不在 DIP 中。让我们在下面的例子中使用 DIP:

```
requestWithAxios(“/someAddress/someResource”, setResponseInState);

function requestWithAxios(address, setResponseInState){
 axios.get(“/someAddress/someResource”, function (response) {
 setResponseInState.setValues(response);
 });
}

var setResponseInState ={
 setValues: function(response){
  this.setState({
  value1: response.value1,
  value2: response.value2
  })
 }
} 
```

我希望这能让你对如何在 Javascript 中应用 SOLID 原则有一个基本的了解。应用 SOLID 原则可以使代码更清晰、可扩展、更容易理解。

感谢阅读这篇文章。

我在[主管技术，在](https://upshotly.com)主管技术。我们很高兴能为现代领导者打造工具，帮助他们将员工置于商业成功的核心。如果你认为你受益于这个博客，请与你的朋友和同事分享！如果你对我的下一篇博客有任何疑问、澄清或想法，请在评论中告诉我！