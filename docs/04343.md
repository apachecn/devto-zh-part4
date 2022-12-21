# 使用 Firebase 模拟器在本地测试 Firestore

> 原文：<https://dev.to/clarity89/testing-firestore-locally-with-firebase-emulators-2p1j>

这篇文章最初发表在我的[个人博客](https://claritydev.net/blog/testing-firestore-locally-with-firebase-emulators)上。

[Cloud Firestore](https://firebase.google.com/docs/firestore) 是来自 Firebase 和 Google 云平台的 NoSQL 云数据库。它很容易上手，提供了一种快速方便的方法来存储数据，而不需要手动设置数据库。

然而，由于它是一个云数据库，很快就出现了一个问题——我们如何在本地测试它，而不发出不必要的请求，或者只为测试目的建立一个单独的项目？直到大约不到一年前，在本地运行 Firestore 还是不可能的，但幸运的是，随着 [Firebase 模拟器](https://firebase.google.com/docs/rules/emulator-setup)的发布，情况发生了变化。虽然模拟器的主要目的是测试 Firebase 的[安全规则](https://firebase.google.com/docs/rules/)，但是经过一些调整，它们可以用来测试针对本地数据库实例的 CRUD 操作。

出于本教程的目的，我们将使用 Node.js 环境，但是如果您直接从客户端运行 Firestore 查询，基本原则也应该适用。

我们将首先确保安装并设置了 [Firebase CLI](https://firebase.google.com/docs/cli/) 。接下来，我们需要安装和设置模拟器本身。

```
 npm i -D @firebase/testing
    firebase setup:emulators:firestore 
```

如果一切顺利，我们现在可以在本地运行模拟器了。

```
 firebase serve --only firestore 
```

这应该会导致以下控制台输出。

```
 i  firestore: Emulator logging to firestore-debug.log
    ✔  firestore: Emulator started at http://localhost:8080 
```

现在我们已经设置并运行了模拟器，让我们添加一些将要测试的 CRUD 操作。真实世界的用法可能更复杂，但是在本教程中，为了简洁起见，我们将坚持使用简化的例子。

```
 // constants.js

    exports.COLLECTION_NAME = "test_collection";

    // operations.js

    const { NotFound } = require("http-errors");
    const admin = require("firebase-admin");
    const { COLLECTION_NAME } = require("./constants");

    const db = admin.firestore();

    async function listItems(userId) {
      try {
        const collection = await db.collection(COLLECTION_NAME);
        let snapshot;
        if (userId) {
          snapshot = await collection.where("userId", "==", userId).get();
        } else {
          snapshot = await collection.get();
        }

        let result = [];
        snapshot.forEach(doc => {
          const { name, created, type, description, url } = doc.data();

          result.push({
            name,
            created,
            type,
            description,
            url,
            id: doc.id
          });
        });
        return result;
      } catch (e) {
        throw e;
      }
    }

    exports.listItems = listItems;

    async function getItem(itemId, userId) {
      try {
        const snapshot = await db
          .collection(COLLECTION_NAME)
          .doc(itemId)
          .get();

        const data = snapshot.data();
        if (!data || data.userId !== userId) {
          throw new NotFound("Item not found");
        }
        return data;
      } catch (error) {
        return error;
      }
    }

    exports.getItem = getItem;

    async function createItem(newRecord) {
      try {
        const addDoc = await db.collection(COLLECTION_NAME).add(newRecord);
        return { ...newRecord, id: addDoc.id };
      } catch (error) {
        throw error;
      }
    }

    exports.createItem = createItem;

    async function updateItem(itemId, data) {
      try {
        const itemRef = await db.collection(COLLECTION_NAME).doc(itemId);
        const item = await itemRef.get();
        if (!item.exists) {
          throw new NotFound("Item not found");
        }
        const newRecord = {
          ...data,
          updated: Date.now()
        };
        await itemRef.update(newRecord);
        return { ...item.data(), ...newRecord, id: itemId };
      } catch (error) {
        throw error;
      }
    }

    exports.updateItem = updateItem;

    async function deleteItem(itemId) {
      try {
        const docRef = db.collection(COLLECTION_NAME).doc(itemId);
        const snapshot = await docRef.get();
        const data = snapshot.data();
        if (!data) {
          throw new NotFound("No record found");
        }
        await docRef.delete();
        return { status: "OK" };
      } catch (error) {
        throw error;
      }
    }

    exports.deleteItem = deleteItem; 
```

现在我们已经有了基本的操作设置，是时候开始为它们编写测试了。但在此之前，看看我们定义的操作，我们可以看到我们在这里使用的不是模拟器，而是我们的“真正的”数据库。通常我们想要的是在生产中对实际数据库运行操作，并在运行测试时使用模拟器。实现这一点的一种方法是让操作函数接受数据库实例作为一个额外的参数，这样我们就可以根据用例来传递它，但是这似乎不是最好的方法。理想情况下，我们希望根据应用程序运行的环境自动检测必要的数据库设置。

为了实现这一点，我们将使用一个小技巧，它利用了 JavaScript 中的对象是通过引用传递的这一事实，因此允许我们在它们被初始化后修改它们。因此，在这种情况下，我们将定义两个方法- `getDb`和`setDb`，它们将返回所需的数据库实例，并允许我们在需要时覆盖它。我们还将把数据库初始化转移到一个单独的`db.js`文件中。

```
 // db.js

    const admin = require("firebase-admin");

    let db;

    if (process.env.NODE_ENV !== "test") {
      db = admin.firestore();
    }

    exports.getDb = () => {
      return db;
    };

    exports.setDb = (database) => {
      db = database;
    }; 
```

这里我们导出了`getDb`方法，而不是`db`变量，所以即使在数据库被修改后，我们也总能得到正确的数据库实例。默认情况下，`db`将是一个实际的生产数据库，如果我们需要改变，提供`setDb`功能。我们故意不在这里设置模拟器实例，以便在生产和测试代码之间有一个清晰的分离。

最后，我们可以进入我们的测试，它位于`operations.test.js`文件中。还要记得在`operations.js`中将`db`改为新增加的`getDb`方法。正如您可能已经猜到的，我们需要首先设置模拟器实例。

```
 // operations.test.js

    const firebase = require("@firebase/testing");

    // Helper function to setup the test db instance
    function authedApp(auth) {
      return firebase
        .initializeTestApp({ projectId: 'test-project', auth })
        .firestore();
    }

    beforeEach(() => {
    // Set the emulator database before each test
      setDb(authedApp(null));
    });

    beforeEach(async () => {
      // Clear the database before each test
      await firebase.clearFirestoreData({ projectId: 'test-project' });
    });

    afterEach(async () => {
      await Promise.all(firebase.apps().map(app => app.delete()));
    }); 
```

在 [Firebase 快速入门库](https://github.com/firebase/quickstart-nodejs/blob/master/firestore-emulator/javascript-quickstart/test/test.js)中可以找到更多设置模拟器的例子。现在开始实际测试！

```
 // operations.test.js

    const { BOOKMARK_COLLECTION_NAME } = require("./constants");
    const {
      listItems,
      getItem,
      createItem,
      updateItem,
      deleteItem
    } = require("./operations");

    // Setup some mock data
    const userId = "123";
    const record = {
      name: "Test item",
      type: "Page",
      userId: userId,
      created: "1000000"
    };

    it("should properly retrieve all items for a user", async () => {
      await getDb()
        .collection(COLLECTION_NAME)
        .add(record);

      const resp = await listItems(userId);
      expect(resp).toBeDefined();
      expect(resp.length).toEqual(1);
      expect(resp[0]).toEqual(expect.objectContaining(record));

      // Test that another user cannot see other user's items
      const resp2 = await listItems("124");
      expect(resp2.length).toEqual(0);
    });

    it("should retrieve correct items", async () => {
      const db = getDb();
      const ref = await db.collection(COLLECTION_NAME).add(record);
      await db
        .collection(COLLECTION_NAME)
        .add({ ...record, name: "another record" });
      const resp = await getItem(ref.id, userId);
      expect(resp).toBeDefined();
      expect(resp).toEqual(expect.objectContaining(record));

      // Check that other user can't get the same item
      await expect(getItem(ref.id, "124")).rejects.toThrowError(
        "Item not found"
      );
    });

    it("should correctly create items", async () => {
      const item = await createItem(record);
      expect(item.id).toBeDefined();
      expect(item.name).toEqual(record.name);
    });

    it("should correctly update items", async () => {
      const db = getDb();
      const ref = await db.collection(COLLECTION_NAME).add(record);
      await updateItem(ref.id, { name: "Updated name" });
      const item = await db
        .collection(COLLECTION_NAME)
        .doc(ref.id)
        .get();
      expect(item.data().name).toEqual("Updated name");
    });

    it("should correctly delete items", async () => {
      const db = getDb();
      const ref = await db.collection(COLLECTION_NAME).add(record);
      await deleteItem(ref.id);

      const collection = await db
        .collection(COLLECTION_NAME)
        .where("userId", "==", record.userId)
        .get();
      expect(collection.empty).toBe(true);
    }); 
```

测试本身非常简单。我们使用 Jest 断言来检查结果。一些数据库操作，比如创建一个条目，可能被抽象成实用工具工厂方法，但这留给读者作为练习；)

希望现在你对如何在本地进行 Firestore 操作的单元测试有了更好的了解。对这篇文章有任何问题/评论或其他类型的反馈吗？请在这里的评论中或者在 [Twitter](https://twitter.com/Clarity_89) 上告诉我。