# 复杂系统的简单用户权限模型

> 原文：<https://dev.to/dillonsadofsky/a-simple-user-permission-system-for-sophisticated-systems-2lan>

# 简介

一个朋友让我详细说明我们这些年来为我维护的一些应用程序开发的权限系统。这些系统是 ERP 解决方案，其中管理人员需要管理用户对某些功能和实体的访问。这些应用程序已经持续开发了 15 年以上，并且已经在一些超过 10 年的客户中实现。我认为我们所达到的对于用户来说是相对容易理解和管理的，对于程序员来说也是相对容易利用的。

该系统可以在任何前端语言中使用，但我们将大多数业务逻辑存储在 MySQL 中，以便 node/graphql 和 c++前端可以获得完全相同的行为。当然，如果你有一个后端 API，你可以在那里实现它并获得同样的好处。我的例子都将在 MySQL。

# 目标:

*   允许用户被分配到组(用户可定义)
*   为所有可能的权限创建一个表(由开发人员定义)
*   允许权限访问级别直接授予用户或组(用户可定义)
*   实现在特定级别检查权限的功能(可以通过后端代码检查)
*   当用户可以从他们所属的任意数量的组中拥有用户级访问和组级访问时，正确处理授予访问权限(用户可以授予权限，过程需要正确处理)。

# 术语:

*   站点:许多系统都包含了一个想法，即一个公司可以跨越许多工厂/商店/站点，用户可以访问其中的一个或多个。我可能会将 store/storeid 与 site/siteid 互换使用。管理员可以授权用户访问任意数量的网站。这控制了他们可以登录的位置，以及在给定他们的权限和站点所有权的情况下，他们可以修改哪些实体和关系。
*   **访问级别:**用户或组可以被授予无(无访问权限)、站点(用户被授予所有站点的访问权限)或全局(访问所有站点拥有的对象以及全局配置)权限。
*   **组:**用户可以加入的东西。组可以表示系统中的职责或角色、职务或其他配置级别。我们让用户定义这些。可以向组授予权限
*   **私有站点:**私有站点是指用户被拒绝访问的站点，除非他们登录到该站点，即使他们通常可以访问该站点。

# 抽象模式(与语言无关):

*   用户帐户:应该有登录信息，但没有权限或网站，这些将在后面定义
*   **site:** 企业运营的地区列表。如果简单的系统相信他们的客户永远不会是多位置的，它们可能会避开这一点。尽管如此，我还是建议这样做，因为在短期内忽略它比在后期添加更难。
*   **用户站点:**多对多表，用于授予用户帐户对站点的访问权限
*   **权限:**用简短的用户可见描述组定义每个权限:定义用户可以加入的组以及可以被授予权限的组
*   **用户组:**多对多表，用于用户在组中的成员资格
*   **userpermission:** 在这里，用户可以被授予全局权限、站点级别的权限，或者根本不被授予权限
*   **grouppermission:** 在这里，组可以被授予全局、站点级别的权限，或者根本不被授予权限

我将把大多数这些表的实现留给用户，因为它们大多是特定于应用程序的，但是我想展示几个表 MySQL 实现来讨论建议。

# 访问:

这个系统通常归结为提出一个问题:对于给定的用户和给定的权限，他们拥有什么级别的访问权限。答案可能是无、站点级访问或全局访问。如果用户具有全局访问权限，则应该允许他们通过，而对于 None 级别则相反。如果他们具有站点级访问权限，这将取决于他们有权访问的站点(通过用户站点)以及拥有用户想要修改或创建的文档/对象的站点。

在检查修改实体或关系的访问权限时，应该考虑拥有该对象的站点。通常，这是用户在其会话中登录的同一站点，但是根据您的系统，他们可能能够查看/修改来自其他站点的文档和对象。在这种情况下，所有者站点是用户需要访问以执行操作的站点。例如，如果他们加载在另一个站点创建的文档，并且他们没有该文档的用户站点条目，那么他们将没有访问权限，除非他们被授予“全局”权限。此外，如果他们想在该站点创建文档，他们也需要访问权限。

