# javascript 应用程序中的高效行为跟踪

> 原文：<https://dev.to/duranenmanuel/efficient-behavioral-tracking-in-javascript-applications-g3e>

*原载于[Enmascript.com](https://enmascript.com/articles/2019/07/15/efficient-behavioral-tracking-in-javascript-applications)T3】*

跟踪是产品开发的一个重要部分，无论是对于网络、移动应用还是你正在开发的任何软件；了解你的用户对你的业务发展至关重要。在本文中，我们将探索使用 javascript 实现跟踪的多种选择和模式。

如果你不知道**行为跟踪**是什么，或者你没有在你的项目中实现跟踪，我可以把解释压缩在一个段落中:

<mark>***行为追踪**是公司获取关于在其平台/应用中发生的有意义事件的有价值信息的方式；这对于理解用户的行为和识别特定流中潜在的失败和机会特别有用。*</mark>

正如你在上面的简单定义中所读到的，它是关于从事件中获取有价值的信息，例如*行动号召点击，用户登录...*作为开发人员，要实现这一点，我们需要一个技术实现，使我们能够以高效和可扩展的方式应用这一点，但是，正如您将很快意识到的，跟踪会带来一些技术挑战。

## 一个起点

通常，您会发现在代码库之间有专门用于跟踪的独立模块是相当常见的，这些模块只是简单的封装功能，允许您将信息发送到端点，该端点存储基于特定类型的事件从用户接收的有效负载。

下面是一个跟踪模块的简单实现:

```
class Tracker {
    static get ENDPOINT_URL() {
        return "my.endpoint.domain/tracking"
    }

    async track(payload) {
        const response = await fetch(
            Tracker.ENDPOINT_URL,
            {
                 method: 'POST',
                 headers: {
                     'Content-Type': 'application/json',
                 },
                 body: JSON.stringify(payload)
            }
        );

        return response;
    }

    ...
} 
```

正如您在上面看到的，我们只是创建了一个包含允许我们向端点发布信息的方法的类；这个例子过于简单，但对于本文的目的来说已经足够了，在真实的场景中，您将/应该有一个模型来验证您想要跟踪的参数和作为有效负载发送的数据类型。

<mark>就本文的目的而言，我们将从跟踪订阅按钮开始，这样我们就可以了解有多少用户在使用它。</mark>

```
<button class="js-tracked-click subscription-button">
    Subscription Button 1
</button> 
```

让我们看看如何应用不同的模式来跟踪这个相同的元素。

## 在模块跟踪中

包括在应用程序的模块中导入跟踪模块，并在逻辑/相关代码块中注入跟踪功能。这种模式的实现应该是这样的:

```
import Tracker from './Tracker';

class SubscriptionButton {
    constructor() {
        this._buttonHandler();
    }

    _onButtonClick() {
        console.log('Click handler function');

        Tracker.track({
            type: 'click',
            element: 'Subscription_button_1'
        });
    }

    _buttonHandler() {
        const button = document.querySelector('.js-tracked-click');

        button.addEventListener('click', this._onButtonClick.bind(this));
    }

    ...
} 
```

非常简单和实用，这种方法被广泛使用，它有一些好的和不好的部分，让我们分析它们:

### 优点:

*   **灵活性**。因为我们在脚本的功能中添加了跟踪方法，所以很容易将跟踪添加到几乎任何逻辑中。
*   **简约**。添加追踪器是一项简单的任务，因为它只是将函数添加到需要它的逻辑中。
*   **统一**。跟踪代码与原始的脚本代码在同一个地方，虽然这一方面不好，但另一方面好的方面是，它允许您在必须对功能进行更改时随时了解它。

### 常量:

*   **不尊重单一责任**。在脚本的核心代码中添加跟踪功能违反了单一责任原则。
*   **被跟踪的元素不易识别**。每个脚本的核心都包含跟踪功能，这意味着我们需要查看它的定义，并查看可能添加跟踪功能的代码
*   可伸缩性风险:由于这种方法非常灵活，很快就会失控，所以建立一些基本规则可能是个好主意。

## 通过扩展其原始定义来隔离被跟踪的方法

扩展原始类是另一种方法，它试图将被跟踪的元素隔离在原始脚本功能之外，其思想是扩展代码以创建一个专门用于跟踪事件的额外层，让我们看一个示例:

我们实现了脚本功能:

```
class SubscriptionButton {
    constructor() {
        this._buttonHandler();
    }

    _buttonHandler() {
        this._button = document.querySelector('.js-tracked-click');

        this._button.addEventListener('click', this.onButtonClick.bind(this));
    }

    _onButtonClick() {
        this.elementHasClass = e.currentTarget.classList.contains('subscription-button');

        if (this.elementHasClass) {
            console.log('Click handler function');
        }
    }

    ...
} 
```

然后我们实现跟踪:

```
import Tracker from './Tracker';

class TrackedSubscriptionButton extends SubscriptionButton {
    constructor() {
        super();

        this._trackedMethods();
    }

    _trackedMethods() {
        this._onButtonClickTracking();
        this._anotherTrackedElement();
    }

    _onButtonClickTracking() {
        if (super.elementHasClass) {
            super._button.addEventListener(
                'click',
                () => Tracker.track({
                    type: 'click',
                    element: 'Subscription_button_1'
                });
            );
        }
    }

    _anotherTrackedElement() { ... }
} 
```

请注意我们如何在不同的类中隔离与跟踪相关的代码，您必须意识到**我们必须小心不要复制您想要跟踪的元素的逻辑**，确保逻辑是可跟踪的并且可从原始类中重用，请注意在上面的案例中，我们使用了新的事件侦听器和条件，但是条件实际上与父类相同，我们只是重用了定义它的属性。这种方法不一定要用继承来实现；如果你想写功能性和声明性代码，你可以使用一个[高阶函数](https://enmascript.com/articles/2019/02/27/higher-order-functions-and-some-great-applications-in-javascript)，它包装了跟踪功能。

### 优点

*   **跟踪代码被隔离**。尊重单一责任原则。
*   **跟踪的元素很容易被检测、修改和删除**，这很容易实现，因为每个模块的所有内容都在一个位置。
*   **可扩展性**。如果这种方法应用得好，您可以轻松地扩展您的代码库。

### 常数

*   **灵活但有限制。我们可以向任何想要的元素添加跟踪，但是我们必须记住跟踪类..**
*   **心态转变**。当使用这种方法时，你需要像在单元测试中一样，在头脑中始终保持跟踪，你需要始终确保你的代码在隔离类中是可跟踪的，这可能是好的，但必须经过深思熟虑。
*   **危险代码和重复逻辑**。如果您注意到 tracking 类，您会看到我们添加了一个特定的侦听器来跟踪点击事件，这可能是危险的，尤其是如果您需要在跟踪周围添加逻辑(如条件)。此外，您必须通过`this`公开属性，这样父类才能被继承和使用。

### 一种定制的方法

保持跟踪可伸缩性和个性化的另一种方法是创建一个定制的中心跟踪系统，这种模式很普遍，我看到它在多家公司中使用，它通常基于数据集属性跟踪交互，例如，假设您想跟踪某个元素的点击:

要跟踪的元素:

```
<button data-click-tracking="subscription_button_left">
    Subscribe
</button>

<button data-click-tracking="subscription_button_right">
    Subscribe
</button> 
```

统一点击跟踪器功能:

```
import Tracker from './Tracker';

class ClickTracker {
    constructor() {
        this._bindClicks();
    }

    static get TRACKED_ATTRIBUTE() {
        return 'data-click-tracking';
    }

    static get TRACKED_ELEMENTS() {
        return document.querySelectorAll(`[${ClickTracker.TRACKED_ATTRIBUTE}]`);
    }

    _onClickHandler(event) {
        const element = event.currentTarget.getAttribute(ClickTracker.TRACKED_ATTRIBUTE);

        Tracker.track({ type: 'click', element }));
    }

    _bindClicks() {
        ClickTracker.TRACKED_ELEMENTS.forEach(element => {
            element.addEventListener('click', this._onClickHandler.bind(this));
        });
    }
} 
```

这样，所有被点击跟踪的元素都通过点击处理程序，我们可以通过数据集属性使用自定义 id 来识别它们。公司使用这种方法的一个很好的例子是 Google on[Google tag manager](https://tagmanager.google.com/)，你可以定义要跟踪的自定义类或数据属性，并将信息发送到 *Google Analytics* 。我认为这种方法是目前为止提到的最好的方法，因为你可以将同样的模式应用于其他类型的事件，比如滚动事件，它不局限于点击。

### 优点

*   **自定义实现**。为公司的特定需求而制造。
*   **可扩展性。**单个脚本负责跟踪，因此其他脚本保持不变。
*   **单一责任**，因为跟踪功能在专用模块中，所以它被保留。

### 弊

*   **约束存在。**由于这种方法是基于跟踪 DOM 中的元素，所以不可能覆盖所有情况，你会发现特殊的功能仍然需要跟踪其核心代码，这意味着在特殊情况下，你必须导入跟踪模块，并决定你想采用模块跟踪中的[还是](#in-module-tracking)[扩展方法](#isolating-tracked-methods-by-extending-its-original-definition)。

## 跟踪异步请求

通常，您会发现自己需要跟踪表单提交或登录事件，由于许多原因，将跟踪添加到提交信息的按钮是没有效率的(登录可能失败，或者表单请求可能返回错误)，这意味着我们将错误地跟踪数据。

为此，您可以通过向`200`响应添加跟踪功能来使用模块跟踪方法中的[,这很好，但是我们将为每个需要跟踪的请求赋予多个条件。](#in-module-tracking)

假设您有一个用于所有异步请求的集中式 HTTP 客户端(几乎总是如此)；这个客户返回一个承诺，以便您可以在每个模块中执行一些代码，然后我们被分配一些跟踪要求如下:

<mark>我们希望跟踪以下事件，以获得关于我们用户的一些有意义的信息，并了解我们如何改善他们在平台上的体验:</mark>

*   <mark>成功登录事件</mark>
*   <mark>成功认购事件</mark>
*   <mark>注销事件</mark>
*   <mark>行动号召点击</mark>

因此，我们注意到，通过点击跟踪事件可以很容易地跟踪行动号召点击，但是其他的呢？所有这些都是不同的事件，使用不同的 URL，需要跟踪不同的数据，所以如果我们使用集中式 HTTP 客户端，它看起来会像这样:

```
function HTTPPost(url = '', data = {}) {
    return fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
         },
        cache: 'no-cache',
        redirect: 'follow',
        referrer: 'no-referrer',
        body: JSON.stringify(data),
    })
    .then(response => response.json());
}

export default HTTPPost; 
```

然后我们可以用它来跟踪数据，比如:

```
import HTTPPost from './http-client';

HTTPPost('/api/login', {userId, password, source: 'modal' })
    .then(response => {
        Tracker.track({ type: 'successful-login', ...response })
    }
    .catch(error => console.error(error)) 
```

上面的方法实际上并不坏，但是我们必须在每个文件中导入 Tracker 模块，该模块将执行成功的异步请求，根据公司的政策，这有时会令人失望。

## 集中异步跟踪

这将是我们在本文中讨论的最后一种方法，也是我非常喜欢的一种方法。这种方法的基础依赖于在`HTTPPost`方法中添加一次跟踪功能，然后我们可以利用一个包含我们想要跟踪的 URL 的字典，这些 URL 将被映射到一个属性模型，其中每个 URL 都需要被成功跟踪，如下所示:

[![](img/bf34005957f3690a8a4090c769a3c1a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yhs9XLVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://enmascript.cimg/2019-07-15-behavioral-tracking-async.png)

让我们用代码一步步解释:

### 1)我们在 HTTPClient 中添加了跟踪

我们基本上采用前面方法的代码，并在承诺响应上添加跟踪:

```
import Tracker from './Tracker';

function HTTPPost(url = '', data = {}) {
    return fetch(url, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
         },
        cache: 'no-cache',
        redirect: 'follow',
        referrer: 'no-referrer',
        body: JSON.stringify(data),
    })
    .then(response => response.json())
    .then(response => Tracker.request(url, response));
}

export default HTTPPost; 
```

正如您所看到的，我们正在对所有请求执行`Tracker.request`，现在我们必须定义我们实际想要跟踪哪些请求，以及哪些参数与这些请求的跟踪相关，因此我们可以使用这样的字典:

```
const TRACKED_URLS = {
    '/api/login': ['userId', 'source', 'url', 'type'],
    '/api/logout': ['userId', 'time', 'type'],
    'api/subscription': ['userId', 'source', 'type'],
    ...
};

export default TRACKED_URLS; 
```

<mark>在上面的例子中，我们使用一个列表来存储有效的属性，只是为了使例子更简单，您可以创建一个真实的模型来正确验证每个被跟踪的 URL 需要的信息</mark>。此后，负责跟踪请求的方法可以添加到跟踪模块中。我们可以这样做:

```
import TRACKED_URLS from './tracked-urls';

class Tracker {
    static get ENDPOINT_URL() {
        return "my.endpoint.domain/tracking"
    }

    async track(payload) {
        const response = await fetch(
            Tracker.ENDPOINT_URL,
            {
                 method: 'POST',
                 headers: {
                     'Content-Type': 'application/json',
                 },
                 body: JSON.stringify(payload)
            }
        );

        return response;
    }

    request(url, data) {
        const URL_PROPERTIES = TRACKED_URLS[url];
        const PAYLOAD_PROPERTIES = Object.keys(data);

        const arePropertiesValid = URL_PROPERTIES
            && URL_PROPERTIES.every(property => (
                PAYLOAD_PROPERTIES.includes(property)
            ));

        if (!arePropertiesValid) return false;

        this.track(data);
    }
} 
```

非常简单，`request`方法只是验证所有被跟踪的元素是否传递了正确的属性，它充当了一个集中的过滤器和一个集中的请求跟踪字典，这种方法非常简单，并且可伸缩性很好，因为您将所有被跟踪的 URL 放在一个地方，这允许您根据需要快速添加和删除。

如开始所述，本文的目的是展示每个跟踪实现的好的和坏的部分，以便您可以决定哪一个对您和您的团队更好。

就这些了，我希望你喜欢它——如果你喜欢，记得你可以和你的朋友分享，或者点击社交链接在 reddit 或 twitter 上发表评论。

下一集再见！