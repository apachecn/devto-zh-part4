# 用 Ramda 实现 Javascript 中的功能透镜

> 原文：<https://dev.to/devinholloway/functional-lenses-in-javascript-with-ramda-4li7>

[镜头](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/basic-lensing)提供了一种将对象的形状与对象上的逻辑操作分离的方法。它使用 getter/setter 模式来“聚焦”对象的子部分，然后在不改变对象的情况下隔离该子部分进行读写，从而实现这一点。

这可以带来多重好处。先说镜片的形状解耦性质。

分离一个对象的形状允许将来对数据进行整形，同时最小化对应用程序中其余代码的影响。举个例子，一个物体代表一个人。

```
const person = {
  firstName: 'John',
  lastName: 'Doe'
} 
```

现在想象一下对象的形状发生了变化，其中的`firstName`和`lastName`属性被替换为一个名为`name`的属性，该属性本身是一个包含属性`first`和`last` :
的对象

```
const person = {
  name: {
    first: 'John',
    last: 'Doe'
  }
} 
```

任何处理该对象的代码现在都需要更新，以反映对象形状的变化。在 OOP 中，通过使用隐藏数据内部结构的类来防止这种情况，并通过 getter/setter API 提供访问。如果一个类的内部数据的形状改变了，那么需要更新的只是这个类的 API。透镜对普通的旧物体也有同样的好处。

透镜的另一个好处是能够在不改变物体的情况下写入物体。数据的不变性当然是 FP(函数式编程)的主要特点之一。问题是，你处理的数据越大、越复杂，就越难在没有突变的情况下改变深层嵌套的数据。正如我们将在后面看到的，无论数据有多复杂，lens 只需要几行代码就可以简化这个过程。