在此系统中，如果用户没有给定 permissionid 的 userpermission 行，并且不属于任何具有给定 permissionid 的 grouppermission 行的组，则假定他们具有 None 访问级别(拒绝访问)。

## 组合来自多个来源的访问:

从这个设计中可以明显看出，用户可以直接被授予权限，他们可以是任意数量的组的一部分，每个组都有自己的访问级别。这种设计在联合模型上运行，本质上授予用户“最慷慨”的访问级别，他们可以从他们所属的任何源访问。我相信这通常也是基于角色的权限在现代操作系统中的工作方式。

因此，如果用户没有权限 SALES_ORDERS_CAN_EDIT 的 userpermission 行，并且是在地点级别拥有该权限的“销售人员”组的一部分，但也是在全局级别拥有该权限的“销售经理”组的一部分，则他们实际上拥有权限 SALES_ORDERS_CAN_EDIT 的全局访问级别。

稍后，我将展示一个示例 MySQL 存储函数，在给定一个 useraccountid、一个权限的代码名称和一个站点的情况下，它可以返回一个关于是否应该授予用户访问权限的布尔答案。

# MySQL 实现示例:

### 用户账号:

```
CREATE TABLE `useraccount` (
  `useraccountid` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'Unique ID',
  `name` varchar(20) NOT NULL DEFAULT '' COMMENT 'User''s login',
  `passwordkey` varbinary(64) NOT NULL COMMENT 'The result of secure password encoding of users password',
  `passwordsalt` binary(32) DEFAULT NULL COMMENT 'The unique salt for this user',
  `resettoken` varchar(10) DEFAULT NULL COMMENT 'If the user begins a password reset, this is a one-use token for that',
  `resetexpiration` datetime DEFAULT NULL COMMENT 'If the user begins a password reset, this is when the token expires',
  `status` enum('Pending Activation','Active','Locked','Deactivated') NOT NULL DEFAULT 'Pending Activation' COMMENT 'Status of the user account and whether it is usable',
  `locknotes` varchar(100) DEFAULT NULL COMMENT 'Used to tell the user why they''re locked out',
  `workemail` varchar(320) NOT NULL DEFAULT '' COMMENT 'Contact email address',
  `recoveryemail` varchar(320) DEFAULT NULL COMMENT 'A password recovery email. Must be set by user, not admin for highest security',
  `firstname` varchar(100) NOT NULL DEFAULT '' COMMENT 'User''s first name',
  `lastname` varchar(100) NOT NULL DEFAULT '' COMMENT 'User''s last name',
  PRIMARY KEY (`useraccountid`),
  UNIQUE KEY `username` (`name`)
) ENGINE=InnoDB; 
```

在 useraccount 表中，确保使用真实、安全的密码算法，而不是基本/廉价的哈希算法(MD5，SHA)。例如 bcrypt、scrypt 和 PBKDF2。还要确保您的 salts 是按用户生成的，而不是全局定义的。你可以在其他地方阅读安全密码系统，以及为什么它们优于(最近被否决的)MySQL 中的内置密码功能或 MD5 等基本哈希算法。确保以二进制格式存储输出字符数组，以减少占用的空间。

此外，如果您的系统进行密码重置，最佳做法是生成一个仅适用于该帐户的定时(限制是可协商的)一次性令牌。

### 权限:

```
CREATE TABLE `permission` (
  `permissionid` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT 'unique identifier',
  `category` varchar(250) NOT NULL DEFAULT '' COMMENT 'a logical grouping of permissions',
  `displayname` varchar(250) NOT NULL DEFAULT '' COMMENT 'a name to show the user',
  `codename` varchar(100) NOT NULL DEFAULT '' COMMENT 'The key used to reference this item in code',
  `description` text COMMENT 'a full description of the permission',
  PRIMARY KEY (`permissionid`),
  UNIQUE KEY `location` (`category`,`displayname`),
  UNIQUE KEY `codename` (`codename`)
) ENGINE=InnoDB; 
```

