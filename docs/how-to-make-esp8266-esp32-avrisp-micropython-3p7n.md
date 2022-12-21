# 如何制作 ESP8266/ESP32 AVRISP(micropython)

> 原文：<https://dev.to/tab4moji/how-to-make-esp8266-esp32-avrisp-micropython-3p7n>

我有一些 esp8266 芯片，但是没有 FTDI 那样的内置 usb 写入器。没有 USB 写入器，很难下载新的 esp8266 固件。使用 MicroPython，很容易对 esp8266 的操作重新编程。MicroPython 很好看。

而 esp8266 可以用 [ESP8266AVRISP](https://github.com/esp8266/Arduino/tree/master/libraries/ESP8266AVRISP) 库做 AVRISP。
但是这个图书馆只对 arduino 开放。

我想在 esp8266 上使用带有 MicroPython 的 ESP8266AVRISP 库。所以我把这个库移植到了 MicroPython 上。

[https://github.com/tab4moji/ESPAVRISP](https://github.com/tab4moji/ESPAVRISP)

```
$ avrdude -v -p t13 -c arduino -P net:192.168.0.2:328 -U flash:w:FIRMWARE_FOR_AVR.hex:i 
```