# 为什么在 Chrome 和 Firefox 中 IndexedDB 操作明显变慢？

> 原文：<https://dev.to/skhmt/why-are-indexeddb-operations-significantly-slower-in-chrome-vs-firefox-1bnd>

我正在围绕 IndexedDB 编写一个简单的键/值承诺包装器，继续我几年前开始的一个项目，但在[local feed](https://github.com/localForage/localForage)发布时停止了，因为它做了几乎相同的事情。但是在运行由[诺兰·劳森](http://nolanlawson.github.io/database-comparison/)进行的一些基准测试时，我注意到了一个问题。根据操作的不同，在使用 IndexedDB 时，Chrome 比 Firefox 慢 2 到 7 倍。对于一个简单的插入(objectStore put()操作)，它要慢两倍多。但除此之外，情况会变得更糟。

运行我的测试代码，Firefox 68 的速度为 170 毫秒，单次为 2800 毫秒。put()事务和 10k。put()事务。在 Chrome 76 中运行同样的代码是 430ms 和 19,400ms。是的，在 Chrome 上进行大量交易时，速度大约慢了 700%。在 Nolan Lawson 的[数据库比较](http://nolanlawson.github.io/database-comparison/)中，你可以看到 local feed 和 PouchDB(非 WebSQL)测试。

这很重要，因为像 local feed 这样的东西不会将很多操作合并到一个事务中，这意味着 Chrome 上的多个数据库 put/set 操作会比 Firefox 慢很多。但我不确定你多久会插入一次。

包括一些我写的代码，你可以粘贴到你的浏览器的开发工具中进行简单的基准测试。我试图将测试隔离为仅在一个事务中插入 10，000 个对象，而在 10，000 个事务中插入 10，000 个对象，但是还有一些其他的小事情在进行(例如，将`number`转换为`string`、`for`循环、函数调用、数组访问和数组`push()`)。

因此...为什么？是 Chrome 处理 IndexedDB 事务的速度慢了很多，还是另有原因？有理由一次向数据库中插入数千个对象吗？

```
const testRuns = 10000;

runSingleTX(testRuns)
.then(_=>runManyTX(testRuns));

function runSingleTX(runs) {
    return new Promise(async resolve => {

        const database = await init();

        await clear(database);
        const data = generateData(runs);
        const startTime = Date.now(); // benchmark start

        const transaction = database.transaction(['theStore'], 'readwrite');
        const objStore = transaction.objectStore('theStore');

        for (let i = 0; i < runs; i++) {
            objStore.put(data[i], i+'');
        }

        transaction.oncomplete = async _ => {
            const endTime = Date.now();
            console.log(`${runs} objects inserted in a single transaction: ${endTime-startTime} ms`);

            await clear(database);
            resolve();
        };
    });
}

function runManyTX(runs) {
    return new Promise(async resolve => {
        const database = await init();

        await clear(database);
        const data = generateData(runs);
        const startTime = Date.now(); // benchmark start

        const promises = []

        for (let i = 0; i < runs; i++) {
            promises.push(tx(database, i, data));
        }

        // doesn't matter THAT much, since "readwrite" transactions are basically synchronous
        await Promise.all(promises);

        const endTime = Date.now();
        console.log(`${runs} objects inserted one per transaction: ${endTime-startTime} ms`);

        await clear(database);
        resolve();
    });

    // a transaction for a single .put() operation
    function tx(database, i, data) {
        return new Promise(resolve => {
            const transaction = database.transaction(['theStore'], 'readwrite');
            const objStore = transaction.objectStore('theStore');

            objStore.put(data[i], i+'');

            transaction.oncomplete = _ => resolve();
        });  
    }
}

// utility to generate random data outside of benchmarking
function generateData(size) {
    const data = [];
    for (let i = 0; i < size; i++) {
        data.push(Math.random());
    }
    return data;
}

// utility to clear the database of all entries
function clear(database) {
    return new Promise(resolve => {
        const transaction = database.transaction(['theStore'], 'readwrite');
        const objStore = transaction.objectStore('theStore');

        objStore.clear();

        transaction.oncomplete = _ => resolve();
    });
}

// open/create the database
function init() {
    return new Promise((resolve, reject) => {
        let request = indexedDB.open('theDB', 1);

        // create the db the first time
        request.onupgradeneeded = _ => {
            let transaction = request.result.createObjectStore('theStore').transaction;
            transaction.oncomplete = _ => {
                resolve(request.result);
            };
        }
        request.onsuccess = _ => {
            resolve(request.result);
        };
        request.onerror = _ => reject(request.error);
    });
} 
```

Enter fullscreen mode Exit fullscreen mode