这个表非常简单，只需定义系统中可以授予的所有权限。类别用于将权限分组到系统的逻辑区域中(供管理员浏览和设置权限)。`codename`很特别，因为它允许一个惟一的后台键来查找权限，而不依赖于 id 在每个系统部署上都是相同的。这种方式的代码可以引用更多面向开发人员的权限名称，如 SALES_ORDERS_CAN_EDIT 或 SALES_ORDERS_CAN_VIEW，而不是 id 1 或 2。

我将跳过连接器表和一些更简单的表，因为您可以毫不费力地找到适合自己的系统实现。

## 访问管理模式:

### 用户权限/组权限:

```
CREATE TABLE `userpermission` (
  `userid` int(10) NOT NULL,
  `permissionid` int(10) NOT NULL,
  `value` enum('None','Site','Global') NOT NULL DEFAULT 'None' COMMENT 'Amount of this permission the user has (none/at his Site/at all Sites)',
  PRIMARY KEY (`userid`,`permissionid`)
) ENGINE=InnoDB;

CREATE TABLE `grouppermission` (
  `groupid` int(11) NOT NULL,
  `permissionid` int(11) NOT NULL,
  `value` enum('None','Site','Global') NOT NULL DEFAULT 'None',
  PRIMARY KEY (`groupid`,`permissionid`)
) ENGINE=InnoDB; 
```

userpermission 用于特定用户应该被授予访问级别的时候，而不是通过他们在特定组中的成员资格。可以设计一个系统，其中没有用户级权限，而只有组权限。在这个系统中，如果用户 admin 希望授予特定用户访问权限，他必须创建一个只有一个成员的组。

grouppermission 将访问级别授予整个组，许多用户可能是该组的一部分。

# 设置权限:

要授予访问权限，最简单的解决方案是:

```
INSERT INTO grouppermission (groupid, permissionid, `value`) VALUES (1, 3, 'Global') ON DUPLICATE KEY UPDATE `value` = VALUES(`value`); 
```

这将给定组的权限级别创建或覆盖到给定的访问级别。我留下一个类似的用户权限查询作为练习。

在内部，我们有一个存储过程来设置用户或组的权限级别，以创建一个数据库级的 API，但您的体验可能会有所不同。

# 检查访问:

因此，大多数开发人员最感兴趣的事情是检查用户是否在给定的级别上拥有执行任务的给定权限。这可以在数据库级别使用用户定义的存储过程来完成，或者系统可以下载当前用户的权限并将其缓存在内存或 redis 等中。为了简单起见，我将直接在 MySQL 中演示。

## 检查访问的伪代码:

以下是关于如何检查用户在给定站点的给定权限的访问级别的独立于语言的伪代码:

```
function: user_has_access
Inputs: user_id, permission_code_name, site_id
Return: TRUE or FALSE
SET permission_id = get_permission_id_with_permission_code(permission_code_name)
SET user_access_level = 'None'

IF (permission_id IS NULL)
   RETURN FALSE

IF (user_permission_table_contains(user_id, permission_id))
   SET user_access_level = user_permission_level(user_id, permission_id)

for each_group_user_is_member_of(user_id)
   SET current_group_access = group_permission_level(group_id, permission_id)

   IF (current_group_access > user_access_level)
       SET user_access_level = current_group_access

IF (user_access_level = 'Global')
   return TRUE
ELSE IF (user_access_level = 'None')
   return FALSE
ELSE
   # The user has site level permission, we need to ensure that they have been granted access to the site in question
   IF (user_site_table_contains(user_id, site_id))
       return TRUE

return FALSE 
```

## MySQL 存储过程示例:

下面是一个在 MySQL 中检查用户在给定站点的权限的示例实现:

