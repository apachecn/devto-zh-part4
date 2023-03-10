# å¦ä½ææ»ð¤éè¿çè§£ JavaScript åè°ãES6 æ¿è¯ºå ES7 å¼æ­¥/ç­å¾è¿è¡å¼æ­¥è°ç¨ð¥ð

> åæï¼<https://dev.to/themarcba/how-to-rock-asynchronous-calls-by-understanding-javascript-callbacks-es6-promises-and-es7-async-await-4nb3>

*äº¤ååå¸èª[developer . blog](https://developer.blog/async-await)T3ã*

å¨ç¨ JavaScript ç¼ç¨æ¶ï¼åè°å¯è½æ¯éå¸¸æç¨çä¸è¥¿ï¼ä½æ¯å½å¤§éä½¿ç¨æ¶ï¼ç¼ç å¯è½ä¼åå¾æ··ä¹±ãè¿ç¯æç« è§£éäºç°ä»£ JavaScript è§èä¸­çæ¿è¯ºåå¼æ­¥/ç­å¾æ¯å¦ä½å·¥ä½çï¼ä»¥åå®ä»¬å¦ä½æé«ä»£ç çå¯è¯»æ§ã

å¨è¿ç¯æç« ä¸­ï¼æå°ä½¿ç¨ç®­å¤´å½æ°ï¼ä½ å¯ä»¥éè¯»æç[ç®­å¤´å½æ°åå®¢æç« ](https://developer.blog/es6-arrow-functions)çç¬¬ä¸ç« ã

## åè°

JavaScript ææ£çä¸ç¹æ¯å½æ°è¢«è§ä¸ºå¯¹è±¡ãè¿ä½¿å¾å°å½æ°ä½ä¸ºåæ°ä¼ éç»å¦ä¸ä¸ªå½æ°æä¸ºå¯è½ï¼ç¶åè¯¥å½æ°å¯ä»¥è°ç¨åé¨ä¼ éçå½æ°ãä¼ éçå½æ°ç§°ä¸ºåè°å½æ°ã

è¿å¨å¤ç sun å¼æ­¥çä»»å¡æ¶å¾æ¹ä¾¿ï¼æä»¬ä¸è½ç¡®å®ä»»å¡çç¡®åå®ææ¶é´ï¼æä»¥æä»¬å¯ä»¥å¤çç»ææ°æ®ãä¸ä¸ªçå®çä¾å­æ¯ä» REST API è¯·æ±æ°æ®ã

ä¸é¢æ¯ä¸ä¸ªå½æ°çä¼ ç»åè°çä¾å­ââåºäºæ¼ç¤ºçç®çââéè¦ 2 ç§éå°ä¸¤ä¸ªæ°ç¸å :

```
// Definition of the asynchronous function
const add = (a, b, callback) => {
    setTimeout(() => {
        const result = a + b
        callback(result)
    }, 2000);
}

// Calling the asynchronous function and passing the callback function
add(3, 6, sum => {
    // Execute this when result is ready
    console.log(`The sum is: ${sum}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

å½æ¨æ§è¡è¯¥ä»£ç æ¶ï¼å°è°ç¨ add å½æ°ï¼ä¸¤ç§éåï¼å°æ§è¡åè°å½æ°ï¼å¹¶æ¾ç¤ºç»æ(è®°å½å°æ§å¶å°)ã

çèµ·æ¥æ²¡é£ä¹ç³ï¼å¯¹å§ï¼ä½æ¯æä¸¤ä»¶äºä½¿è¿ç§æ¹æ³ä½¿ç¨èµ·æ¥ä»¤äººçå:

*   å½è¯å¾å¼å¥éè¯¯å¤çæ¶(åºéäº)
*   å½è¯å¾ä¸ä¸ªæ¥ä¸ä¸ªå°ä½¿ç¨åç§åè°å½æ°æ¶

### éè¯¯å¤ç

è®©æä»¬åè®¾æä»¬èæçå½æ°åªè½æ·»å æ­£æ°ãæä»¬å¸æç¨æ·ç¥éå¨è¯å¾å¤çè´æ°æ¶åºç°äºé®é¢ã

```
const add = (a, b, callback) => {
    setTimeout(() => {
        // Checking if the input numbers are right
        if(a >= 0 && b >= 0) {
            const result = a + b
            callback(result)
        } else {
            // Passing an error if there is a negative input
            callback(undefined, 'Numbers must be non-negative')
        }
    }, 2000);
}

add(3, -6, (sum, error) => {
    // If an error occured in the add function, display it
    if(error) {
        console.log(`An error occured: ${error}`)
    } else {
        console.log(`The sum is: ${sum}`)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

### é¾æ¥

ä¸ä¸ªæ¥ä¸ä¸ªå°æ§è¡åç§åè°(é¾æ¥)ï¼æèè¢«ç§°ä¸ºâ*åè°å°ç±*âï¼ä¼å¾å¿«åå¾éå¸¸æ··ä¹±ã

åè®¾æä»¬æ³è¦è®¡ç®ç»ææ»åçå¹³æ¹ï¼ç¶åæ£æ¥è¿ä¸ªå¹³æ¹æ¯å¥æ°è¿æ¯å¶æ°ãæ¯æ¬¡å¤è± 1 ç§éæ§è¡ã

```
const add = (a, b, callback) => {
    setTimeout(() => {
        // Checking if the input numbers are right
        if(a >= 0 && b >= 0) {
            callback(a + b)
        } else {
            // Passing an error if there is a negative input
            callback(undefined, 'Numbers must be non-negative')
        }
    }, 2000);
}

const tripleDown = (a, callback) => {
    setTimeout(() => {
        callback(a * 3)
    }, 1000);
}

const isEven = (a, callback) => {
    setTimeout(() => {
        callback(a % 2 === 0)
    }, 1000);
}

add(3, -6, (sum, error) => {
    // If an error occured in the add function, display it
    if(error) {
        console.log(`An error occured: ${error}`)
    } else {
        square(sum, tripleResult => {
            isEven(square, isEvenResult => {
                console.log(`The sum is: ${sum}`)
                console.log(`The triple of the sum is: ${tripleResult}`)
                console.log(`The triple is even: ${isEvenResult}`)
            })
        })
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

ææ³æä»¬ç°å¨å¯ä»¥åæä»£ç å¼å§åå¾æ··ä¹±ï¼è¿ä½¿å¾ä¸æ®µæ¶é´åå¾é¾çè§£åç»´æ¤ã

## æ¿è¯º

æ¿è¯ºææ´ï¼2015 å¹´ï¼å½ ES6 åå¸æ¶ï¼ä¸ä¸ªæ¼äº®çå°åè½è¢«å¼å¥ï¼è¿ä½¿å¾å¼åèå¯ä»¥éç¦»åè°å°ç±ã

æ¿è¯ºé¡¾åæä¹å°±æ¯:æ¿è¯ºå¨æªæ¥çæä¸ªæ¶é´ä¼æç»æãé£ä¸ªç»æå¯ä»¥æ¯æåçï¼é£ä¹è¿ä¸ªæ¿è¯ºå°ä¼è¢«*å±¥è¡*æèå¤±è´¥ï¼è¿å°ä¼ä½¿è¿ä¸ªæ¿è¯ºè¢«*æç»*ãè½ç¶(è¿)æ²¡æç­æ¡ï¼ä½æ¿è¯ºæ¯*å¾å®*ã

è®©æä»¬ç¨ä¸ä¸ªæ¿è¯ºæ¥åæä»¬å¼å§æ¶çä»£ç (ä¸¤ä¸ªæ°å­å»¶è¿ä¸¤ç§ç¸å çä¾å­)ã

```
const add = (a, b) => {
    // Returning a promise that there will be an answer sometime
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            // Resolving the promise
            // This means it was successful
            resolve(a + b)
        }, 2000);
    })
}

// Executing the add function, *then* executing the callback.
add(2, 9).then(sum => {
    console.log(`The sum is: ${sum}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

å½æä»¬åå»ºçæ¿è¯ºæ­£å¨è¢«*è§£æ*æ¶ï¼`.then()`æ­£å¨è¢«æ§è¡ï¼å®å°å·æå¨è§£æè°ç¨ä¸­ä¼ éçä»»ä½å¼ã

### éè¯¯å¤ç

å¤çéè¯¯æ¯æ¿è¯ºçä¹è¶£ãèä¸æ¯è®©åè°å½æ°æ¥åé¢å¤çåæ°ã

æä»¬ä¸è°ç¨æ¿è¯ºä¸­ç`resolve()`ï¼èæ¯è¦è°ç¨`reject()`è®©æ¿è¯ºä¸æåç»æãè®©æä»¬æ©å±è¿ä¸ªä¾å­ï¼å¢å ä¸å¤çè´æ°çéå¶:

```
const add = (a, b) => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(a >= 0 && b >= b) {
                // The promise is being fullfilled successully
                resolve(a + b)
            } else {
                // The promise is being fullfilled unsuccessully
                reject('Numbers must be non-negative')
            }
        }, 2000);
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

ç°å¨å¤çè¿ä¸ªéè¯¯å·²ç»å¾ä¼éäºãæä»¬åªæ¯ç»æ¿è¯ºæ§è¡å¢å ä¸ä¸ª`.catch()`:

```
add(2, -9).then(sum => {
    // Processing the asynchonous function result
    console.log(`The sum is: ${sum}`)
}).catch(error => {
    // The error has being "caught"
    console.log(`An error occured: ${error}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

### é¾æ¥

ç°å¨å°åç§å¼æ­¥å½æ°é¾æ¥å¨ä¸èµ·ä¹æ´å®¹æäºãè¿éæä¸ä¸ªé¾æ¥ä¸æ¬¡ç¸åç`add()`å½æ°çä¾å­ãåå  2+5ï¼åå é£ä¸ª+ 43 çç»æï¼åå é£ä¸ª+ 1000 çç»æã

```
add(2, 5).then(firstSum => {
    console.log('first sum', firstSum);
    return add(firstSum, 43)
}).then(secondSum => {
    console.log('second sum', secondSum);
    return add(secondSum, 1000)
}).then(thirdSum => {
    console.log('third sum', thirdSum);
}).catch(error => {
    console.log('error', error);
}) 
```

Enter fullscreen mode Exit fullscreen mode

è¿æ¯ä¸ç§æ´å¹²åçæ¹å¼ï¼äººä»¬å¨ 2015 å¹´å¯¹æ­¤æå°éå¸¸å´å¥ï¼å ä¸ºä»ä»¬ç»äºå¯ä»¥äº¤ä»æ´å¹²åçä»£ç ï¼å¹¶å°åè°å°ç±è¸¢ååå¤(å¸¸è§å°ç±)ã

ä½æ¯ä»ç¶æä¸¤ä¸ªé®é¢:

*   å¨æ¯ä¸ªåè°çåè°ä¸­ï¼ä½ ä¸è½è®¿é®ä¸­é´çç»æ(ä¾å¦ï¼ä½ ä¸è½è®¿é®ç¬¬ä¸ä¸ª`.then()`ç`firstSum`
*   å°å¼æ­¥å½æ°é¾æ¥å¨ä¸èµ·ä»ç¶ä¸æ¯é£ä¹ç´è§

è¿ä¸¤ä¸ªé®é¢å¨ä¸å¹´ååå¸ç ES7 ä¸­å¾å°äºè§£å³ã

## å¼æ­¥/ç­å¾

Async/Await ä¸æ¯ä¸é¡¹æ°ææ¯ï¼èä¸æ¯å»ºç«å¨æ¿è¯ºä¹ä¸çæ°å·¥å·éãå®çç®çæ¯ä½¿å¼æ­¥å½æ°çæ­£æäºç¼ç åçè§£ï¼å¶è¯­æ³å¯ä»¥éå¸¸èªç¶å°ä»é®çä¸æµåºãææ£çæ¯ï¼å·²ç»ç¼ç¨çä¸è¥¿å°ç»§ç»­ä¸ async/await ä¸èµ·å·¥ä½ï¼å ä¸ºæä»¬åªæ¯ä»¥ä¸åçæ¹å¼ç¼åä»£ç ï¼èä¸æ¯ä¸ç§æ°ææ¯ã

### å¼æ­¥

å½æ¨å°`async`å³é®å­æ¾å¨å½æ°åé¢æ¶(ä¸ç®¡æ¯ arrow è¿æ¯ regular)ï¼å®ä¼èªå¨è¿åä¸ä¸ª(å·²è§£å³ç)æ¿è¯ºï¼èä¸æ¯è¿åå¼ã

```
const doAsynchronousStuff = async () => {
    return 4711;
}

// Returns: Promise { 4711 } 
```

Enter fullscreen mode Exit fullscreen mode

### ç­å¾

å½å¨å½æ°è°ç¨åä½¿ç¨`await`æ¶ï¼JavaScript *å¨ç»§ç»­æ§è¡ä¸ä¸è¡ä¹åç­å¾*çæ¿è¯ºè¢«å±¥è¡ã

**`await`åªè½å¨`async`å½æ°åé¨ä½¿ç¨ï¼**

è®©æä»¬ççè¿ä¸ªä¾å­(åè®¾æ¥èª*ç`add`å½æ°æ¿è¯º>éè¯¯å¤ç*å·²ç»å­å¨:

```
const doCalculations = async () => {
    const sum = await add(13, 99)
    return sum
}

doCalculations().then(result => {
    console.log(`The result is: {result}`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

### éè¯¯å¤ç

ä¸ä¸ª`await`å½æ°è°ç¨åçä¸ä¸è¡æ¯**ï¼åªæå½æ¿è¯ºè¢«å±¥è¡**æ¶æä¼è¢«æ§è¡ãå½å®è¢«æç»æ¶ï¼å¼æ­¥å½æ°ä¸­çæææªæ¥æ§è¡é½å°è¢«åæ­¢ã

æä¸ç§æ¹æ³å¯ä»¥æææ¯ä¸ªåç¬ç`await`å½æ°è°ç¨çéè¯¯ï¼ä½¿ç¨ä¸ä¸ªå¾å¥½çèå¼ try/catch è¯­å¥:

```
const doCalculations = async () => {
    let sum;
    try {
        // Try to execute this...
        sum = await add(13, -99)
    } catch (error) {
        // If something goes wrong, we catch the error here
        console.log(`An error occured: ${error}`);
    }
    return sum
} 
```

Enter fullscreen mode Exit fullscreen mode

### é¾æ¥

ç°å¨é¾æ¥æ¯ä»¥åæ´å®¹æäºãæ¨ç¼åä»£ç çæ¹å¼çè³è®©æ¨ç¸ä¿¡å®ä»¬æ¯åæ­¥è°ç¨ï¼ä½å®éä¸ï¼ææç`Promise`é­æ³é½åçå¨å¹åã

const doccalculations = async()= > {
const sum = await add(13ï¼-99)
const sum2 = await add(sumï¼1000)
const sum 3 = await add(sum 2ï¼9999)

```
// You could access all three variables here.
// For example to do comparisons

return sum3 
```

Enter fullscreen mode Exit fullscreen mode

}

## æ»ç»ð

async/await ç°å¨æ¯ä¸ä¸ªè¡ä¸æ åï¼æ¨èä½ ä½¿ç¨å®ï¼å ä¸ºå®ç»ä½ å¸¦æ¥äºå¾å¤å¥½å¤ãç¶èï¼éè¦çæ¯è¦ç¥éå®ä»ä½èæ¥ï¼å¦ä½å¨å¼æçä¸å·¥ä½ãä½¿ç¨å®æ¶ï¼å¾å®¹æå¿è®°æä»¬å®éä¸æ¯å¨åå¼æ­¥è°ç¨ã

ç°å¨ï¼æ¨åºè¯¥å·²ç»åå¤å¥½åå»ºèªå·±çæ¯æ Promise çåºï¼å¹¶ä»¥ä¸ç§ç®åæè¯»çæ¹å¼ä½¿ç¨å·²ç»æ¯æ Promise çç°æåº(ææéè¦çåºé½æ¯æ Promise)ã

*ç§çç± [Alex å¨ Unsplash](https://unsplash.com/photos/OMF0olQno6M) ä¸ææ*