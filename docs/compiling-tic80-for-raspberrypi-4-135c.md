# 为 raspberrypi 4 编译 tic80

> 原文：<https://dev.to/tonetheman/compiling-tic80-for-raspberrypi-4-135c>

我从这里开始，但改变了一点。

[https://github . com/nes box/TIC-80/wiki/build-instructions # raspberry-pi-retro pie](https://github.com/nesbox/TIC-80/wiki/build-instructions#raspberry-pi-retropie)

主要是我不做回传的事情。

你还需要运行 sudo raspi-config 并选择高级选项(7)，然后 A7 Gl 驱动你需要选择 Gl 驱动(G2 假 KMS OpenGL 桌面驱动与假 KMS)

然后所有这些东西...

```
sudo apt install --reinstall libraspberrypi0 libraspberrypi-dev libraspberrypi-doc libraspberrypi-bin

sudo apt install git build-essential libgtk-3-dev libsdl2-dev zlib1g-dev liblua5.3-dev cmake

# this is really weird but needed the link will fail without it
# needed this for bcm_host in the build
# this one took a while to find ha
sudo apt install libraspberrypi-dev raspberrypi-kernel-headers
sudo cp /opt/vc/lib/libbcm_host.so /usr/local/lib

git clone --recursive https://github.com/nesbox/TIC-80
cd TIC-80
mkdir tonybuild
cd tonybuild
cmake ..
make 
```

Enter fullscreen mode Exit fullscreen mode

一旦你这样做了，穿过了你所有的附属物，你就应该有一个可以跑的 bin/tic80 了！