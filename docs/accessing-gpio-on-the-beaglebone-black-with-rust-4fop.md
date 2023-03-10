# 访问锈黑的比格犬骨上的 GPIO

> 原文：<https://dev.to/kuon/accessing-gpio-on-the-beaglebone-black-with-rust-4fop>

对于一个小项目，我需要能够用 Linux 控制一些 GPIO。我对树莓派有一些经验，但我想要更开放的东西
(硬件方面)。我试用了 beaglebone black wireless，对
体验很满意。我有一个小问题，用`sysfs`访问 GPIO 很慢
(比如 3Khz)，我想要更快的 GPIO。

我摆弄了一下，发现了一个较低层次的解决方案，我在这篇文章中解释。因为我正在学习这门语言，但它的水平很低，所以你应该能用 c 语言做到。

## 注册概述

为了在硬件级访问 GPIO，我们需要访问一些寄存器。beaglebone black wireless 使用 AM335x 系列处理器。要获得
文档，需要从
德州仪器下载*技术参考手册*，文档编号为 *SPRUH73P* 。

第一步是找到 GPIO 寄存器在内存中的位置。为此，我们
需要查看 **ARM Cortex-A8 内存映射**表。如果我们在表中查找，
有多个 GPIO 寄存器，第一个寄存器 *GPIO0* 位于地址
`0x44E0_7000`，长度为 **4KB** 。每个 GPIO 寄存器具有相同的布局，文档的 **GPIO 寄存器**部分记录了
。有
多个子寄存器。最让我们感兴趣的是使
输出、`GPIO_DATAIN`读数据、`GPIO_DATAOUT`写数据的`GPIO_OE`。

## 记忆映射

要从 Linux 访问寄存器，我们需要直接访问内存。在 Linux 中，
方法是访问`/dev/mem`。

以下 rust 代码将`/dev/mem`映射到内存中，以便直接访问
寄存器。

```
 const GPIO_BASE_REGISTERS: [off_t; 3] = [0x44E0_7000, 0x4804_C000, 0x481A_C000];
const GPIO_REGISTER_SIZE: size_t = 0xFFF;

const GPIO_OE_REGISTER: isize = 0x134;
const GPIO_DATAOUT_REGISTER: isize = 0x13C;
const GPIO_DATAIN_REGISTER: isize = 0x138;

let index = 0;

let path = CString::new("/dev/mem").unwrap();
let fd = unsafe { libc::open(path.as_ptr(), libc::O_RDWR) };

if fd < 0 {
    panic!("Cannot open memory device");
}

let base = unsafe {
    libc::mmap(
        std::ptr::null_mut(),
        GPIO_REGISTER_SIZE,
        libc::PROT_READ | libc::PROT_WRITE,
        libc::MAP_SHARED,
        fd,
        GPIO_BASE_REGISTERS[index],
    )
};

if base.is_null() {
    panic!("Cannot map GPIO");
}

let oe: *mut u32 = unsafe { base.offset(GPIO_OE_REGISTER) as *mut u32 };
let dataout: *mut u32 =
    unsafe { base.offset(GPIO_DATAOUT_REGISTER) as *mut u32 };
let datain: *mut u32 =
    unsafe { base.offset(GPIO_DATAIN_REGISTER) as *mut u32 }; 
```

该代码可以访问三个 GPIO 寄存器(通过改变`index`)。从这里，
我们可以访问三个子寄存器`u32`。

## 访问寄存器

如果我们访问`oe`、`dataout`、`datain`，硬件将立即获取这些变化
，但是我们需要确保编译器不会对
out 访问做任何聪明的事情。在 C 语言中，我们需要在声明
变量时使用 volatile 关键字。在 rust 中我们可以使用`std::ptr::read_volatile`和
T4。

比如，做:

```
std::ptr::write_volatile(oe, 0);
std::ptr::write_volatile(dataout, 0xffffffff); 
```

将启用所有 GPIO 引脚上的输出，并将它们全部设置为`HIGH`。

## 样本代码

为了我的需要，我写了一个快速和肮脏的库。

