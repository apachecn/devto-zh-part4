# 使用“Xrm.WebApi”执行 CRUD 操作

> 原文：<https://dev.to/mehmetseckin/perform-crud-operations-using-xrm-webapi-3a0>

# 使用`Xrm.WebApi`执行 CRUD 操作

我最近遇到了一个挑战，我需要对一堆记录执行一些 CRUD 操作，我想知道是否可以使用客户端 API `Xrm.WebApi.online.executeMultiple`来实现这一点。

这里是我想出的例子，希望这能节省一些时间！：

> 更新:这些例子现在在 PowerApps 文档中，看看吧:[执行 CRUD 操作](https://docs.microsoft.com/en-us/powerapps/developer/model-driven-apps/clientapi/reference/xrm-webapi/online/execute#perform-crud-operations)。

#### 创建记录

下面的示例演示了如何执行创建操作。

```
var Sdk = window.Sdk || {};
/**
 * Request to execute a create operation
 */
Sdk.CreateRequest = function (entityName, payload) {
    this.etn = entityName;
    this.payload = payload;
    this.getMetadata = function () {
        return {
            boundParameter: null,
            parameterTypes: {},
            operationType: 2, // This is a CRUD operation. Use '0' for actions and '1' for functions
            operationName: "Create",
        };
    };
};
// Construct a request object from the metadata
var payload = {};
payload["name"] = "Fabrikam Inc.";
var createRequest = new Sdk.CreateRequest("account", payload);
// Use the request object to execute the function
Xrm.WebApi.online.execute(createRequest).then(
    function (result) {
        if (result.ok) {
            console.log("Status: %s %s", result.status, result.statusText);
            // perform other operations as required;
        }
    },
    function (error) {
        console.log(error.message);
        // handle error conditions
    }
); 
```

#### 检索一条记录

下面的示例演示如何执行检索操作。

```
var Sdk = window.Sdk || {};
/**
 * Request to execute a retrieve operation
 */
Sdk.RetrieveRequest = function (entityReference, columns) {
    this.entityReference = entityReference;
    this.columns = columns;
    this.getMetadata = function () {
        return {
            boundParameter: null,
            parameterTypes: {},
            operationType: 2, // This is a CRUD operation. Use '0' for actions and '1' for functions
            operationName: "Retrieve",
        };
    };
};
// Construct a request object from the metadata
var retrieveRequest = new Sdk.RetrieveRequest({ etn: "account", id: "87547d08-b9d0-e911-a826-000d3a43d70a" }, ["name"]);
// Use the request object to execute the function
Xrm.WebApi.online.execute(retrieveRequest).then(
    function (result) {
        if (result.ok) {
            console.log("Status: %s %s", result.status, result.statusText);
            // perform other operations as required;
        }
    },
    function (error) {
        console.log(error.message);
        // handle error conditions
    }
); 
```

#### 更新记录

下面的示例演示了如何执行更新操作。

```
var Sdk = window.Sdk || {};
/**
 * Request to execute an update operation
 */
Sdk.UpdateRequest = function (entityName, entityId, payload) {
    this.etn = entityName;
    this.id = entityId;
    this.payload = payload;
    this.getMetadata = function () {
        return {
            boundParameter: null,
            parameterTypes: {},
            operationType: 2, // This is a CRUD operation. Use '0' for actions and '1' for functions
            operationName: "Update",
        };
    };
};
// Construct a request object from the metadata
var payload = {};
payload["name"] = "AdventureWorks Inc.";
var updateRequest = new Sdk.UpdateRequest("account", "87547d08-b9d0-e911-a826-000d3a43d70a", payload);
// Use the request object to execute the function
Xrm.WebApi.online.execute(updateRequest).then(
    function (result) {
        if (result.ok) {
            console.log("Status: %s %s", result.status, result.statusText);
            // perform other operations as required;
        }
    },
    function (error) {
        console.log(error.message);
        // handle error conditions
    }
); 
```

#### [删除一条记录](#delete-a-record)

下面的示例演示如何执行删除操作。

```
var Sdk = window.Sdk || {};
/**
 * Request to execute a delete operation
 */
Sdk.DeleteRequest = function (entityReference) {
    this.entityReference = entityReference;
    this.getMetadata = function () {
        return {
            boundParameter: null,
            parameterTypes: {},
            operationType: 2, // This is a CRUD operation. Use '0' for actions and '1' for functions
            operationName: "Delete",
        };
    };
};
// Construct a request object from the metadata
var deleteRequest = new Sdk.DeleteRequest({ entityType: "account", id: "87547d08-b9d0-e911-a826-000d3a43d70a" });
// Use the request object to execute the function
Xrm.WebApi.online.execute(deleteRequest).then(
    function (result) {
        if (result.ok) {
            console.log("Status: %s %s", result.status, result.statusText);
            // perform other operations as required;
        }
    },
    function (error) {
        console.log(error.message);
        // handle error conditions
    }
); 
```