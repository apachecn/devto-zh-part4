# Jasmine 测试教程

> 原文：<https://dev.to/flippedcoding/jasmine-testing-tutorial-2nmk>

测试在 web 开发中越来越突出，尤其是 DevOps 越来越多。随着持续集成和部署成为标准实践，知道如何保持源代码整洁并确保没有疯狂的事情进入 prod 或任何其他环境非常重要。一种方法是自动化测试，尽管您仍然需要编写那些测试。

这是一个关于 TDD 和 BDD 如何在敏捷环境中工作以及如何用 Jasmine 编写单元测试的快速教程。我们将经历使用 TDD 和 BDD 编写一些单元测试的过程，并解释我们为什么要做我们要做的事情，以及我们如何做出做这些事情的决定。

## TDD 和 BDD 的背景

让我们从 TDD 和 BDD 的一点背景开始。你可以在这里得到更深入、更快速的概述: [TDD/BDD 概述](https://flippedcoding.com/2019/05/02/testing-with-tdd-and-bdd/)简而言之，BDD 描述了特性如何工作以及用户应该如何使用它们，TDD 让你在为这些特性编写代码之前编写测试。将这两种方法结合使用会给你带来一些好处。

*   您的团队将理解特性需求来自哪里，以及为什么它们是必要的。

*   你将在你的项目上有广泛的测试覆盖面，这将使你更快地产生更高质量的代码。

*   文档对于代码的任何部分都不是问题，因为任何开发人员都可以查看测试来了解代码应该如何工作。

*   在编写代码之前编写测试可以让您更好地理解您试图解决的问题。

*   对您的代码进行测试使您的 CI/CD 管道更加可靠和一致。

这些只是你从编写测试中得到的一些好处。现在我将介绍在 Jasmine.js 中编写两个测试的过程。同样的过程可以用于使用前端或后端的任何库来编写测试。

## 用户故事

这是我们将用来编写测试的两个用户故事。

**用户故事 1**

作为管理员用户，我可以停用具有我选择的权限的所有用户，这样我就可以同时从系统中删除多个具有相同权限的人，而不是单独删除。

**用户故事 2**

作为帐户用户，我可以启用或禁用双因素身份验证，这样我就可以决定我的帐户信息的安全级别。

这些都不是什么奇特的功能，但是它们都有几个任务。通常下一步是与您的团队会面，将这些用户故事分解成任务，然后您将为这些任务编写代码。让我们做一点。我们可能会省略一些东西，或者对它们进行压缩，以保持简洁。

**用户故事 1 任务**

*   确保用户是管理员
*   基于权限选择筛选所有用户
*   添加将用户作为一个组删除的功能

**用户故事 2 任务**

*   创建一个新模型来更新两个因子设置
*   如果他们不启用双因素身份认证，则添加有关信息安全的警告

这是您可以从这些用户故事中提取的任务的粗略分类。现在我们已经定义了我们的任务，我们可以开始编写测试了。

## 写茉莉的中考

对于这个快速教程，我们将使用 Jasmine.js。它非常容易掌握，并且可以与大多数 JavaScript 框架很好地集成。我喜欢用它来测试角度应用。如果你想知道关于 Jasmine 的细节并了解如何使用它，查看他们的文档:[https://jasmine.github.io/api/edge/global](https://jasmine.github.io/api/edge/global)我们不会为所有的任务编写一个测试，因为那会给你留下一些练习。我们将编写三个测试，这样你可以更好地理解如何编写测试。请记住，我们还没有实现任何代码，所以你可以调用任何你喜欢的东西。

**确保用户是管理员**

```
'use strict';

const testAdminUser = require('./adminUser.js');

describe('make sure user is an admin', () => {
    let dummyUser = {
        username: 'testdummy',
        password: 't3StD4mM7'
    };

    it('check if user is logged in', () => {
        expect(testAdminUser.login(dummyUser).response.success).toBe(true); 
    });

    it('check if user has admin permissions', () => {
        let userPermissions = [];
        userPermissions = testAdminUser.login(dummyUser).response.permissions;

        expect(userPermissions.includes('Administrator')).toBe(true);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

**添加删除用户群组的功能**

```
'use strict';

const testAdminUser = require('./adminUser.js');

describe('functionality to remove users as a group', () => {
    let dummyPermission = 'Contributor';

    it('gets a list of all users marked for deletion', () => {
        expect(testAdminUser.getUsersForDeletion(dummyPermission).response).toBeDefined(); 
    });

    it('remove all users with the targeted permissions', () => {        
        expect(testAdminUser.deleteUsers(dummyUser).response.success).toBe(true);
    });

    it('check that the users have actually been removed', () => {
        let listOfUsers = testAdminUser.getUsersForDeletion(dummyPermission).response;

        expect(listOfUsers.length).toBeLessThan(1);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

**制作一个新的模态来更新两个因子设置**

```
'use strict';

const testAdminUser = require('./adminUser.js');

describe('make a new modal to update two factor settings', () => {
    let dummyUser = {
        username: 'testdummy',
        password: 't3StD4mM7'
    };

    it('check if new modal has been created', () => {
        expect('#two-factor-modal').toExist();
    });

    it('check that two factor settings have loaded with the selected value', () => {        
        expect(testAdminUser.getTwoFactorSettings(dummyUser).response).not.toBeNull();
    });

    it('check that changing the two factor setting updates the value for the user', () => {
        let selectedValue = false;
        let isUpdateSuccessful = testAdminUser.updateTwoFactorSettings(dummyUser).response.success;

        if (isUpdateSuccessful) {
            expect(testAdminUser.getTwoFactorSettings(dummyUser).response).toBe(selectedValue);
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些是一些如何使用 Jasmine 编写测试的例子，但是大多数测试库都遵循类似的流程。您将描述您试图测试的内容，然后编写一些虚拟条件来对您计划实现的代码执行这些测试。这并不是为这些任务编写测试的唯一方法，所以如果你觉得有改进的空间，就自己编写测试吧！无论如何，这是你获得最大利益的地方。

编写测试并不难，只是需要时间来适应。当你通常得到一个任务并直接进入代码时，首先编写测试似乎很复杂。许多开发人员马上会问，如何为尚不存在的代码编写测试。那不是看待它的正确方式。您不必编写测试来检查代码的实现。您正在编写测试来检查某个特性是否按照业务方面要求的方式工作。

这就是为什么你可以在写代码之前写一个测试。一旦你写好了测试，你基本上就有了你需要写的代码的大纲。所以您编写代码来通过特性测试。然后你重构它，使它更有效。这就是 TDD/BDD 过程的工作方式，当你掌握了它的窍门时，它会变得非常酷。

你认为为什么开发人员在是否写测试的问题上如此分裂？我知道有些从事软件工作 20 多年的人从来没有编写过测试，也有些人不会想到在没有测试的情况下编写代码。为什么会有这样的分野？是因为整个测试相对较新，还是因为商业运作方式或其他原因？我真的很好奇！

* * *

嘿！你应该在推特上关注我，因为原因:[https://twitter.com/FlippedCoding](https://twitter.com/FlippedCoding)