```
DROP FUNCTION IF EXISTS `f_check_user_permission`;

CREATE FUNCTION `f_check_user_permission`(nUserID INT, strPermission VARCHAR(100), nSiteID INT) RETURNS tinyint(1)
    READS SQL DATA
    DETERMINISTIC
    COMMENT 'Checks a permission for a particular user at a specific Site'
BEGIN
    DECLARE nPermissionID INT;
    DECLARE nUserAccess INT;
    DECLARE nGroupAccess INT;
    DECLARE strPermissionLevel VARCHAR(50);

    SELECT permissionid INTO nPermissionID FROM permission WHERE codename = strPermission;

    IF (nPermissionID IS NULL) THEN
        RETURN NULL;
    END IF;

    # Access means the user has either:
    # 1. Site permission and this is one of their Sites.
    # 2. Global permission and either (2a. this is one of their Sites or 2b. this is a public Site)
    # Get the ability to access it at a user level
    SELECT COUNT(*) INTO nUserAccess FROM userpermission LEFT JOIN usersite ON (userpermission.userid = usersite.userid 
            AND siteid = nSiteID) 
        LEFT JOIN site ON (site.siteid = nSiteID)
        WHERE userpermission.`value` != 'None' AND userpermission.userid = nUserID AND userpermission.permissionid = nPermissionID
            AND (usersite.siteid IS NOT NULL OR (userpermission.`value` = 'Global' AND site.private = 'False'));
    # As well as if its been granted through any groups
    SELECT COUNT(*) INTO nGroupAccess FROM usergroup LEFT JOIN usersite ON (usergroup.userid = usersite.userid AND siteid = nSiteID) 
        JOIN grouppermission ON (usergroup.groupid = grouppermission.groupid) 
        LEFT JOIN site ON (site.siteid = nSiteID)
        WHERE grouppermission.`value` != 'None' AND usergroup.userid = nUserID AND grouppermission.permissionid = nPermissionID 
            AND (usersite.siteid IS NOT NULL OR (grouppermission.`value` = 'Global' AND site.private = 'False'));

    RETURN IFNULL(nUserAccess, 0) + IFNULL(nGroupAccess, 0) > 0;
END; 
```

#### 示例用法:

```
# Does user 1 have permission to edit sales at site 2
SELECT f_check_permission(1, 'SALES_ORDERS_CAN_EDIT', 2);
# Does user 1 have global permission to void sales orders
SELECT f_check_permission(1, 'SALES_ORDERS_CAN_VOID', NULL); 
```

# 权限应该是什么？

我认为这可能是最依赖于您的特定环境的事情，但我认为这通常是开发人员给自己制造问题的地方。当设计系统和访问范例时，开发人员可能很难确切地知道权限应该包含什么。应该是特定的字段、端点、表格还是屏幕？如果用户没有访问权限，您是显示该区域，但将其灰显(为了便于发现)，完全隐藏，还是在用户试图访问该区域时弹出拒绝访问框？

对于所有这些问题，不幸的答案是，这将取决于。我猜这些年来我可能都做过，这取决于用户和系统的需求。不过总的来说，以下是我的经验法则:

*   创建清晰的权限描述，概述授予/拒绝权限将*做什么*。
*   除非特别敏感，否则不要创建控制小区域(特定字段)的权限。
*   启动常规，仅在必要时创建更具体的权限。如果客户的需求或使用模式决定了用户需要能够做权限控制的某些事情，而不是其余的事情，那么就将其分解或创建“子权限”。通常，与终端用户管理大量特定于字段的权限相比，向应用程序添加新权限更容易，可以在以前的权限(更通用的权限)之外进行检查。
*   考虑权限的类型(见后面的部分)以及它对 UI 和 API 的影响。在哪里检查？只是在 UI 中影响用户体验，在 UI 中以及后端，还是只是后端？根据权限的不同，答案可能会有所不同，良好的用户体验通常需要在前端进行检查，而一个安全的系统需要在后端进行检查。

## 重叠权限

不可避免地，您最终将拥有控制整个区域(例如创建销售)的一般权限“SALES_ORDERS_CAN_CREATE ”,以及控制该区域中的子功能和子角色的更具体的权限“SALES _ ORDERS _ CAN _ ACCEPT _ PAYMENTS”。下面是一些最佳实践，让开发人员更容易知道检查什么权限，让管理员更容易知道授予什么权限:

