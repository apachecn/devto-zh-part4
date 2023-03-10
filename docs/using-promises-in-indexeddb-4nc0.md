# 在索引中使用承诺 b

> 原文：<https://dev.to/andyhaskell/using-promises-in-indexeddb-4nc0>

这是我的 IndexedDB 系列教程的第 3 部分。你可以在这里找到第一部分[，在这里](https://dev.to/andyhaskell/build-a-basic-web-app-with-indexeddb-38ef)找到第二部分[。GitHub 上这个教程的代码是](https://dev.to/andyhaskell/testing-your-indexeddb-code-with-jest-2o17)[这里是](https://github.com/andyhaskell/indexeddb-tutorial/tree/master/3-promises-tutorial)。

在我的上一篇 IndexedDB 教程中，我们重构了便笺存储上的 IndexedDB 函数，以接受回调，这样我们就可以在自动化测试中使用它们。我们可以使用这些回调来保证我们的 IndexedDB 操作以连续的顺序运行。但是在回调风格下，我们的 Jest 测试只做了四个 IndexedDB 动作，结果看起来像这样:

```
test('we can store and retrieve sticky notes', function(done) {
  setupDB('FORWARD_TEST', function() {
    addStickyNote('SLOTHS', function() {
      addStickyNote('RULE!', function() {
        // Now that our sticky notes are both added, we retrieve them from
        // IndexedDB and check that we got them back in the right order.
        getNotes(reverseOrder=false, function(notes) {
          expect(notes).toHaveLength(2);
          expect(notes[0].text).toBe('SLOTHS');
          expect(notes[1].text).toBe('RULE!');
          done();
        });
      });
    });
  });
}); 
```

它确实起了作用，但是随着你写更多复杂的测试，甚至更多的索引数据库操作，我们得到的回调金字塔会变得更大，这意味着阅读和维护你的代码的人会有更多的认知负荷。

如果不是让每个 IndexedDB 函数都是最后一个函数的回调，而是让代码看起来更像是按顺序发生的动作，那就太好了:

```
test('we can store and retrieve sticky notes', function(done) {
  setupDB('FORWARD_TEST');
  addStickyNote('SLOTHS');
  addStickyNote('RULE!');
  let notes = getNotes(reverseOrder=false);

  // Check that we got back the sticky notes we exepcted

  done();
}); 
```

一种方法是让我们的 IndexedDB 函数使用**承诺**而不是回调链接在一起。虽然有办法清理基于回调的代码，但我个人发现基于承诺的 IndexedDB 代码更容易推理，这也是我在自己的 IndexedDB 函数中使用它的原因。因此，在本教程中，我将展示如何承诺基于回调的 IndexedDB 代码。

本教程假设您对 JavaScript 中的承诺有所了解。你可以在 Mozilla Developer Network 的本教程中阅读关于承诺的内容。

## 承诺概述

像回调一样，承诺也是处理异步动作的一种方式，它告诉 JavaScript 在动作完成后你希望你的代码做什么，而不会阻塞 JavaScript 运行时的线程。

承诺，而不是将回调传递给异步函数，在它完成后运行，就像你在 jQuery :
中下载数据

```
$.get('sloth.txt', function(data) {
  console.log(data);
});
console.log(`This code still runs while we're waiting for our sloth`); 
```

您可以创建一个 Promise 对象，并将您的回调传递给它的`.then`方法，就像在 fetch API 中一样:

[![Lola the Micropanda (an adorable black and white Havanese pupper) with a tennis ball that she fetched but is definitely not bringing back](img/5ddef8a2315fbf2df8a92e7e4f28335d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yD2lILHk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ezv4x1gz0eqn8ozpt94.jpg)

我是说[这个 fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) ！

```
fetch('sloth.txt').then(function(res) {
  console.log(res.text());
}) 
```

一旦下载完成，就会运行 fetch API promise 的`.then`方法中的回调，就像运行传递给`$.get()`的回调一样。所以这是一个相似的模式，但是承诺的一个优点是你可以**链接**异步函数一起返回承诺，就像这样:

```
fetch('/my-profile-data').
  then(function(res) {
    // Get the URL of the user's profile picture based on what's in the data we
    // got with our first fetch call, and then run fetch on that URL. We
    // return a promise for when that fetch completes, so this promise can be
    // chained with the callback below
    let profilePicURL = res.json()["profilePicURL"]
    return fetch(profilePicURL);
  }).then(function(res) {
    console.log(res.text());
  }); 
```

这意味着在测试中，我们可以让我们的代码看起来像这样，更清楚地表明我们的函数是按顺序运行的:

```
setupDB().
  then(() => addStickyNote('SLOTHS').
  then(() => addStickyNote('RULE!')).
  then(() => getNotes(reverseOrder=false)).
  then((notes) => { /* Here we run assertions on the notes we get back */ }); 
```

因此，为了让我们的 IndexedDB 函数使用承诺，我们需要**让每个函数返回一个`Promise`对象**，以便下一个 IndexedDB 动作可以在承诺的`.then`中运行。如果我们这样做，我们将能够把我们所有的 IndexedDB 操作链接在一起。

## 允诺 setupDB

第一步是从许诺`setupDB`开始。在回调版本的`setupDB`中，我们有设置数据库和创建对象存储的所有代码。代码看起来像这样:

```
function setupDB(namespace, callback) {
  if (namespace != dbNamespace) {
    db = null;
  }
  dbNamespace = namespace;

  // If setupDB has already been run and the database was set up, no need to
  // open the database again; just run our callback and return!
  if (db) {
    callback();
    return;
  }

  let dbName = namespace == '' ? 'myDatabase' : 'myDatabase_' + namespace;
  let dbReq = indexedDB.open(dbName, 2);

  // Fires when the version of the database goes up, or the database is created
  // for the first time
  dbReq.onupgradeneeded = function(event) {
    db = event.target.result;

    // Create an object store named notes, or retrieve it if it already exists.
    // Object stores in databases are where data are stored.
    let notes;
    if (!db.objectStoreNames.contains('notes')) {
      notes = db.createObjectStore('notes', {autoIncrement: true});
    } else {
      notes = dbReq.transaction.objectStore('notes');
    }
  }

  // Fires once the database is opened (and onupgradeneeded completes, if
  // onupgradeneeded was called)
  dbReq.onsuccess = function(event) {
    // Set the db variable to our database so we can use it!
    db = event.target.result;
    callback();
  }

  // Fires when we can't open the database
  dbReq.onerror = function(event) {
    alert('error opening database ' + event.target.errorCode);
  }
} 
```

正如我们在上一个教程中看到的，这个动作的“结束”是当`dbReq.onsuccess`触发，运行回调函数，或者它的`onerror`触发，产生一个`alert`弹出窗口。这个想法是，一旦我们得到一个触发`onsuccess`处理程序的事件，这意味着`db`变量被设置，我们的数据库被创建。

要将这个基于回调的 IndexedDB 函数转换为基于承诺的函数，我们需要遵循这个模式，您可以在 [Commit 1](https://github.com/andyhaskell/indexeddb-tutorial/commit/8d116722e0f8ceb9656c12ccf76cb987c6a9212f) 中找到完整的代码更改:

**第一步**:将`setupDB`的整体包装在一个匿名函数中，我们将这个匿名函数传递给 [`Promise`构造函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。

```
function setupDB(namespace) {
  return Promise((resolve, reject) => {
    if (namespace != dbNamespace) {
      db = null;
    }
    dbNamespace = namespace;

    // ...
  });
} 
```

这样，所有的主代码仍将运行，但现在`setupDB`返回一个`Promise`对象，而不是什么都不返回，并在完成时运行回调。

**步骤 2** :用对`resolve()`的调用替换对我们请求的回调的所有调用。这将是两个地方:当变量`db`已经设置时，if 语句中的回调:

```
 if (db) {
-    callback(); +    resolve();
    return;
  } 
```

以及对`dbReq.onsuccess`的回调，它在数据库打开时运行。

```
 dbReq.onsuccess = function(event) {
    // Set the db variable to our database so we can use it!
    db = event.target.result;
-    callback(); +    resolve();
  } 
```

我们传递给 promise 构造函数的函数上的`resolve`和`reject`参数用于指示异步操作何时完成。举个例子，

`setupDB().then(callback);`

意味着如果我们的 IndexedDB 操作成功，那么我们**解析**，然后我们运行回调来执行承诺的`.then`中的下一个操作。

**步骤 3** :用对`reject()` :
的调用替换处理我们的 IndexedDB 请求/事务的`onerror`和`onabort`方法的代码

```
 dbReq.onerror = function(event) {
-      alert('error opening database ' + 'event.target.errorCode'); +      reject(`error opening database ${event.target.errorCode}`);
    } 
```

这意味着如果我们在运行数据库请求时出错，那么 promise 会拒绝，我们传递给 promise 的`catch`方法的回调会运行。例如在代码中:

`setupDB().then(callback).catch((err) => { alert(err); })`

如果我们的 IndexedDB 事务成功，则`setupDB`运行其`then`回调，如果失败，则运行其`catch`回调。

**第四步**:由于我们改变了 setupDB 的函数签名，现在任何调用`setupDB(callback)`的地方都需要改成`setupDB.then(callback)`。

在我们的代码库中，这意味着在`index.html`中，当我们运行 setupDB，然后获取并显示我们的注释时，我们将运行:

```
 <script type="text/javascript">
-      setupDB(getAndDisplayNotes); +      setupDB('').then(getAndDisplayNotes);
    </script> 
```

现在我们有了一个 promised`setupDB`，所以如果我们想建立数据库，然后放入一个便笺条，我们将运行如下代码:

`setupDB('').then(() => addStickyNote('SLOTHS')`

很好，但是在我们的测试中，我们在数据库中添加了不止一个便笺条。这意味着在我们的测试中，我们希望在一个承诺链中链接多个对`addStickyNote`的调用。因此，要做到这一点，`addStickyNote`将需要在这之后返回一个承诺。

## 许诺添加 StickyNote

将我们的 addStickyNote 函数转换为 promise 函数遵循与 setupDB 中相同的模式；我们将函数体包装在`Promise`构造函数中，让它返回一个承诺，我们用对`resolve`的调用替换我们对回调的调用，我们用对`reject`的调用替换我们的错误处理。

对于`addStickyNote`，你可以看到[提交 2](https://github.com/andyhaskell/indexeddb-tutorial/commit/6b00eca2558d6f62e0ac4c75c2fb89f6674d3bf7) 的全部变化，但是我们最感兴趣的代码部分如下:

```
 tx.oncomplete = resolve;
    tx.onerror = function(event) {
      reject(`error storing note ${event.target.errorCode}`);
    } 
```

如你所见，我们的事务的`oncomplete`回调被设置为我们的`resolve`函数，我们的`onerror`回调现在只是带有我们得到的错误的`reject` s。

虽然看起来有点滑稽，`tx.oncomplete = resolve`是完全有效的 JavaScript。`resolve`是一个函数，当向数据库添加便笺的事务完成时，`tx.oncomplete`运行，这意味着`resolve`运行。

现在我们有了`addStickyNote`返回的承诺，我们可以像这样将`addStickyNote`个调用链接在一起:

```
setupDB().
  then(() => addStickyNote('SLOTHS')).
  then(() => addStickyNote('RULE!')); 
```

这个承诺链读着“建立我们的数据库，然后当它准备好了就添加便签‘SLOTHS’，最后一旦准备好了，就添加便签‘RULE！’".`then`回调中的每个函数都是一个返回承诺的函数，这就是为什么每个`addStickyNote`可以与另一个方法链接。

现在，我们的 addStickyNote 方法准备好链接了，在 page.js 中，我们有用户界面函数`submitNote`，我们会像这样用`getAndDisplayNotes`链接它。

```
function submitNote() {
  let message = document.getElementById('newmessage');
-  addStickyNote(message.value, getAndDisplayNotes); +  addStickyNote(message.value).then(getAndDisplayNotes);
  message.value = '';
} 
```

在`submitNote`函数中，`addStickyNote`开始将我们的消息添加到数据库中，当它的承诺兑现时，我们运行 getAndDisplayNotes 来检索我们的便笺并显示它们。当我们的异步代码运行时，我们将 web 应用程序的 textarea 的内容设置为空白。

⚠️:我在这个问题上遇到的一个微妙的陷阱是试图像这样将调用链接在一起:

```
setupDB().
  then(addStickyNote('SLOTHS')).
  then(addStickyNote('RULE!')); 
```

我认为这将是调用这个函数的一种更巧妙的方式，而且看起来它会工作，因为`addStickyNote`返回一个承诺。虽然**那个函数**确实返回了一个承诺，但是`addStickyNote('SLOTHS')`的值不是一个函数，而是承诺对象`addStickyNote` **已经返回了**。

这意味着在`setupDB().then(addStickyNote('SLOTHS!'))`中，每个对`addStickyNote`的调用都必须运行，这样它才能计算出一个值，所以函数开始运行，而我们的`db`变量仍然是`undefined`。

相比之下，`() => addStickyNote('SLOTHS')`是一个返回承诺的函数，而不是承诺本身，所以如果我们将我们的匿名函数传递给承诺的`.then`，该函数将不会启动，直到 setupDB 的承诺解析。

## 许诺获取注释

我们只剩下一个函数需要承诺:`getNotes`，我们再次使用相同的技术，除了这次有一个小的不同。

在`setupDB`和`addStickyNote`中，我们没有检索任何数据，所以没有什么需要传递给下一个函数；我们可以运行`resolve()`，让我们的下一个动作在我们的承诺的`then`回调中运行。然而在`getNotes`中，我们正在检索一些数据，这是我们的便笺，我们想在`then`回调中使用我们的便笺。

要做到这一点(你可以在[提交 3](https://github.com/andyhaskell/indexeddb-tutorial/commit/c07cd26abc6c21bfa8e470ab3e38c6ce4b7da653) 中看到所有的改变)，就像之前一样，我们在之前运行`callback`的地方运行`resolve`。所以我们的`onsuccess`回调现在看起来像这样:

```
 let allNotes = [];
    req.onsuccess = function(event) {
      let cursor = event.target.result;

      if (cursor != null) {
        // If the cursor isn't null, we got an IndexedDB item. Add it to the
        // note array and have the cursor continue!
        allNotes.push(cursor.value);
        cursor.continue();
      } else {
        // If we have a null cursor, it means we've gotten all the items in
        // the store, so resolve with those notes!
-        callback(allNotes); +        resolve(allNotes);
      }
    } 
```

像在我们的其他函数中一样，我们请求的`onerror`回调现在只是调用`reject`而不是调用`alert`。

```
req.onerror = function(event) { -   alert('error in cursor request ' + event.target.errorCode); +   reject(`error in cursor request ${event.target.errorCode}`); } 
```

这意味着经过我们的修改，`getNotes`现在像我们的其他 IndexedDB 函数一样返回一个承诺。然而，这不是一个没有数据就能兑现的承诺，这是一个用一堆便利贴来兑现的承诺！

这意味着如果我们对`getNotes`的调用有一个`then`回调，而不是给`then`一个什么都不接受的函数，我们可以给`then`一个接受一组便笺的函数。这就是我们在`getAndDisplayNotes`身上要做的！

```
function getAndDisplayNotes() { -   getNotes(reverseOrder, displayNotes); +   getNotes(reverseOrder).then((notes) => { displayNotes(notes) }); } 
```

现在，当我们运行`getNotes`时，它用我们的便笺列表进行解析，所以这些便笺被传递到我们的回调函数中，回调函数用它们运行`displayNotes`。

太棒了。我们所有直接接触 IndexedDB 的函数现在都返回承诺，所以下一站:我们的测试覆盖率！

## 承诺我们的索引数据库测试

正如我们在 page.js 和 index.html 中看到的，当我们想要连续运行基于承诺的 IndexedDB 动作时，我们让每个动作在最后一个动作的`then`中运行。所以现在，代替 Jest 测试中的回调金字塔，我们将让测试运行这样的承诺链:

```
test('we can store and retrieve sticky notes!', function() {
  return setupDB('FORWARD_TEST').
    then(() => addStickyNote('SLOTHS')).
    then(() => addStickyNote('RULE!')).
    then(() => getNotes(reverseOrder=false)).
    then((notes) => {
      // Assertions on the notes we retrieved
      expect(notes).toHaveLength(2);
      expect(notes[0].text).toBe('SLOTHS');
      expect(notes[1].text).toBe('RULE!');
    });
}); 
```

在函数的第一行，我们建立了数据库。`setupDB`返回一个承诺，当它解决时，它`then`将便签 SLOTHS 添加到数据库中。`then`一旦承诺兑现，我们就加入便利贴规则！。并且`then`，在承诺链的以下操作中，我们运行`getNotes`，知道我们的两个`addStickyNote` IndexedDB 操作都已完成。

最后，当`getNotes`解析时，`then`回调接收我们检索到的两个便笺，因此我们对它们运行我们的断言。如果他们都成功了，那意味着我们整个测试都通过了，但是如果其中一个失败了，那么测试就失败了。

如您所见，有了这个承诺链，我们不需要在文本编辑器中一直将每个 IndexedDB 操作向右推进几个空格。相反，我们可以把它写得更像是按顺序运行的一系列动作。

顺便说一句，关于我们是如何改变这个函数的，有一点很重要，那就是我们传递给`test`的函数的签名已经稍微改变了:

```
- test('we can store and retrieve sticky notes!', function(done) { + test('we can store and retrieve sticky notes!', function() { 
```

记住，我们传递的函数与异步代码一起工作，所以我们需要一种方法让代码告诉 Jest 我们已经完成了测试。所以`done`参数解决了这个问题，它是一个函数，我们在运行完断言后调用它，表示测试已经完成。但是为什么我们的新承诺链样式不需要那个`done`参数呢？让我们仔细看看测试中的第一行:

```
test('we can store and retrieve sticky notes!', function() {
  return setupDB('FORWARD_TEST'). 
```

在我们的测试中，我们不只是**运行**我们的承诺链，我们**返回**它！在 Jest 中，如果你的测试的回调函数接受一个返回承诺的函数，那么 Jest 就知道测试完成了[当那个承诺解决了](https://jestjs.io/docs/en/asynchronous.html#promises)！所以这有点像 Jest 说的

`runOurTest().then(runOurNextTest)`

由于以逆序检索便笺的测试看起来是一样的，所以我不会展示该测试的承诺版本，但是您可以在 [Commit 4](https://github.com/andyhaskell/indexeddb-tutorial/commit/692f8657f5f6055f9e7583b83e9dd92f252b0566) 中看到它。如果您运行测试，您将会看到:

[![Command line showing our IndexedDB tests running with our promise chain. They both return promises, so they pass!](img/42ae509f8311059862fbb3f9aa14db03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--15QNg2UL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zmhwnh7so9acp40m02nm.png)

测试通过了！现在让我们对我们的测试再做一个改变，使用更新的`async/await`关键字！

## 像酷孩子一样运行异步/等待！

[`async/await`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) 给出了多一种处理异步代码的方式。而不是通过使用`.then`回调来按顺序运行每个动作，比如:

```
doAsynchronousAction().
  then(doAnotherAsynchronousAction).
  then(finallyRunThisCode); 
```

async/await 允许我们编写一个接一个运行的动作，就好像函数根本不是异步的一样！

```
await doAsynchronousAction();
await doAnotherAsynchronousAction();
finallyRunThisCode(); 
```

在`await` ed 承诺完成之前，函数中`await`之后的代码不会运行。对我来说，我发现这是编写异步动作序列的一种更自然的方式，因为我们并不试图同时运行它们中的任何一个。

所以在[提交 5](https://github.com/andyhaskell/indexeddb-tutorial/commit/c3fc85260526c65b82d19fb85c4d84269820b3c9) 中引入了 async/await 之后，我们的第一个函数看起来应该是:

```
test('we can store and retrieve sticky notes!', function() {
  await setupDB('FORWARD_TEST');
  await addStickyNote('SLOTHS');
  await addStickyNote('RULE!');

  let notes = await getNotes(reverseOrder=false);
  expect(notes).toHaveLength(2);
  expect(notes[0].text).toBe('SLOTHS');
  expect(notes[1].text).toBe('RULE!');
}); 
```

我们`await`设置 upDB 完成，然后我们开始将便笺条 SLOTHS 添加到我们的数据库，`await`完成，当完成时，我们`await`将便笺条规则添加到我们的数据库。

用`getNotes`检索我们的便笺变得更加有趣。由于`getNotes`返回一个解析了一些数据的承诺，我们可以使用`await`将 getNotes 解析的数据赋给一个变量。

```
let notes = await getNotes(reverseOrder=false); 
```

这一行意味着在我们检索了我们的便笺列表后，那些用`getNotes`解决的便笺现在在`notes`变量中。这意味着在`await`下面，我们可以在便签上运行我们的断言。

await 关键字现在已经抽象出 setupDB、addStickyNote、getNotes 和我们的断言应该是回调的想法。如果我们运行这个测试，我们会得到:

[![Command line showing that our tests failed. The error we got is "Can not use keyword 'await' outside an async function"](img/d308609ee0ec4db8aac1ec361f11b130.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KBD1H35q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j95ucfc01tau2vl4iaaj.png)

不幸的是，我们有一个错误；`await`关键字不能在常规函数中使用。原因是因为在常规函数中，等待每个动作完成会阻塞 JavaScript 运行时的单线程。幸运的是，让它工作只需要一行代码:

```
- test('we can store and retrieve sticky notes!', function() { + test('we can store and retrieve sticky notes!', async function() { 
```

现在，我们给了一个 T2，而不是普通的 T1。由于异步函数隐式返回一个承诺，这意味着我们在测试中仍然不需要`done`参数。

再次运行这个测试，您将得到:

[![Command line showing that our tests now pass again!](img/514cb8a21ae82149b96bc34fdcc9f130.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mM9BODhJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/29lqpqicwc6jehlrfb95.png)

通过测试！现在您已经看到了如何将基于回调的 IndexdedDB 函数转换为基于承诺的函数，如何将它们链接在一起，以及如何编写使用承诺链的测试。我希望这对你设计 web 应用程序的 IndexedDB 代码有所帮助。直到下一次，

[![Baby two-toed sloth sitting in a dark green ball like a tire swing](img/e0f10b06a3b976c70234d1ede40e4e4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zksYMc6n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6ouh8hg0o82g0vve1kn9.jpg)

### 偷懒！

[树懒图片](https://www.flickr.com/photos/ekilby/16652833928)由 Eric Kilby 拍摄，并获得了 [CC-BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/) 的许可。