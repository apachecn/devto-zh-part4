# 在后台运行 Google Apps 脚本函数，第 2 部分

> 原文：<https://dev.to/bugmagnet/running-google-apps-script-functions-in-the-background-part-2-4mla>

在第 1 部分中，我提到了比每分钟运行一次更频繁的可能性。下面是代码，还有一些讨论。

```
function onTimer() {
    // create four timers for 15, 30, 45 and 60 seconds hence
    const scpt = new ScptProps();

    const lock = LockService.getScriptLock();
    lock.waitLock(30000);

    if (null === scpt.get("seconds15")) {
        const seconds15 = ScriptApp.newTrigger("ProcessFunctionQueue")
            .timeBased()
            .after(15 * 1000)
            .create();
        scpt.set("seconds15", seconds15.getUniqueId());
    }

    if (null === scpt.get("seconds30")) {
        const seconds30 = ScriptApp.newTrigger("ProcessFunctionQueue")
            .timeBased()
            .after(30 * 1000)
            .create();
        scpt.set("seconds30", seconds30.getUniqueId());
    }

    if (null === scpt.get("seconds45")) {
        const seconds45 = ScriptApp.newTrigger("ProcessFunctionQueue")
            .timeBased()
            .after(45 * 1000)
            .create();
        scpt.set("seconds45", seconds45.getUniqueId());
    }

    if (null === scpt.get("seconds60")) {
        const seconds60 = ScriptApp.newTrigger("ProcessFunctionQueue")
            .timeBased()
            .after(60 * 1000)
            .create();
        scpt.set("seconds60", seconds60.getUniqueId());
    }

    lock.releaseLock();
} 
```

Enter fullscreen mode Exit fullscreen mode

所以有了更新的`onTimer`，它通过依赖于时间的触发器每分钟运行一次。

在这里，我们创建了四个`installable triggers`，并将它们的 uniqueIds 存储在脚本属性中。这是因为可安装触发器是一种有限的资源。即使他们已经完成了他们被创造出来要做的事情，他们也不会从存在中消失。相反，它们会一直存在，直到被移除。在`ProcessFunctionQueue`函数中，调用一个函数来删除已完成的触发器。

我不完全确定那些东西。我昨天才读到它，但它就在那里 [JIC](https://www.internetslang.com/JIC-meaning-definition.asp) 。

```
function ProcessFunctionQueue(json: any) {
    ClearUsedTrigger(json.triggerUid.toString())
    const scpt = new ScptProps();
    let funcQueueTxt = scpt.get("FUNCTION_QUEUE");
    if (funcQueueTxt === "[null]" || funcQueueTxt === null) {
        funcQueueTxt = "[]";
    }
    const functionQueueJsn = JSON.parse(funcQueueTxt);
    if (functionQueueJsn.length > 0) {
        const functionQueueItm = functionQueueJsn.pop();
        scpt.set("FUNCTION_QUEUE", JSON.stringify(functionQueueJsn));
        eval(functionQueueItm + "()");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当可安装触发器执行时，它将一个 JSON blob 传递给包含 triggerUid 的被调用函数。这个 id 被传递给`ClearUsedTrigger`函数。然后从 FUNCTION_QUEUE 中弹出一个函数名并进行求值。

这是我对锁和隐藏更新感到疑惑的一点。我希望有人能尽早启发我。

```
function ClearUsedTrigger(id: string) {
    const scpt = new ScptProps();
    ScriptApp.getProjectTriggers().forEach(trigger => {
        if (trigger.getEventType() == ScriptApp.EventType.CLOCK) {
            if (trigger.getUniqueId() === id) {
                ScriptApp.deleteTrigger(trigger);
                ["seconds15", "seconds30", "seconds45", "seconds60"].forEach(itm => {
                    if (scpt.get(itm) === id) {
                        scpt.forget(itm);
                    }
                });
            }
        }
    });
} 
```

Enter fullscreen mode Exit fullscreen mode

这个小魔术遍历项目触发器，找到`CLOCK`事件，并找到哪个`seconds*`脚本属性匹配 id 和那个属性的`forgets`。

`AddToFunctionQueue`保持不变

```
function AddToFunctionQueue(fn: string) {
    const scpt = new ScptProps();
    let funcQueueTxt = scpt.get("FUNCTION_QUEUE");
    if (funcQueueTxt === null || funcQueueTxt === "[null]") {
        funcQueueTxt = "[]";
    }
    let funcQueueJsn = JSON.parse(funcQueueTxt);
    if (funcQueueJsn.indexOf(fn) === -1) {
        funcQueueJsn.unshift(fn);
    }
    scpt.set("FUNCTION_QUEUE", JSON.stringify(funcQueueJsn));
} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我想知道这是否也是锁有用的地方。

一句话:我仍在试图理解 Google Apps 脚本。有些事情很明显，有些不明显。如果你最终改编了上面的任何东西，请告诉我。