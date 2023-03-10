# 用 JavaScript 演奏空鼓

> 原文：<https://dev.to/devdevcharlie/playing-air-drums-in-javascript-47jg>

去年，当我去参加一个周末的工作旅行时，我和一个以前的同事谈论我有多么想学打鼓，但实际上从来没有学过，因为它需要很多空间，而且架子鼓也很贵。

不知从哪里，他把手伸进包里，拿出了 Freedrum 传感器。我以前从未听说过这些东西，但他告诉我，你可以把这些运动传感器绑在鼓棒和鞋子上，用来演奏空鼓。你可以通过蓝牙把它们连接到你的手机或笔记本电脑上，然后就可以玩了。

这是如此巧合，他只是碰巧在他的包里，我太兴奋了！这不仅让我有可能拥有某种更便宜、更便携的架子鼓，而且知道它通过蓝牙连接意味着我不得不尝试用它来破解一些东西。那个周末我试了试，但没成功。

几周前，我买了我自己的 Freedrum 工具包，上周末，我花了一些时间用它来破解，它工作了！！🎉🎉🎉

如果你想跳过其余部分，只看代码，请随意查看 [freedrum.js](https://github.com/charliegerard/freedrum.js) 。

## 演示

[https://www.youtube.com/embed/UrG_mlfvDjE](https://www.youtube.com/embed/UrG_mlfvDjE)

## 它是如何工作的

我之前用 JavaScript、蓝牙和硬件搭建过几个项目，所以我知道在 Node.js 中可以使用 [noble 模块](https://github.com/noble/noble)，直接在浏览器中可以使用 [Web 蓝牙 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Bluetooth_API) 。

在开始之前，我做了一些研究，看看是否有人做过类似的事情，或者至少，我可以找到一些 Freedrum 传感器的蓝牙规范。幸运的是，我找到了这个要点，但这是它的一种…

我首先通过尝试连接到提到的蓝牙服务来检查 gist 中的信息是否仍然正确。

一旦我看到 gist 中指出的 uuids 正在工作，我就开始开发两个版本，一个在 Node.js 中，另一个使用 Web Bluetooth API。

### Web 蓝牙 API

代码和任何 Web 蓝牙 API 例子没有太大区别:

```
const bluetoothLEMidi = '03b80e5a-ede8-4b33-a751-6ce34ec4c700';
const bleMidiCharacteristic = '7772e5db-3868-4112-a1a9-f2669d106bf3';
const uuid = "XrnS1FRG/q/kM7ecsfErcg==";

const options = {
  "filters": [
      {name: uuid},
      {services: [bluetoothLEMidi]}
  ],
};
return navigator.bluetooth.requestDevice(options)
.then(device => device.gatt.connect())
.then(server => server.getPrimaryService(bluetoothLEMidi)
.then(service => service.getCharacteristic(bleMidiCharacteristic)
.then(characteristic => characteristic.startNotifications())
.then(characteristic => {
  characteristic.addEventListener('characteristicvaluechanged', function(e){
  let data = event.target.value;
  });
 }) 
```

我们首先请求一个设备，在我们的例子中是一个 Freedrum 传感器；我们连接到它，请求它的一个服务和特性来获取 BLE MIDI 数据，并订阅通知，这样我们就可以实时接收数据。

### Node.js 版本

与 Node.js 版本相同，代码类似于一个基本的 noble 示例:

```
let state = {};
const sensorId = "XrnS1FRG/q/kM7ecsfErcg==";

function FreedrumsController(sensorId){
    noble.on('stateChange', function(state) {
        state === 'poweredOn' ? noble.startScanning() : noble.stopScanning();
      });

      noble.on('discover', function(peripheral){
        if(peripheral.id === sensorId){
          noble.stopScanning();
        }

        explore(peripheral);
      });

      function explore(peripheral){
        peripheral.connect(function(){
          peripheral.discoverSomeServicesAndCharacteristics([bluetoothLEMidi], [], function(error, services, characteristics){
              characteristics[0].on("read", function(event, isNotification){
                let data = event.toJSON().data;
                state = data;
                onStateChangeCallback(state);
              })
          })
        });
      }

      function onStateChangeCallback(e){
        return e;
      }

      return {
        onStateChange: function ( callback ) {
          onStateChangeCallback = callback;
        }
    }
} 
```

我们首先扫描附近的设备，连接到具有正确 uuid 的设备，连接到所提供的服务和特性，并监听实时数据。

### BLE 迷笛数据

一旦您订阅并接收到来自传感器的数据，它将作为 5 个整数的数组返回，例如[128，128，153，60，90]。

前两个数字代表报头和时间戳字节。它们似乎总是以 128 的值返回，这很好，因为我们无论如何都不会使用它们。对我们来说重要的是最后三个价值。

数组的第三个值代表状态字节或 MIDI 命令。基本上，153 表示音符应该演奏时音符开，137 表示音符不演奏时音符关。

第四个值是 MIDI 音符本身。根据你用鼓棒击打的位置，不同的数字(音符)会返回，你可以用它来触发不同的声音。

最后一个值代表速度，介于 0 和 127 之间，可用于设置音量。

差不多就是这样了！现在您已经用 JavaScript 获得了这些数据，您可以触发任何您想要的声音和可视化效果！

## 改进

目前，我只能从传感器读取数据，但我知道如果你愿意，你也可以写入数据。

我也只和 BLE 的 MIDI 服务合作过，但是也有一个获取原始运动数据的服务，玩起来会很有趣！

* * *

就是这样！如果你想查代码，这里有[回购](https://github.com/charliegerard/freedrum.js)！

希望有帮助！💜