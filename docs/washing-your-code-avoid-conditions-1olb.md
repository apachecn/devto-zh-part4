# 清洗你的代码:避免条件

> 原文：<https://dev.to/sapegin/washing-your-code-avoid-conditions-1olb>

[![](img/5cfa32af222d24fd3089f5913d500c74.png)](https://leanpub.com/washingcode/)

你正在阅读的是我即将出版的关于干净代码的书《清洗你的代码:写一次，读七次》的节选[在 Leanpub](https://leanpub.com/washingcode/) 上预订，或者[在线阅读草稿](https://github.com/sapegin/washingcode-book/blob/master/manuscript/book.md)。

* * *

条件使得代码更难阅读和测试。它们增加了嵌套，使代码行更长，所以你必须把它们分成几行。每个条件都增加了您需要为某个模块或函数编写的测试用例的最小数量，因为您现在有两个或更多的代码路径，而不是一个代码路径。

## 不必要的条件

许多条件是不必要的，或者可以用更易读的方式重写。

例如，您可能会找到类似这样的代码，它返回一个布尔值:

```
const hasValue = value !== NONE ? true : false;
const hasProducts = products.length > 0 ? true : false; 
```

`value !== NONE`和`products.length > 0`已经给了我们布尔值，所以我们可以避开三元运算符:

```
const hasValue = value !== NONE;
const hasProducts = products.length > 0; 
```

即使初始值不是布尔值:

```
const hasValue = value ? true : false;
const hasProducts = products.length ? true : false; 
```

我们仍然可以通过将值显式转换为布尔值来避免这种情况:

```
const hasValue = Boolean(value); 
```

在所有情况下，没有三元组的代码既短又易读。

条件不必要的情况更多:

```
- const hasProducts = products && Array.isArray(products); + const hasProducts = Array.isArray(products); 
```

`Array.isArray`对任何 falsy 值返回`false`，无需单独检查。

和一个更复杂但伟大(而且真实！)不必要条件的例子:

```
function IsNetscapeOnSolaris() {
  var agent = window.navigator.userAgent;
  if (
    agent.indexOf('Mozilla') != -1 &&
    agent.indexOf('compatible') == -1
  ) {
    if (agent.indexOf('SunOS') != -1) return true;
    else return false;
  } else {
    return false;
  }
} 
```

整个条件块可以用一个表达式代替:

```
function IsNetscapeOnSolaris() {
  const { userAgent } = window.navigator;
  return (
    userAgent.includes('Mozilla') &&
    userAgent.includes('SunOS') &&
    !userAgent.includes('compatible')
  );
} 
```

我们已经消除了两级嵌套和相当多的样板代码，所以实际情况更容易理解。

## 处理数组

在对数组的元素进行循环之前，通常会检查数组的长度:

```
return getProducts().then(response => {
  const products = response.products;
  if (products.length > 0) {
    return products.map(product => ({
      label: product.name,
      value: product.id
    }));
  }
  return [];
}); 
```

所有的循环和数组函数，像`.map()`或`.filter()`都可以很好地处理空数组，所以我们可以安全地删除检查:

```
return getProducts().then(({ products }) =>
  products.map(product => ({
    label: product.name,
    value: product.id
  }))
); 
```

有时我们不得不使用现有的 API，它只在某些情况下返回一个数组，所以直接检查长度会失败，我们需要先检查类型:

```
return getProducts().then(response => {
  const products = response.products;
  if (Array.isArray(products) && products.length > 0) {
    return products.map(product => ({
      label: product.name,
      value: product.id
    }));
  }
  return [];
}); 
```

在这种情况下，我们无法避免这种情况，但是我们可以更早地移动它，并避免处理数组缺失的单独分支。根据可能的数据类型，有几种方法可以做到这一点。

如果我们的数据可以是数组或`undefined`，我们可以使用函数参数的默认值:

```
return getProducts().then((products = []) =>
  products.map(product => ({
    label: product.name,
    value: product.id
  }))
); 
```

或者对象的析构属性的默认值:

```
- return getProducts().then((products = []) => + return getProducts().then(({ products = [] }) => 
```

如果我们的数据可以是数组或`null`，那就更棘手了，因为只有当值严格为`undefined`时才使用默认值，而不仅仅是 falsy。在这种情况下，我们可以使用`||`操作符:

```
return getProducts().then(products =>
  (products || []).map(product => ({
    label: product.name,
    value: product.id
  }))
); 
```

我们仍然有一个条件，但整体代码结构更简单。

在所有这些示例中，我们都删除了一个单独的分支，并通过尽早规范化输入(将其转换为数组)来处理数据缺失问题，然后对规范化数据运行通用算法。

数组很方便，因为我们不必担心它们包含多少项:相同的代码可以处理一百项、一项甚至没有项。

当输入是一个单独的条目或一个数组时，类似的技术也适用:

```
return getProducts().then(({ products }) =>
  (Array.isArray(products) ? products : [products]).map(product => ({
    label: product.name,
    value: product.id
  }))
); 
```

这里我们将单个项目包装在一个数组中，所以我们可以使用相同的代码来处理单个项目和数组。

### 重复数据删除算法

上一节中的示例介绍了一项重要的技术:算法重复数据删除。根据输入的性质，主逻辑没有几个分支，我们只有一个分支。但是在运行算法之前，我们将输入标准化。这个技术可以用在其他地方。

假设您有一个文章投票计数器，类似于 Medium，您可以多次投票:

```
const articles = counter();
articles.upvote('/foo');
articles.upvote('/bar', 5);
articles.downvote('/foo');
/* {
 *   '/bar': 5
 * }
 */ 
```

实现`upvote`方法的一个简单方法可以是:

```
function counter() {
  const counts = {};
  return {
    upvote(url, votes = 1) {
      if (url in counts) {
        counts[url] += votes;
      } else {
        counts[url] = votes;
      }
    }
  };
} 
```

这里的问题是主函数逻辑 count increment 实现了两次:一次是我们已经为那个 URL 投票，另一次是我们第一次投票。所以每次需要更新这个逻辑的时候，需要在两个地方做改动。您需要编写两组非常相似的测试，以确保两个分支都按预期工作，否则它们最终会出现分歧，您将很难调试问题。

让我们将主逻辑设为无条件的，但是在运行逻辑之前，如果必要的话，准备好状态:

```
function counter() {
  const counts = {};
  return {
    upvote(url, votes = 1) {
      if (!(url in counts)) {
        counts[url] = 0;
      }

      counts[url] += votes;
    }
  };
} 
```

现在我们没有任何逻辑重复。我们正在规范化数据结构，因此通用算法可以使用它。

当有人用不同的参数调用函数时，我经常看到类似的问题:

```
if (errorMessage) {
  alert(errorMessage);
} else {
  alert(DEFAULT_ERROR_MESSAGE);
} 
```

让我们在函数调用内部移动一个条件:

```
alert(errorMessage || DEFAULT_ERROR_MESSAGE); 
```

我们已经删除了所有重复的代码，代码更短，更容易阅读。

## 提前返回

应用*保护条款*，或者*提前返回*，是避免嵌套条件的一个好方法。一系列嵌套条件，也称为[箭头反模式](http://wiki.c2.com/?ArrowAntiPattern)或*危险深度嵌套*，常用于错误处理:

```
function postOrderStatus(orderId) {
  var idsArrayObj = getOrderIds();

  if (idsArrayObj != undefined) {
    if (idsArrayObj.length == undefined) {
      var tmpBottle = idsArrayObj;
      idsArrayObj = new Array(tmpBottle);
    }

    var fullRecordsArray = new Array();
    // 70 lines of code

    if (fullRecordsArray.length != 0) {
      // 40 lines of code
      return sendOrderStatus(fullRecordsArray);
    } else {
      return false;
    }
  } else {
    return false;
  }
} 
```

第一个条件和它的`else`块之间有 120 行。主要返回值在三层条件中的某处。

让我们解开这个意大利面条怪物:

```
function postOrderStatus(orderId) {
  let idsArrayObj = getOrderIds();
  if (idsArrayObj === undefined) {
    return false;
  }

  if (!Array.isArray(idsArrayObj)) {
    idsArrayObj = [idsArrayObj];
  }

  const fullRecordsArray = [];

  // 70 lines of code
  if (fullRecordsArray.length === 0) {
    return false;
  }

  // 40 lines of code
  return sendOrderStatus(fullRecordsArray);
} 
```

这个函数仍然很长，但是因为代码结构更简单，所以更容易理解。

现在我们在函数中最多有一层嵌套，主返回值在没有嵌套的最后。我们添加了两个保护子句，以便在没有数据要处理时提前退出函数。

我不太确定第二个条件中的代码是做什么的，但是它看起来像是在一个数组中包装一个元素，就像我们在上一节中做的那样。

不，我不知道`tmpBottle`是什么意思，也不知道为什么需要它。

这里的下一步可能是改进`getOrderIds()`函数的 API。它可以返回三种不同的东西:`undefined`，一个单项，或者一个数组。我们必须分别处理每一个，所以在函数的最开始我们有两个条件，我们重新分配了`idsArrayObj`变量。

通过让`getOrderIds()`函数总是返回一个数组，并确保`// 70 lines of code`中的代码使用一个空数组，我们可以消除两个条件:

```
function postOrderStatus(orderId) {
  const orderIds = getOrderIds(); // Always an array

  const fullRecordsArray = [];

  // 70 lines of code
  if (fullRecordsArray.length === 0) {
    return false;
  }

  // 40 lines of code
  return sendOrderStatus(fullRecordsArray);
} 
```

这比最初的版本有了很大的改进。我还重命名了`idsArrayObj`变量，因为“数组对象”对我来说没有任何意义。

下一步超出了本节的范围:`// 70 lines of code`中的代码使`fullRecordsArray`变异。

## 重复条件

重复的条件会使代码几乎不可读。让我们来看看这个函数，它返回宠物店中某个产品的特价信息。我们有两个品牌，角&蹄和爪&尾，他们有独特的优惠。由于历史原因，我们以不同的方式将它们存储在缓存中:

```
function getSpecialOffersArray(sku, isHornsAndHooves) {
  let specialOffersArray = isHornsAndHooves
    ? Session.get(SPECIAL_OFFERS_CACHE_KEY + '_' + sku)
    : Session.get(SPECIAL_OFFERS_CACHE_KEY);
  if (!specialOffersArray) {
    const hornsAndHoovesOffers = getHornsAndHoovesSpecialOffers();
    const pawsAndTailsOffers = getPawsAndTailsSpecialOffers();
    specialOffersArray = isHornsAndHooves
      ? hornsAndHoovesOffers
      : pawsAndTailsOffers;
    Session.set(
      isHornsAndHooves
        ? SPECIAL_OFFERS_CACHE_KEY + '_' + sku
        : SPECIAL_OFFERS_CACHE_KEY,
      specialOffersArray
    );
  }
  return specialOffersArray;
} 
```

`isHornsAndHooves`条件重复三次。其中两个创建相同的会话密钥。很难看出这个函数在做什么:业务逻辑与低级会话管理代码交织在一起。

让我们试着简单一点:

```
function getSpecialOffersArray(sku, isHornsAndHooves) {
  const cacheKey = isHornsAndHooves
    ? `${SPECIAL_OFFERS_CACHE_KEY}_${sku}`
    : SPECIAL_OFFERS_CACHE_KEY;

  const cachedOffers = Session.get(cacheKey);
  if (cachedOffers) {
    return cachedOffers;
  }

  const offers = isHornsAndHooves
    ? getHornsAndHoovesSpecialOffers()
    : getPawsAndTailsSpecialOffers();

  Session.set(cacheKey, offers);

  return offers;
} 
```

这已经更具可读性，在这里停下来可能是个好主意。但是如果我有时间，我会更进一步，提取缓存管理。不是因为这个函数太长或者它有潜在的可重用性，而是因为缓存管理分散了我对函数主要目的的注意力，而且它太低级了。

```
const getSessionKey = (key, isHornsAndHooves, sku) =>
  isHornsAndHooves ? `${key}_${sku}` : key;

const sessionGet = (key, isHornsAndHooves, sku) =>
  Session.get(getSessionKey(key, isHornsAndHooves, sku));

const sessionSet = (key, sku, isHornsAndHooves, value) =>
  Session.get(getSessionKey(key, isHornsAndHooves, sku), value);

function getSpecialOffersArray(sku, isHornsAndHooves) {
  const cachedOffers = sessionGet(
    SPECIAL_OFFERS_CACHE_KEY,
    isHornsAndHooves,
    sku
  );
  if (cachedOffers) {
    return cachedOffers;
  }

  const offers = isHornsAndHooves
    ? getHornsAndHoovesSpecialOffers()
    : getPawsAndTailsSpecialOffers();

  sessionSet(SPECIAL_OFFERS_CACHE_KEY, isHornsAndHooves, sku, offers);

  return offers;
} 
```

它看起来可能不会更好，但我认为它更容易理解主函数中发生了什么。这里让我恼火的是`isHornsAndHooves`。我宁愿传递一个品牌名称，并将所有特定于品牌的信息保存在表中:

```
const BRANDS = {
  HORNS_AND_HOOVES: 'Horns & Hooves',
  PAWS_AND_TAILS: 'Paws & Tails'
};

const getSpecialOffersForBrand = brand =>
  ({
    [BRANDS.HORNS_AND_HOOVES]: getHornsAndHoovesSpecialOffers,
    [BRANDS.PAWS_AND_TAILS]: getPawsAndTailsSpecialOffers
  }[brand]());

const getSessionKey = (key, brand, sku) =>
  ({
    [BRANDS.HORNS_AND_HOOVES]: `${key}_${sku}`,
    [BRANDS.PAWS_AND_TAILS]: key
  }[brand]);

const sessionGet = (key, brand, sku) =>
  Session.get(getSessionKey(key, brand, sku));

const sessionSet = (key, sku, brand, value) =>
  Session.get(getSessionKey(key, brand, sku), value);

function getSpecialOffersArray(sku, brand) {
  const cachedOffers = sessionGet(
    SPECIAL_OFFERS_CACHE_KEY,
    brand,
    sku
  );
  if (cachedOffers) {
    return cachedOffers;
  }

  const offers = getSpecialOffersForBrand(brand);
  sessionSet(SPECIAL_OFFERS_CACHE_KEY, brand, sku, offers);
  return offers;
} 
```

现在很清楚，这里唯一的业务逻辑是`getSpecialOffersForBrand`，其余的是缓存。如果我们不止一次使用这个模式，我会把它提取到自己的模块中，类似于 Lodash:
中的 [memoize 函数](https://lodash.com/docs/#memoize)

```
const BRANDS = {
  HORNS_AND_HOOVES: 'Horns & Hooves',
  PAWS_AND_TAILS: 'Paws & Tails'
};

const getSessionKey = (key, brand, sku) =>
  ({
    [BRANDS.HORNS_AND_HOOVES]: `${key}_${sku}`,
    [BRANDS.PAWS_AND_TAILS]: key
  }[brand]);

const sessionGet = (key, brand, sku) =>
  Session.get(getSessionKey(key, brand, sku));

const sessionSet = (key, brand, sku, value) =>
  Session.get(getSessionKey(key, brand, sku), value);

const withSessionCache = (key, fn) => (brand, sku, ...args) => {
  const cachedValue = sessionGet(key, brand, sku);
  if (cachedValue) {
    return cachedValue;
  }

  const value = fn(brand, sku, ...args);
  sessionSet(key, brand, sku, value);
  return value;
};

// --- 8< -- 8< ---

const getSpecialOffersArray = withSessionCache(
  SPECIAL_OFFERS_CACHE_KEY,
  brand =>
    ({
      [BRANDS.HORNS_AND_HOOVES]: getHornsAndHoovesSpecialOffers,
      [BRANDS.PAWS_AND_TAILS]: getPawsAndTailsSpecialOffers
    }[brand]())
); 
```

我们能够分离所有低级代码并将其隐藏在另一个模块中。

看起来我更喜欢小函数，甚至是非常小的函数，但事实并非如此。这里将代码提取到单独的函数中的主要原因是违反了[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)。最初的函数有太多的职责:获取特价商品、生成缓存键、从缓存中读取数据、将数据存储在缓存中。他们都有两个分公司，负责我们的两个品牌。

## 表格或地图

我最喜欢的改善*(理解:避免)*状况的技巧之一是用表格或地图来代替它们。通过 JavaScript，您可以使用普通对象创建表格或地图。

我们已经将此作为上面“特价”重构示例的一部分。现在让我们看一个更简单的例子。这个例子可能有点极端，但实际上我在 19 年前就写了这段代码:

```
if (month == 'jan') month = 1;
if (month == 'feb') month = 2;
if (month == 'mar') month = 3;
if (month == 'apr') month = 4;
if (month == 'may') month = 5;
if (month == 'jun') month = 6;
if (month == 'jul') month = 7;
if (month == 'aug') month = 8;
if (month == 'sep') month = 9;
if (month == 'oct') month = 10;
if (month == 'nov') month = 11;
if (month == 'dec') month = 12; 
```

让我们用一个表格来代替条件:

```
const MONTH_NAME_TO_NUMBER = {
  jan: 1,
  feb: 2,
  mar: 3,
  apr: 4,
  may: 5,
  jun: 6,
  jul: 7,
  aug: 8,
  sep: 9,
  oct: 10,
  nov: 11,
  dec: 12
};
const month = MONTH_NAME_TO_NUMBER[monthName]; 
```

数据周围几乎没有样板代码，可读性更好，看起来像一个表格。还要注意，原始代码中没有括号:在大多数现代风格的指南中，条件体周围需要括号，并且条件体应该自成一行，因此这段代码将会是原来的三倍长，可读性更差。

或者更现实一点的常见例子:

```
const DECISION_YES = 0;
const DECISION_NO = 1;
const DECISION_MAYBE = 2;

const getButtonLabel = decisionButton => {
  switch (decisionButton) {
    case DECISION_YES:
      return (
        <FormattedMessage
          id="decisionButtonYes"
          defaultMessage="Yes"
        />
      );
    case DECISION_NO:
      return (
        <FormattedMessage id="decisionButtonNo" defaultMessage="No" />
      );
    case DECISION_MAYBE:
      return (
        <FormattedMessage
          id="decisionButtonMaybe"
          defaultMessage="Maybe"
        />
      );
  }
};

// And later it's used like this
<Button>{getButtonLabel(decision.id)}</Button>; 
```

这里我们有一个`switch`语句来返回三个按钮标签中的一个。

首先，让我们用一个表来代替`switch`:

```
const DECISION_YES = 0;
const DECISION_NO = 1;
const DECISION_MAYBE = 2;

const getButtonLabel = decisionButton =>
  ({
    [DECISION_YES]: (
      <FormattedMessage id="decisionButtonYes" defaultMessage="Yes" />
    ),
    [DECISION_NO]: (
      <FormattedMessage id="decisionButtonNo" defaultMessage="No" />
    ),
    [DECISION_MAYBE]: (
      <FormattedMessage
        id="decisionButtonMaybe"
        defaultMessage="Maybe"
      />
    )
  }[decisionButton]);

// And later it's used like this
<Button>{getButtonLabel(decision.id)}</Button>; 
```

object 语法比`switch`语句更轻量级，可读性更强。

我们甚至可以通过将我们的`getButtonLabel`函数转换成 React 组件:
来使这段代码更习惯于 React

```
const DECISION_YES = 0;
const DECISION_NO = 1;
const DECISION_MAYBE = 2;

const ButtonLabel = ({ decision }) =>
  ({
    [DECISION_YES]: (
      <FormattedMessage id="decisionButtonYes" defaultMessage="Yes" />
    ),
    [DECISION_NO]: (
      <FormattedMessage id="decisionButtonNo" defaultMessage="No" />
    ),
    [DECISION_MAYBE]: (
      <FormattedMessage
        id="decisionButtonMaybe"
        defaultMessage="Maybe"
      />
    )
  }[decision]);

// And later it can be used like this
<Button>
  <ButtonLabel decision={decision.id} />
</Button>; 
```

现在，实现和使用都更简单了。

另一个现实且常见的例子是表单验证:

```
function validate(values) {
  const errors = {};

  if (!values.name || (values.name && values.name.trim() === '')) {
    errors.name = (
      <FormattedMessage
        id="errorNameRequired"
        defaultMessage="Name is required"
      />
    );
  }

  if (values.name && values.name.length > 80) {
    errors.name = (
      <FormattedMessage
        id="errorMaxLength80"
        defaultMessage="Maximum 80 characters allowed"
      />
    );
  }

  if (!values.address1) {
    errors.address1 = (
      <FormattedMessage
        id="errorAddressRequired"
        defaultMessage="Address is required"
      />
    );
  }

  if (!values.email) {
    errors.mainContactEmail = (
      <FormattedMessage
        id="errorEmailRequired"
        defaultMessage="Email is required"
      />
    );
  }

  if (!values.login || (values.login && values.login.trim() === '')) {
    errors.login = (
      <FormattedMessage
        id="errorLoginRequired"
        defaultMessage="Login is required"
      />
    );
  }

  if (values.login && values.login.indexOf('  ') > 0) {
    errors.login = (
      <FormattedMessage
        id="errorLoginWithoutSpaces"
        defaultMessage="No spaces are allowed in login."
      />
    );
  }

  if (values.address1 && values.address1.length > 80) {
    errors.address1 = (
      <FormattedMessage
        id="errorMaxLength80"
        defaultMessage="Maximum 80 characters allowed"
      />
    );
  }

  // 100 lines of code

  return errors;
} 
```

这个函数非常长，有很多很多重复的样板代码。真的很难读和维护。有时，同一个字段的验证不会组合在一起。

但是如果我们仔细观察，就会发现只有三个独特的验证:

*   必填字段(在某些情况下，前导和尾随空格会被忽略，在某些情况下，很难说这是有意还是无意)；
*   最大长度(总是 80)；
*   不允许有空格。

首先，让我们将所有的验证提取到它们自己的函数中，这样我们可以在以后重用它们:

```
const hasStringValue = value => value && value.trim() !== '';
const hasLengthLessThanOrEqual = max => value =>
  !hasStringValue(value) || (value && value.length <= max);
const hasNoSpaces = value =>
  !hasStringValue(value) || (value && value.includes('  ')); 
```

我假设不同的空白处理是一个错误。我还颠倒了所有的条件，以验证正确的值，而不是不正确的值，在我看来，这更具可读性。

请注意，`hasLengthLessThanOrEqual`和`hasNoSpaces`仅在值存在时检查条件，这将允许我们创建可选字段。还要注意，`hasLengthLessThanOrEqual`函数是可定制的:我们需要传递最大长度:`hasLengthLessThanOrEqual(80)`。

现在我们可以定义我们的验证表了。有两种方法可以做到这一点:

*   使用关键字表示表单字段的对象
*   使用数组

我们将使用第二个选项，因为我们希望对一些字段使用不同的错误消息进行多次验证，例如一个字段可能是必需的*和*具有最大长度:

```
const validations = [
  {
    field: 'name',
    validation: hasStringValue,
    message: (
      <FormattedMessage
        id="errorNameRequired"
        defaultMessage="Name is required"
      />
    )
  },
  {
    field: 'name',
    validation: hasLengthLessThanOrEqual(80),
    message: (
      <FormattedMessage
        id="errorMaxLength80"
        defaultMessage="Maximum 80 characters allowed"
      />
    )
  }
  // All other fields
]; 
```

现在我们需要迭代这个数组，并对所有字段进行验证:

```
function validate(values, validations) {
  return validations.reduce((errors, ({field, validation, message}) => {
    if (!validation(values[field])) {
      errors[field] = message;
    }
    return errors;
  }, {})
} 
```

我们又一次将“什么”和“如何”分开了:我们有一个可读且可维护的验证列表(“什么”)、一组可重用的验证函数和一个用于验证表单值(“如何”)的`validate`函数，表单值也可以被重用。

*提示:使用第三方库，如[是的](https://github.com/jquense/yup)或 [Joi](https://github.com/hapijs/joi) 会使代码更短，让你不必自己编写验证函数。*

你可能觉得我这本书里类似的例子太多了，你说的没错。但是我认为这样的代码是如此普遍，用表代替条件在可读性和可维护性方面的好处是如此巨大，所以值得重复。所以这里再举一个例子(最后一个，我保证！):

```
const getDateFormat = format => {
  const datePart = 'D';
  const monthPart = 'M';

  switch (format) {
    case DATE_FORMAT_ISO:
      return `${monthPart}-${datePart}`;
    case DATE_FORMAT_DE:
      return `${datePart}.${monthPart}`;
    case DATE_FORMAT_UK:
      return `${datePart}/${monthPart}`;
    case DATE_FORMAT_US:
    default:
      return `${monthPart}/${datePart}`;
  }
}; 
```

虽然只有 15 行代码，但我觉得这段代码很难读懂。我认为`switch`是绝对不必要的，而且`datePart`和`monthPart`变量使代码变得如此混乱，以至于几乎无法阅读。

```
const DATE_FORMATS = {
  [DATE_FORMAT_ISO]: 'M-D',
  [DATE_FORMAT_DE]: 'D.M',
  [DATE_FORMAT_UK]: 'D/M',
  [DATE_FORMAT_US]: 'M/D',
  _default: 'M/D'
};

const getDateFormat = format => {
  return DATE_FORMATS[format] || DATE_FORMATS._default;
}; 
```

改进后的版本并没有缩短多少，但是现在很容易看到所有的日期格式。我们将数据提取到一个简短可读的对象中，并将其与访问该数据的正确部分的代码分离开来。

## 嵌套三角形

三元运算符是一种简短的单行条件运算符。当您想要将两个值中的一个赋值给一个变量时，这非常有用。比较`if`语句:

```
let drink;
if (caffeineLevel < 50) {
  drink = DRINK_COFFEE;
} else {
  drink = DRINK_WATER;
} 
```

带三元:

```
const drink = caffeineLevel < 50 ? DRINK_COFFEE : DRINK_WATER; 
```

但是嵌套 ternaries 是不同的野兽:它们通常使代码难以阅读，而且几乎总是有更好的选择:

```
function Products({products, isError, isLoading}) {
  return isError
    ? <p>Error loading products</p>
      : isLoading
        ? <Loading />
        : products.length > 0
          ? <ul>{products.map(
              product => <li>{product.name}</li>
            )}</ul>
          : <p>No products found</p>
} 
```

这是一种罕见的情况，当漂亮使代码完全不可读:

```
function Products({ products, isError, isLoading }) {
  return isError ? (
    <p>Error loading products</p>
  ) : isLoading ? (
    <Loading />
  ) : products.length > 0 ? (
    <ul>
      {products.map(product => (
        <li>{product.name}</li>
      ))}
    </ul>
  ) : (
    <p>No products found</p>
  );
} 
```

但也许这是有意的，是我们应该重写它的一个信号。

在本例中，我们根据加载操作的状态呈现了四个 ui 中的一个:

*   旋转器(装载)；
*   错误消息(失败)；
*   产品清单(成功)；
*   没有产品的消息(也是成功)。

让我们使用已经熟悉的早期返回模式来重写这段代码:

```
function Products({ products, isError, isLoading }) {
  if (isError) {
    return <p>Error loading products</p>;
  }

  if (isLoading) {
    return <Loading />;
  }

  if (products.length === 0) {
    return <p>No products found</p>;
  }

  return (
    <ul>
      {products.map(product => (
        <li>{product.name}</li>
      ))}
    </ul>
  );
} 
```

我觉得现在好办多了:所有特例都在函数顶端，快乐路径在末端。

我们将在本书的后面回到这个例子。

* * *

开始思考:

*   删除不必要的条件，比如手动将一个已经存在的布尔值传送给`true`或`false`。
*   通过尽早将缺少的数据转换为数组来规范化输入数据，以避免分支和单独处理无数据。
*   规范化状态以避免算法重复。
*   缓存变量中的重复条件。
*   用表格或地图代替长组条件。

* * *

*如果你有任何反馈，[发微博给我](https://twitter.com/iamsapegin)，[在 GitHub 上开问题](https://github.com/sapegin/washingcode-book/issues)，或者发邮件给我 [artem@sapegin.ru](//mailto:artem@sapegin.ru) 。[在 Leanpub](https://leanpub.com/washingcode/) 上预订书籍或者[在线阅读草稿](https://github.com/sapegin/washingcode-book/blob/master/manuscript/book.md)。*