最后，镜头是[可弯曲的](https://en.wikipedia.org/wiki/Currying)和[可组合的](https://en.wikipedia.org/wiki/Function_composition)，这使得它们非常适合 FP 范式。我们将在后面的例子中使用这两种方法。

在[拉姆达](https://ramdajs.com/)的帮助下，让我们创建一个与人的`firstName`一起工作的镜头。

```
const person = {
  firstName: 'John',
  lastName: 'Doe'
} 
```

我们将从 Ramda 最通用的镜头创建函数开始，简单地称为 [lens()](https://ramdajs.com/docs/#lens) 。如前所述，镜头使用 getter/setter 模式向我们的对象读写数据。让我们先创建它们。

```
const getFirstName = data => data.firstName // getter
const setFirstName = (value, data) => ({    // setter
  ...data, firstName: value
}) 
```

然后镜头本身:

```
const firstNameLens = lens(getFirstName, setFirstName) 
```

`lens()`函数有两个参数，我们之前定义的 getter 和 setter。然后，透镜准备好应用于物体，在这个例子中，是人物体。但在此之前，我想指出几件事。

*   镜头本身没有给出任何数据的引用。这使得镜头可重用，并能够应用于任何数据，只要数据符合其 getter 和 setter 参数所需的形状。换句话说，这个镜头只有在应用于具有`firstName`属性的数据时才有用，这些属性可以是一个人、一个雇员，甚至是一只宠物。
*   因为镜头没有绑定到任何特定的数据，所以 getter 和 setter 函数需要被赋予它们将要操作的数据。镜头将获取它所应用的对象，并自动将其传递给提供的 getters 和 setters。
*   因为 FP 不允许数据的突变，所以 setter 必须返回应用了镜头的数据的更新副本。在这个例子中，我们的镜头将被应用到一个人对象，所以镜头的 setter 函数将返回一个人对象的副本。

让我们来看看我们如何使用 Ramda 的 [view()](https://ramdajs.com/docs/#view) 函数:
来使用镜头读取对象

```
view(firstNameLens, person) // => "John" 
```

`view()`函数有两个参数；一个透镜和一个应用该透镜的物体。然后，它执行镜头的 getter 函数，返回镜头聚焦的属性值；这种情况下，`firstName`。

同样值得注意的是,`view()`是可处理的，因为我们可以仅用镜头配置`view()`,并在以后提供对象。如果您想使用 Ramda 的 [compose()](https://ramdajs.com/docs/#compose) 、 [pipe()](https://ramdajs.com/docs/#pipe) 或其他各种复合函数将`view()`与其他函数复合，这将变得非常方便。

```
const sayHello = name => `Hello ${name}`

const greetPerson = pipe(
  view(firstNameLens),
  sayHello
);

greetPerson(person) // => "Hello John" 
```

现在让我们看看如何使用 Ramda 的 [set()](https://ramdajs.com/docs/#pipe) 函数:
用我们的镜头写一个对象

```
set(firstNameLens, 'Jane', person) 
// => {"firstName": "Jane", "lastName": "Doe"} 
```

`set()`函数还接受一个镜头和一个应用该镜头的对象，以及一个更新聚焦属性的值。如前所述，我们得到了一个焦点属性已更改的对象的副本。而且，就像`view()` , `set()`是可流通的，允许你先用镜头和值配置它，然后再给它提供数据。

还有第三个镜头应用函数叫做 [over()](https://ramdajs.com/docs/#over) ，它的作用就像`set()`一样，除了提供一个更新值的函数，而不是提供一个更新值的函数。所提供的函数将被传递透镜的 getter 的结果。假设我们想要大写这个人的`firstName` :

```
over(firstNameLens, toUpper, person)
// => {"firstName": "JOHN", "lastName": "Doe"} 
```

我们还利用了 Ramda 的 [toUpper()](https://ramdajs.com/docs/#toUpper) 函数。这相当于:

```
const toUpper = value => value.toUpperCase() 
```

我想回到我们最初的 getter 和 setter 函数，看看它们更简洁的编写方式。

```
const getFirstName = data => data.firstName // getter
const setFirstName = (value, data) => ({    // setter
  ...data, firstName: value
}) 
```

如果我们使用 Ramda 创建镜头，那么在代码的其他部分利用 Ramda 函数是有意义的。特别是，我们将使用 Ramda 的 [prop()](https://ramdajs.com/docs/#prop) 函数来替换我们的 getter 函数，使用 [assoc()](https://ramdajs.com/docs/#assoc) 函数来替换我们的 setter 函数。

`prop()`函数接受一个属性名和一个对象，并返回该对象上该属性名的值。它的工作方式非常类似于我们的 getter 函数。

```
prop('firstName', person) // => "John" 
```

同样，与大多数所有的 Ramda 函数一样，`prop()`是可调用的，允许我们用一个属性名来配置它，并在以后提供数据:

```
const firstNameProp = prop('firstName')
firstNameProp(person) // => "John" 
```

当它与镜头一起使用时，我们可以用一个属性名来配置它，并让镜头稍后传递它的数据。

```
lens(prop('firstName'), ...) 
```

这也是[无要点风格或默认编程](https://en.wikipedia.org/wiki/Tacit_programming)的一个例子，因为我们没有在我们的逻辑中定义一个或多个参数(在这个例子中是 person)。如果你不习惯 FP 中常见的这种风格，你可能很难理解这是如何工作的，但是当分解时，它可能更有意义...

当传递一个参数给一个多变量 T2 函数时，它返回一个新的函数，接受其余的参数。直到提供了所有的参数，它才执行它的函数体并返回结果。因此，当仅用属性名配置`prop()`时，我们将收到一个接受数据参数的新函数。这与镜头获取器非常匹配:一个接受数据参数的函数。

`assoc()`函数以同样的方式工作，但是它是为写而不是读而设计的。此外，它将返回正在写入的对象的副本，这与镜头设置器所需的功能相同。

```
assoc('firstName', 'Jane', person)
// => {"firstName": "Jane", "lastName": "Doe"} 
```

当与镜头一起使用时，我们可以仅用属性名配置`assoc()`，并让`set()`函数处理值和数据。

```
const firstNameLens = lens(prop('firstName'), assoc('firstName'))

view(firstNameLens, person) // => "John"
set(firstNameLens, 'Jane', person)
// => {"firstName": "Jane", "lastName": "Doe"} 
```

这些是镜头的基础，但在 Ramda 中还有其他更专业的镜头创建功能。具体来说， [lensProp()](https://ramdajs.com/docs/#lensProp) ， [lensIndex()](https://ramdajs.com/docs/#lensIndex) ， [lensPath()](https://ramdajs.com/docs/#lensPath) 。这些可能是你在创建镜头时最常使用的函数。一般的`lens()`只会在需要做非常个性化的镜头创作时使用。让我们来看看这些专门的镜头创建功能。

`lensProp()`函数接受一个参数；属性名。

```
const lastNameLens = lensProp('lastName') 
```

就是这样！属性名是生成适当的 getter 和 setter 所需要的全部内容:

```
view(lastNameLens, person) // => "Doe"
set(lastNameLens, 'Smith', person)
// => {"firstName": "John", "lastName": "Smith"} 
```

`lensIndex()`函数的工作方式与`lensProp()`相似，只是它是为关注数组索引而设计的，因此，传递给它的是一个索引而不是属性名。让我们给 person 添加一组数据来测试它。

```
const person = {
  firstName: 'John',
  lastName: 'Doe',
  phones: [
    {type: 'home', number: '5556667777'},
    {type: 'work', number: '5554443333'}
  ]
} 
```

然后当应用透镜时...

```
const firstPhoneLens = lensIndex(0)

view(firstPhoneLens, person.phones)
// => {"number": "5556667777", "type": "home"}

set(
  firstPhoneLens, 
  {type: 'mobile', number: '5557773333'}, 
  person.phones
)
// => [
//  {"number": "5557773333", "type": "mobile"}, 
//  {"number": "5554443333", "type": "work"}
//] 
```

请注意，在应用镜头时，我们必须传递`person.phones`。虽然这是可行的，但并不理想，因为现在我们依赖于一般应用程序代码中的对象形状知识，而不是将其隐藏在我们的镜头中。此外，当应用具有`set()`功能的镜头时，我们得到的是手机阵列，而不是人。这强调了无论你给镜头应用什么对象，你得到的都是一样的。下一步可能是将新的电话数组合并回 person 对象。当然，这需要以非突变的方式完成...拉姆达能轻松搞定的事。然而，最好不要采取额外的步骤。这就引出了第三个专业镜头，`lensPath()`，它是为聚焦嵌套数据而设计的。

```
const homePhoneNumberLens = lensPath(['phones', 0, 'number'])

view(homePhoneNumberLens, person) // => "5556667777"
set(homePhoneNumberLens, '5558882222', person)
// => {
//  "firstName": "John", "lastName": "Doe"
//  "phones": [
//    {"number": "5558882222", "type": "home"}, 
//    {"number": "5554443333", "type": "work"}
//  ]
//} 
```

正如您所看到的，`lensPath()`采用了一个数组，其中的路径段指向我们想要关注的嵌套数据。每个路径段可以是属性名或索引。因为我们给它提供了根 person 对象，所以我们得到了 person 对象的完整副本，只是家庭电话号码发生了变化。在我看来，这是镜头功能真正开始发光的地方。想象一下，如果我们想要复制上面的`set()`函数的结果，但是使用常规的 Javascript。即使有了最新的功能，比如扩展和析构，我们也可能会以类似下面的东西告终:

```
const [homePhone, ...otherPhones] = person.phones
const updatedPerson = {
  ...person,
  phones: [
    {...homePhone, number: '5558882222'},
    ...otherPhones
  ]
} 
```

与使用镜头的两行示例相比，这要做很多工作！

镜头更强大的功能之一是它们能够与其他镜头组合在一起。这允许你从现有的镜头中构建新的更复杂的镜头:

```
const phonesLens = lensProp('phones')
const workPhoneLens = lensIndex(1)
const phoneNumberLens = lensProp('number')

const workPhoneNumberLens = compose(
  phonesLens, 
  workPhoneLens, 
  phoneNumberLens
)

view(workPhoneNumberLens, person) // => "5554443333" 
```

结果与使用单个`lensPath()`没有太大区别。事实上，如果我在其他上下文中不需要单独的`phonesLens`和`workPhoneLens`，我可能会用一个`lensPath()`来代替。然而，这种方法的好处是，没有一个镜头能够完全了解一个人的整个形状。取而代之的是，每一个镜头只是记录下它们自己的形状，把这个责任从构图中的下一个镜头上卸下来。例如，如果我们要将属性名`phones`改为`phoneList`，我们只需要更新负责该形状的镜头(`phoneLens`)，而不是更新碰巧与该路径重叠的多个镜头。

这是用 Ramda 编写的 Javascript 中功能透镜的特性和优点的概要。