*   确保这样的权限很容易分辨出它们是相关的。在我们的范式中，这是通过共享的类别和良好的描述。我见过这样的系统，其中这样的许可是现有许可的子许可，并且在一个级别上授予/拒绝沿着树向上或向下级联，但是我发现这比它在实践中的价值更复杂。
*   在代码中，检查影响特定 UI 区域或终结点的所有权限的交集(而不是并集)(用 AND 代替 or)。本质上，在代码中，可以很容易地根据一般权限显示/隐藏整个区域(或只读锁定整个页面)，然后让子区域/按钮/功能检查更具体的权限*和*。如果一个功能/区域检查更一般的和更具体的权限的联合，则不清楚权限何时控制或不控制事情(因为子权限覆盖更一般的权限)。
*   这可能导致这样一种情况，即用户有权接受销售付款，尽管他们没有编辑或查看销售的权限。该用户将无法执行该任务，因为缺少更一般的权限。这可能看起来很奇怪，但是当用户组代表不同/重叠的角色时，这种情况有时会发生(取决于用户所在的组)，通常情况下，管理员会更容易理解为什么用户不能做某些事情(缺少顶级权限),而不是调试为什么他们能够做某些事情，尽管已经缺少定义良好的顶级权限。

## 权限类型:

*   敏感数据权限将完全隐藏软件的某些区域，或者影响视图中可显示的关于对象的字段。这通常是由前端代码检查的权限。
*   基于任务的权限通常控制用户是否可以在您的系统中“创建”或“编辑”日常操作的实体和关系。我发现对于大多数主要领域来说，他们管理着一个总体项目，创建两个权限(一个用于创建新项目，一个用于编辑现有项目)是一个很好的起点。这些最终对用户经理来说很容易理解，因为它们与他们熟悉的软件领域非常吻合。
*   角色权限通常被绑定到特定的用户组，代表不太常见的“管理任务”,这些任务通常更特定于特定的按钮或菜单选项。像作废/删除/批准一般用户做不到的事情。如果用户没有这些权限之一，该按钮或菜单选项通常是不可访问或隐藏的。这些通常仍然与软件和操作中容易被发现的部分非常吻合。
*   交叉权限。我在这里包括这些是为了完整性，但我认为它们是应该避免的事情。从本质上讲，创建一个看似基于角色的权限，其定义会影响软件的许多/所有领域，这是很有诱惑力的。例如“访问高级功能”或“查看销售活动”。在软件系统中，高级功能或销售数据可能包含在许多屏幕上，需要弄清楚它在每个屏幕上的含义。它们并非不可能，但通常很难向用户描述并管理它们。通常，用户希望用户查看客户的销售历史(销售活动)，而不是由商业智能单元生成的销售报告，因此您不希望在您的软件中引入横切关注点。

# 添加新权限:

一个常见的问题是，在产品的生命周期中，当用户接受更新时，会添加需要由他们管理的新权限。由于(如前所述)忽略访问级别意味着用户没有访问权限，新的权限自然会被现有用户拒绝，这可能会导致在更新之前中断之前的职责。

为了解决这个问题，我有一个存储过程，它是更新脚本的一部分，我称之为“复制授予权限”。本质上，新添加的权限通常是以前存在的权限的更具体的部分，或者是一个全新的区域，其使用可以根据现有区域的使用来推断。因此，本质上，当我在权限表中创建新行时(不要忘记给它一个面向用户的好描述)，我通常调用这个存储过程，并向它传递新权限的代码名以及我想从中复制的以前的权限。实质上，该过程将新权限授予拥有以前权限的任何用户或组，这些用户或组具有与以前权限相同的访问级别。此时，用户管理员可以进一步修改这些权限。

# 结论:

希望这能为您在应用程序中创建灵活的权限系统提供一个合理的起点。我知道这并没有以某种方式概念化权限(没有子权限)，但这已经在几个生产 ERP 系统中使用，其中两个已经使用了 10 多年，另一个使用了 5 年以上，我从未发现过我无法处理的情况。

剩下的大部分挑战来自于将权限设计得不要太具体(每个字段)也不要太笼统。在与用户或管理员交谈时，您可能会发现他们希望控制用户访问的级别。在这种情况下，只需要命名和描述权限，使它们变得有用和易于管理。这是非常依赖于应用程序的，所以我不知道我在这个问题上的学习会像这个框架一样适用。