```
use libc::{c_int, c_void, off_t, size_t};
use std::ffi::CString;

const GPIO_BASE_REGISTERS: [off_t; 3] = [0x44E0_7000, 0x4804_C000, 0x481A_C000];
const GPIO_REGISTER_SIZE: size_t = 0xFFF;

const GPIO_OE_REGISTER: isize = 0x134;
const GPIO_DATAOUT_REGISTER: isize = 0x13C;
const GPIO_DATAIN_REGISTER: isize = 0x138;

#[derive(Debug)]
pub enum PinValue {
    Low = 0,
    High = 1,
}

#[derive(Debug)]
pub enum PinMode {
    Output,
    Input,
}

#[derive(Debug)]
pub enum BitOrder {
    LSBFirst,
    MSBFirst,
}

pub struct Gpio {
    fd: c_int,
    base: *mut c_void,
    oe: *mut u32,
    dataout: *mut u32,
    datain: *mut u32,
}

pub struct Pin<'a> {
    gpio: &'a Gpio,
    index: usize,
}

impl Drop for Gpio {
    fn drop(&mut self) {
        unsafe {
            libc::munmap(self.base, GPIO_REGISTER_SIZE);
            libc::close(self.fd);
        };
    }
}

impl Gpio {
    pub fn open(index: usize) -> Gpio {
        let path = CString::new("/dev/mem").unwrap();
        let fd = unsafe { libc::open(path.as_ptr(), libc::O_RDWR) };

        if fd < 0 {
            panic!("Cannot open memory device");
        }

        let base = unsafe {
            libc::mmap(
                std::ptr::null_mut(),
                GPIO_REGISTER_SIZE,
                libc::PROT_READ | libc::PROT_WRITE,
                libc::MAP_SHARED,
                fd,
                GPIO_BASE_REGISTERS[index],
            )
        };

        if base.is_null() {
            panic!("Cannot map GPIO");
        }

        let oe: *mut u32 = unsafe { base.offset(GPIO_OE_REGISTER) as *mut u32 };
        let dataout: *mut u32 =
            unsafe { base.offset(GPIO_DATAOUT_REGISTER) as *mut u32 };
        let datain: *mut u32 =
            unsafe { base.offset(GPIO_DATAIN_REGISTER) as *mut u32 };

        Gpio {
            fd,
            base,
            oe,
            dataout,
            datain,
        }
    }

    pub fn pin_mode(&self, bit_index: usize, mode: PinMode) {
        let mut bits = unsafe { std::ptr::read_volatile(self.oe) };
        bits = match mode {
            PinMode::Input => bits & !(1 << bit_index),
            PinMode::Output => bits | (1 << bit_index),
        };
        bits &= !(1 << bit_index);
        unsafe { std::ptr::write_volatile(self.oe, bits) };
    }

    pub fn digital_write(&self, bit_index: usize, value: PinValue) {
        let mut bits = unsafe { std::ptr::read_volatile(self.dataout) };
        bits = match value {
            PinValue::Low => bits & !(1 << bit_index),
            PinValue::High => bits | (1 << bit_index),
        };
        unsafe { std::ptr::write_volatile(self.dataout, bits) };
    }

    pub fn digital_read(&self, bit_index: usize) -> PinValue {
        let bits = unsafe { std::ptr::read_volatile(self.datain) };
        if bits & (1 << bit_index) != 0 {
            PinValue::High
        } else {
            PinValue::Low
        }
    }
}

impl Pin<'_> {
    pub fn open(gpio: &Gpio, index: usize) -> Pin {
        Pin { gpio, index }
    }
    pub fn digital_read(&self) -> PinValue {
        self.gpio.digital_read(self.index)
    }
    pub fn digital_write(&self, value: PinValue) {
        self.gpio.digital_write(self.index, value);
    }
    pub fn mode(&self, mode: PinMode) {
        self.gpio.pin_mode(self.index, mode);
    }

    pub fn shift_out(
        data_pin: &Pin,
        clock_pin: &Pin,
        bit_order: BitOrder,
        value: u8,
    ) {
        for i in 0..8 {
            let value = match bit_order {
                BitOrder::LSBFirst => {
                    if (value & (1 << i)) != 0 {
                        PinValue::High
                    } else {
                        PinValue::Low
                    }
                }
                BitOrder::MSBFirst => {
                    if (value & (1 << (7 - i))) != 0 {
                        PinValue::High
                    } else {
                        PinValue::Low
                    }
                }
            };
            data_pin.digital_write(value);

            clock_pin.digital_write(PinValue::High);
            clock_pin.digital_write(PinValue::Low);
        }
    }
} 
```

这个可以这样用:

```
let gpio0 = Gpio::open(0);
let clk = Pin::open(&gpio0, 26);
clk.mode(Output);

loop {
  clk.digital_write(Low);
  clk.digital_write(High);
} 
```

这将在割台 *P8* 的引脚 14 上产生一个信号(查看喙骨
黑色原理图)。