# 通过 Uart 在 Raspberry Pi 上引导自己的内核

> 原文：<https://dev.to/nicolasmesa/booting-your-own-kernel-on-raspberry-pi-via-uart-565f>

交叉发布自我的个人博客[https://blog.nicolasmesa.co](https://blog.nicolasmesa.co)。

在过去的几周里，我一直在读一本很棒的[书](https://github.com/s-matyukevich/raspberry-pi-os/)，讲述了如何为一个树莓 Pi 从头开始构建自己的操作系统。这是一次很棒的经历，我学到了很多。我不喜欢的一点是我的开发工作流程，它看起来像这样:

1.  进行代码更改。
2.  进行变革。
3.  拔掉树莓皮。
4.  取出 SD 卡。
5.  把 SD 卡插到我的笔记本电脑上。
6.  将新构建的内核复制到 SD 卡上。
7.  弹出 SD 卡。
8.  将 SD 卡放回树莓派上。
9.  把树莓派连接到我的电脑上。
10.  关闭以前的终端，因为`screen`命令使它变得毫无用处。
11.  使用`screen`命令连接到 Raspberry Pi。
12.  测试我的变化。

所有这些都非常耗时，使得反馈循环非常缓慢(尤其是当我忘记了一个关键步骤，例如将 SD 卡放回 Raspberry Pi 中)。我搜索了一下，看看是否有办法改进我的工作流程，在 github repo 中发现了一个名为 [UART boot](https://github.com/s-matyukevich/raspberry-pi-os/issues/115) 的问题。我通读了一遍，决定自己构建 UART 引导作为学习练习。在这篇博文中，我讲述了我使 UART 启动工作的过程，包括我的错误假设/失败以及我如何克服它们。

## 要求

为了让这个项目值得我花时间，我决定它需要能够做到以下几点:

1.  内核必须同时充当 UART 引导加载程序和内核本身(一个单独的可执行文件)。
2.  我必须能够通过 UART 发送一个新编译的内核，而且 Raspberry Pi 必须启动。
3.  我必须能够使用我的程序通过 UART 进行交互会话(基本上，替换`screen`)。
4.  `screen`替换必须让我的终端处于工作状态(我假设有办法让这个在`screen`中工作，但是，我想把它作为一个学习练习)。

## 先决条件

如果你想继续下去，你需要:

1.  要满足书里的所有[先决条件。](https://github.com/s-matyukevich/raspberry-pi-os/blob/master/docs/Prerequisites.md)
2.  学完第一课。
3.  完成第二个[练习](https://github.com/s-matyukevich/raspberry-pi-os/blob/master/docs/lesson01/exercises.md)使用 UART 而不是 Mini UART(这可能仍然适用于 Mini UART，但我没有尝试过)。
4.  Python 3.7(这是我用的版本)。

**注意**如果您尚未完成练习，但仍想继续，请遵循下面的设置说明。我还标记了每个提交，以便更容易跟进/向前跳转。

### 设置

如果您已经有了自己的项目，请确保您已经构建了自己的内核，并将其保存在 SD 卡中。否则，您可以克隆我的[库](https://github.com/nicolasmesa/PiOS)并跟随:

```
$  git clone https://github.com/nicolasmesa/PiOS
$  git checkout tags/uart-boot-initial 
```

#### Git 标签

我已经为每个部分创建了 commit 标记，以防您想要试验项目的特定部分。您可以通过运行命令来检查标签:

```
$  git checkout tags/<tag-name> 
```

我将在每一节的末尾引用标记名。

**Git 标签:** `uart-boot-initial` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-initial)

## `screen`替换

为了能够通过 UART 发送内核，我们需要与 Raspberry Pi 建立一个串行连接，并发送内核字节。`screen`替换将做同样的事情，不同之处在于它也将从串行连接和`stdin`读取，并将写入`stdout`。如果我们能够做到这一点，发送内核应该很容易。

让我们从安装`pyserial`开始吧(我建议你在一个[虚拟环境](https://docs.python-guide.org/dev/virtualenvs/)中这样做):

```
(rpi_os) $  pip install pyserial==3.4 
```

让我们创建一个名为`boot_send.py`的文件，并添加以下代码:

```
import os
import select
import serial
import sys
import time
import tty

class UartConnection:

    def __init__ (self, file_path, baud_rate):
        self.serial = serial.Serial(file_path, baud_rate)

    def send_string(self, string):
        return self.send_bytes(bytes(string, "ascii"))

    def send_bytes(self, bytes_to_send):
        return self.serial.write(bytes_to_send)

    def read(self, max_len):
        return self.serial.read(max_len)

    def read_buffer(self):
        return self.read(self.serial.in_waiting)

    def read_buffer_string(self):
        return self._decode_bytes(self.read_buffer())

    def start_interactive(self, input_file, output_file):
        try:
            tty.setcbreak(input_file.fileno())
            while True:
                rfd, _, _ = select.select([self.serial, input_file], [], [])

                if self.serial in rfd:
                    r = self.read_buffer_string()
                    output_file.write(r)
                    output_file.flush()

                if input_file in rfd:
                    r = input_file.read(1)
                    self.send_string(r)
        except KeyboardInterrupt:
            print("Got keyboard interrupt. Terminating...")
        except OSError as e:
            print("Got OSError. Terminating...")
        finally:
            os.system("stty sane")

    def _decode_bytes(self, bytes_to_decode):
        return bytes_to_decode.decode("ascii") 
```

哇，代码真多！让我们过一遍。

##### 构造函数

构造函数(`__init__`)接收我们用来创建串行连接的两个参数:

*   `file_path`:UART 设备所在文件的路径(我的电脑里的`/dev/cu.SLAB_USBtoUART`)。
*   `baud_rate`:串行连接中使用的波特率。

##### 效用方法

我们有一堆具有不同抽象层次的实用方法，但它们最终都调用了`send_bytes`(调用`serial.write`)或`read`(调用`serial.read`)。

##### 开始 _ 互动

`start_interactive`方法接收两个参数(`input_file`和`output_file`)。我们将这些参数分别称为`stdin`和`stdout`。这个方法做的第一件事是在`stdin`文件上调用`tty.setcbreak(input_file.fileno())`。`tty.setcbreak`将`tty`转换为“raw”模式，并允许我们在键入时读取字符，而不是缓冲它们，直到用户按下 enter 键。

`select`函数接收一组要监控的文件描述符。当被调用时，它会一直阻塞，直到其中一个文件包含我们可以读取的数据。当`select`函数返回时，`rfd`变量有一个包含可用数据的文件描述符列表。接下来，我们验证`serial`文件描述符是否有可用的数据，如果有，我们读取所有数据并将输出发送到我们的终端(`output_file`)。我们还检查我们的`input_file`是否有数据，如果有，我们通过串行连接将它发送到 Raspberry Pi。请注意，在这种情况下，我们不向`output_file`写入任何内容，我们依赖于 Raspberry Pi 回显我们发送的字符。

这个调用使我们的 tty 不再处于原始模式。这个功能允许我们继续使用同一个终端(而不是我用`screen`遇到的问题)。

```
def main():
    import sys
    uart_connection = UartConnection(
        # Change these to match your setup
        file_path='/dev/cu.SLAB_USBtoUART',
        baud_rate=115200
    )
    time.sleep(1)
    uart_connection.start_interactive(sys.stdin, sys.stdout)

if __name__ == ' __main__':
    main() 
```

现在，让我们执行脚本。假设一切正常，您应该会看到类似这样的内容:

```
(rpi_os) $  python boot_send.py
Hello world! 
```

你应该可以输入任何你想输入的东西，树莓派应该会回应它。按`ctrl-c`退出，你的终端应该还能正常工作。

**注意:**如果你使用我提供的 git 分支，你需要点击`enter`才能看到“Hello world！”消息。我这样做是为了确保内核只在`boot_send`成功连接后才发送`Hello world!`消息。

**Git 标签:** `uart-boot-screen-replacement` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-screen-replacement)

## 通过 UART 发送内核

发送内核应该和通过串口发送字符没有什么不同；毕竟，一切都被转换成字节。

### 协议

我们将使用以下协议来发送内核:

1.  Raspberry Pi 引导到内核并在等待读取一行时阻塞。
2.  如果它读取的行与字`kernel`匹配，那么我们进入 UART 引导模式，如果不匹配，我们跳过 UART 引导。
3.  当处于 UART 引导模式时，`boot_send`脚本发送一个整数(4 字节),包含我们将要发送的内核大小。
4.  Raspberry Pi 记录下这个大小并将其发送回去。
5.  `boot_send`脚本验证 Raspberry Pi 得到了正确的数字。
6.  `boot_send`脚本开始读取内核，并通过串行连接逐字节发送。
7.  Raspberry Pi 接收每个字节并…
    1.  保存所有字节的运行总和(校验和)。
    2.  将该字节放入内存(覆盖当前内核)。
8.  当 Raspberry Pi 接收到与内核大小相等的字节数(在之前的步骤中发送)时，它会通过 UART 发送校验和。
9.  `boot_send`脚本验证校验和是否匹配(用于错误检测)。
10.  Raspberry Pi 用字符串`"Done"`响应，表示它现在将跳转到新的内核。
11.  Raspberry Pi 跳回到地址`0x00`开始在新内核中执行。
12.  `boot_send`脚本启动一个交互式会话。

我们需要记住的一件事是发送大小时的字节序。我决定在所有的交流中使用大端字节序。

### boot_send 修改

我们需要向我们的`boot_send`脚本添加一些功能来实现协议:

##### UART 连接

让我们将这些函数添加到我们的`UartConnection`类:

```
class UartConnection:
    # ...
    def send_line(self, line):
        if not line.endswith("\n"):
            line += "\n"
        return self.send_string(line)

    def send_int(self, number):
        if number > 2 ** 32 - 1:
            raise 'Number can only be 4 bytes long'
        number_in_bytes = number.to_bytes(4, byteorder='big')
        return self.send_bytes(number_in_bytes)

    def read_int(self):
        bytes_to_read = 4
        number_bytes = self.read(bytes_to_read)
        return int.from_bytes(number_bytes, byteorder='big')

    def read_line(self):
        return self._decode_bytes(self.serial.readline()) 
```

这里最重要的是我们决定使用的`byteorder`(`big`代表`big-endian`)。

##### 内核校验和

我们计算校验和来验证一些错误。我不认为这是检查错误的最佳方式，因为像改变两个字节的顺序这样简单的事情仍然会使这个校验和通过。我使用它是因为它给了我更多的信心，让我相信事情会按预期进行。

```
def compute_kernel_checksum(kernel_bytes):
    num = 0
    for b in kernel_bytes:
        num = (num + b) % (2 ** 32)
    return num 
```

我们在`2^32`处换行，因为这是整数的最大长度。

##### 发送 _ 内核

让我们添加一个函数，按照我们的协议将内核发送到 Raspberry Pi:

```
def send_kernel(path, uart_connection):
    with open(path, mode='rb') as f:
        uart_connection.send_line("kernel")
        kernel = f.read()
        size = len(kernel)
        checksum = compute_kernel_checksum(kernel)

        print("Sending kernel with size", size, "and checksum", checksum)
        uart_connection.send_int(size)
        time.sleep(1)
        size_confirmation = uart_connection.read_int()
        if size_confirmation != size:
            print("Expected size to be", size, "but got", size_confirmation)
            return False

        print("Kernel size confirmed. Sending kernel")
        uart_connection.send_bytes(kernel)
        time.sleep(1)

        print("Validating checksum...")
        checksum_confirmation = uart_connection.read_int()
        if checksum_confirmation != checksum:
            print("Expected checksum to be", checksum,
                  "but was", checksum_confirmation)
            return False

        line = uart_connection.read_line()
        if not line.startswith("Done"):
            print("Didn't get confirmation for the kernel. Got", line)
            return False

        return True 
```

我们的函数接收一个内核文件的`path`和一个`UartConnection`对象。我们使用这些来遵循上面描述的协议，将内核发送到 Raspberry Pi。

##### 调用`send_kernel`函数

让我们对我们的`main`函数做一个小小的调整。

```
def main():
    # ...
    uart_connection = UartConnection(...)
    time.sleep(1)
    result = send_kernel(
        path="kernel8.img",
        uart_connection=uart_connection
    )
    if result:
        print("Done!")
        uart_connection.start_interactive(sys.stdin, sys.stdout)
    else:
        print("Error sending kernel :(") 
```

我们的脚本首先发送内核，然后开始一个交互式会话。

**Git 标签:** `uart-boot-boot-send-client-side` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-boot-send-client-side)

### 内核端修改

让我们深入了解内核所需的修改。

##### 分支 _ 目的 _ 地址

我们的引导协议的最后一部分涉及跳回到地址`0x00`并从该存储位置开始执行。让我们从添加一个可以做到这一点的函数开始。

打开`utils.h`文件，添加函数声明:

```
extern void branch_to_address( void * ); 
```

现在，转到`utils.S`文件并添加定义:

```
global branch_to_address
branch_to_address:
    br x0 
```

##### UART _ send _ int/UART _ read _ int

我们需要通过 UART 发送和读取整数(内核大小和校验和)。让我们打开`uart.h`文件并添加以下声明:

```
void uart_send_int(int number);
int uart_read_int(); 
```

让我们实现`uart.c`文件中的函数:

```
int uart_read_int() {
    int num = 0;
    for (int i = 0; i < 4; i++) {
        char c = uart_recv();
        num = num << 8;
        num += (int)c;
    }
    return num;
}

void uart_send_int(int number) {
    uart_send((char)((number >> 24) & 0xFF));
    uart_send((char)((number >> 16) & 0xFF));
    uart_send((char)((number >> 8) & 0xFF));
    uart_send((char)(number & 0xFF));
} 
```

在`uart_read_int`中，我们一次读取一个字节(首先是最高有效字节)，并一直将这些字节向左移动，直到我们得到代表整数的 4 个字节。在`uart_send_int`中，我们一次发送一个字节(最高有效字节优先)。注意，在这里使用指针并不是一个好主意，因为我们不应该假设 Raspberry Pi 如何在内存中存储`int`([大端对小端](https://en.wikipedia.org/wiki/Endianness))。

##### 包括 utils.h

打开`kernel.c`文件，添加以下`#include`语句:

```
#include "utils.h" 
```

##### [阅读线](#readline)

这里我们写了一个非常简单的`readline`函数:

```
int readline(char *buf, int maxlen) {
    int num = 0;
    while (num < maxlen - 1) {
        char c = uart_recv();
        if (c == '\n' || c == '\0' || c == '\r') {
            break;
        }
        buf[num] = c;
        num++;
    }
    buf[num] = '\0';
    return num;
} 
```

##### strcmp

我们的协议需要读取一行文本，并将其与单词`kernel`进行比较。为此，我们需要一个字符串比较函数:

```
int strcmp(char *str1, char *str2) {
    while (1) {
        if (*str1 != *str2) {
            return *str1 - *str2;
        }

        if (*str1 == '\0') {
            return 0;
        }

        str1++;
        str2++;
    }
} 
```

##### 复制 _ 并 _ 跳转 _ 至 _ 内核

现在我们有了实现之前提出的协议的所有部分:

```
void copy_and_jump_to_kernel() {
    int kernel_size = uart_read_int();
    uart_send_int(kernel_size);

    char *kernel = (char *)0;
    int checksum = 0;
    for (int i = 0; i < kernel_size; i++) {
        char c = uart_recv();
        checksum += c;
        kernel[i] = c;
    }
    uart_send_int(checksum);

    uart_send_string("Done copying kernel\r\n");
    branch_to_address((void *)0x00);
} 
```

这个函数接收内核大小并将其发送回来。然后，从新内核接收每个字节，并从地址`0x00`开始将其放入内存。接下来，它发送计算出的校验和以及一个字符串`"Done copying the kernel"`。最后，它转移到地址`0x00`(新内核驻留的地方)开始执行。

##### 内核 _ 主

让我们添加在引导时读取一行的逻辑，并将其与`kernel`进行比较，以决定我们是否要通过 UART 引导:

```
void kernel_main(void) {
    int buff_size = 100;
    uart_init();

    char buffer[buff_size];
    readline(buffer, buff_size);
    if (strcmp(buffer, "kernel") == 0) {
        copy_and_jump_to_kernel();
    }

    uart_send_string("Hello world!\r\n");
    while (1) {
        uart_send(uart_recv());
    }
} 
```

请注意，对`copy_and_jump_to_kernel`的调用永远不会返回。

**Git 标签:** `uart-boot-boot-send-kernel-side` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-boot-send-kernel-side)

### 测试 UART 启动

#### 构建内核，复制到 SD 卡

下面是我构建内核并将其复制到 Mac 上的 SD 卡的步骤:

```
$  ./build.sh
$  cp kernel8.img /Volumes/boot/ 
```

#### 引导测试:发送同一个内核

现在弹出 SD 卡，放入你的树莓派。

让我们运行我们的`boot_send`脚本:

```
(rpi_os) $  python boot_send.py 
Sending kernel with size 1991 and checksum 201267
Kernel size confirmed. Sending kernel
Validating checksum...
Done! 
```

如果你看到这个，这意味着你刚刚通过 UART 发送了一个新的内核(与你安装在 SD 卡中的内核相同)!此时，`kernel`卡在了`readline`函数中，检查是否要发送新内核。让我们按下回车键:

```
Hello world!
Got keyboard interrupt. Terminating... 
```

按`ctrl-c`退出。

#### 开机测试:做一点小改动

发送相同的内核并不是我们做这些工作的原因！让我们对我们的内核代码做一个小小的改变，编译它并通过 UART 发送它(而不是把它放在 SD 卡里)。转到`kernel_main`函数，更改我们发送的字符串:

```
int kernel_main(void) {
    // ...
   uart_send_string("Hello from a new kernel!!!\r\n"); 
   // ...
} 
```

让我们编译它并执行我们的 python 脚本(不再需要复制任何东西到 SD 卡上！):

```
(rpi_os) $  ./build.sh
(rpi_os) $  python boot_send.py 
Sending kernel with size 2005 and checksum 202127
Kernel size confirmed. Sending kernel
Validating checksum...
Done! 
```

太好了！我们发了内核！现在，关键时刻到了！让我们按回车键:

```
Hello from a new kernel!!!
Got keyboard interrupt. Terminating... 
```

**Git 标签:** `uart-boot-string-change-test-uart-boot` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-string-change-test-uart-boot)

#### 开机测试:添加功能

我们做到了！我们完了，对吧？嗯，没那么快！让我们尝试做出不同的改变。我们来添加一个函数，从`kernel_main`开始调用。打开`kernel.c`文件，添加以下代码:

```
// ...
void my_test_function(void) {
    uart_send_string("Sending a test message!\r\n"); 
}

void kernel_main(void) {
    // ...
   uart_send_string("Hello from a new kernel!!!\r\n");  
   my_test_function();
   // ...
} 
```

我们添加了一个名为`my_test_function`的新函数，我们从`kernel_main`调用它。让我们编译并通过 UART 发送这个:

```
(rpi_os) $  ./build.sh
(rpi_os) $  python boot_send.py Sending kernel with size 2077 and checksum 208704
Kernel size confirmed. Sending kernel
Validating checksum... 
```

它挂在*验证校验和…* 部分！这是怎么回事？让我们想一想我们在做什么。Raspberry Pi 将我们的内核从 SD 卡加载到地址`0x00`上，并开始在该地址执行代码。当我们复制内核时，我们用新的内核覆盖了 SD 卡上的内核。如果内核是相同的，这不会有什么不同，因为我们实际上是让代码保持原样。我们的字符串更改也没有影响我们，因为字符串放在可执行文件的`.rodata`(只读数据)部分，该部分在`.text`部分(可执行代码所在的部分)之后，所以更改字符串不会修改可执行代码的地址。然而，添加一个函数会改变`.text`布局，导致内核在复制新内核时“损坏”自己。

**Git 标签:** `uart-boot-fail-by-adding-function` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-fail-by-adding-function)

### 修罗

在实施修复之前，让我们移除`my_test_function`以回到“良好”状态。

**Git 标签:** `uart-boot-remove-test-function` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-remove-test-function)

#### 选项

那么，我们如何解决这个问题呢？让我们考虑这两个选项:

##### 选项 1:将内核复制到不同的位置并跳转到那里

使用这个选项不会覆盖当前运行的内核，我们会跳转到不同地址的新内核。这种方法有一些缺点:

1.  内核代码不会从地址`0x00`开始。[进程](https://github.com/s-matyukevich/raspberry-pi-os/)可能会做出一些关于内核在地址`0x00`的假设，而我们会打破这些假设。
2.  本课程可以使用我们选择的地址范围来放置我们的新内核。
3.  我们将无法在同一个会话中通过 UART 多次发送内核(一旦我们复制了内核并运行在新的地址，我们就没有新的地方来复制另一个新的内核)。

##### 选项二:将当前运行的内核复制到一个新的地址范围，跳转到它，将新的内核复制到原来的地址范围，跳转回它

要实施这一方案，我们需要:

1.  将当前运行的内核复制到一个新的内存位置(假设是`0x8000`)。
2.  跳转到新地址范围内的函数。
3.  新存储单元中的函数实现了上述协议(我们的`copy_and_jump_to_kernel`函数)。
    *   它通过 UART 从内存位置`0x00`开始复制内核。
4.  跳回地址`0x00`。
5.  开始运行新内核。

我们将从`0x8000`开始的地址范围视为临时存储，一旦我们跳回地址`0x00`就可以用于其他目的。

有了这个实现，我们能够保留课程可能做出的所有假设，并且我们能够在同一个会话中多次复制内核！这种方法被称为链式装载。你可以在这里阅读更多关于[链条装载的信息。](https://github.com/rust-embedded/rust-raspi3-OS-tutorials/tree/76b86a4997db57c37bfe12e7f2fb18e292069183/06_raspbootin64#chain-loading)

#### 修复实现

##### 位置独立代码

目前，编译器编译我们的内核时假设它的代码从地址`0x00`开始。编译器可以使用这个硬编码每个函数的绝对地址。一旦我们开始在内核中执行从位置`0x8000`开始的指令，绝对地址就会成为一个问题，因为它仍然会引用旧的绝对内存位置。

我们可以通过使用[位置独立代码](https://en.wikipedia.org/wiki/Position-independent_code)来解决这个问题，这里的地址是相对于程序计数器保存的地址的。

为了实现这一点，我们需要调整我们的`Makefile`来添加`-fPIC`标志。你可以在[这个堆栈溢出帖子](https://stackoverflow.com/a/5311538)中读到更多关于这个标志的信息。

```
# -fPIC makes the addresses relative instead of absolute allowing
# us to place the kernel anywhere in memory. COPS = -fPIC -Wall -nostdlib -nostartfiles -ffreestanding -Iinclude -mgeneral-regs-only
ASMOPS = -fPIC -Iinclude 
```

这个改变是使我们的代码位置独立所需要的！

##### 确定当前内核大小

内核需要知道自己的大小才能复制自己。我们使用`linker.ld`文件的`bss_end`部分来确定内核的大小。让我们在我们的`kernel.c`文件:
中添加一个对该部分的引用

```
#include "utils.h" // ...
extern char bss_end[]; 
```

我们将它定义为一个数组，尽管从技术上来说它不是(你可以在[这篇文章](https://sourceware.org/binutils/docs/ld/Source-Code-Reference.html)中了解更多原因)。现在我们可以使用`bss_end`作为指向内核结束地址的指针(这包括[)。bss](https://en.wikipedia.org/wiki/.bss) 。

##### 复制 _ 当前 _ 内核 _ 并 _ 跳转

让我们实现一个函数:

1.  将当前内核(从地址`0x00`开始)复制到新的地址范围(从`0x8000`开始)。
2.  在新的地址范围内，跳转到我们之前写的`copy_and_jump_to_kernel`函数。

**注意:**确保在`copy_and_jump_to_kernel`下面定义该功能。

```
void copy_current_kernel_and_jump(char *new_address) {
    char *kernel = (char *)0x00;
    char *end = bss_end;

    char *copy = new_address;

    while (kernel <= end) {
        *copy = *kernel;
        kernel++;
        copy++;
    }

    // Cast the function pointer to char* to deal with bytes.
    char *original_function_address = (char *)&copy_and_jump_to_kernel;

    // Add the new address (we're assuming that the original kernel resides in
    // address 0). copied_function_address should now contain the address of the
    // original function but in the new location.
    char *copied_function_address =
        original_function_address + (long)new_address;

    // Cast the address back to a function and call it.
    void (*call_function)() = (void (*)())copied_function_address;
    call_function();
} 
```

该函数将当前运行的内核复制到从`new_address`开始的地址范围。然后，它执行一些指针数学运算来确定新地址范围内的`copy_and_jump_to_kernel`函数的地址。最后，它在新的地址范围内调用该函数。

##### [从`kernel_main`呼叫`copy_current_kernel_and_jump`](#call-raw-copycurrentkernelandjump-endraw-from-raw-kernelmain-endraw-)

剩下的就是从`kernel_main`调用`copy_current_kernel_and_jump`，我们应该准备好测试:

```
void kernel_main(void) {
    // ...
    if (strcmp(buffer, "kernel") == 0) {
        copy_current_kernel_and_jump((char *)0x8000);
    }
    // ...
} 
```

**Git 标签:** `uart-boot-fix-implementation` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-fix-implementation)

### 测试修复

首先，我们需要构建我们的内核，并将其复制到 SD 卡上。参考构建内核并将其复制到 SD 卡一节。

让我们执行之前运行的相同测试，以确保我们仍然可以通过 UART 发送内核。回头参考以下章节:

*   引导测试:发送相同的内核:这将帮助我们确保引导过程端到端地工作。
*   启动测试:做一个小的改变:这个测试将帮助我们确保一切至少和修复前一样好。

##### 测试以前的错误

现在，关键时刻到了！让我们做一个更改，添加一个函数，就像在添加函数一节中一样。让我们来测试一下！

```
(rpi_os) $  ./build.sh
(rpi_os) $  python boot_send.py 
Sending kernel with size 2280 and checksum 223784
Kernel size confirmed. Sending kernel
Validating checksum...
Done! 
```

这个产量是有前途的！至少我们的`boot_send`脚本没有像上次一样挂在*验证校验和……*步骤中。新的内核可能会启动并卡在`readline`函数中。让我们按回车键发送一个空行:

```
Hello from a new kernel!!!
Sending a test message!
Got keyboard interrupt. Terminating... 
```

我们做到了！我们能够向内核添加一个新功能，而且它仍然能够启动！

##### 多次发送同一内核

现在，如果我们试图一遍又一遍地发送同一个内核会怎么样呢？我们将能够发送一个新的内核，以不同的方式处理下一次 UART 启动，而不必将其放在 SD 卡中！

```
(rpi_os) $  python boot_send.py 
Sending kernel with size 2440 and checksum 234324
Kernel size confirmed. Sending kernel
Validating checksum...
Done! 
```

这没什么好奇怪的，我们的内核又卡在了`readline`函数中。这一次，让我们按`ctrl-c`退出我们的`boot_send`程序并重新运行它，而不是按 enter。

```
Got keyboard interrupt. Terminating... (rpi_os) $  python test.py 
Sending kernel with size 2440 and checksum 234324
Kernel size confirmed. Sending kernel
Validating checksum...
Done! 
```

我们在同一个会话中再次发送了内核！让我们按回车键来确保我们的新内核启动:

```
Hello from a new kernel!!!
Sending a test message!
Got keyboard interrupt. Terminating... 
```

**Git 标签:** `uart-boot-succeeds-when-adding-function` - [链接](https://github.com/nicolasmesa/PiOS/tree/uart-boot-succeeds-when-adding-function)

## 结论

在这篇文章中，我们构建了一个内核，可以通过 UART 启动来测试，或者从 SD 卡启动(为了更持久的解决方案)。为此，我们想出了一个 UART 通信协议并加以实现。经过几次测试后，我们注意到当我们试图用一个额外的函数启动一个内核时出现了一个问题。问题是内核覆盖/破坏了它的指令。为了克服这个问题，我们让内核首先将自己复制到一个新的地址范围，并在从该地址范围执行时执行 UART 复制。内核通过 UART 复制完新内核后，可以跳回原来的起始地址(`0x00`)执行新内核。

## 改进

以下是您可以对项目进行的一些改进:

*   向`boot_send`脚本添加参数。我在我的项目中这样做了，但是这篇文章已经很长了。
*   清理`kernel.c`文件并将函数提取到一个帮助文件中。
*   如果没有传递一个`UARTBoot`标志，使用 C 预处理器排除 UART 引导段。
*   让 UART 引导在多个 CPU 上工作。[我也以一种有点笨拙的方式实现了这个](https://github.com/nicolasmesa/PiOS#multiprocessor)(我可能以后会写一篇关于它的帖子)。
*   添加一个更好的校验和(例如 [CRC](https://en.wikipedia.org/wiki/Cyclic_redundancy_check) )。

## 链接/引用

*   [学习使用 Linux 内核和树莓 Pi 进行操作系统开发](https://github.com/s-matyukevich/raspberry-pi-os/)(非常推荐的课程！)
*   [链条加载](https://github.com/rust-embedded/rust-raspi3-OS-tutorials/tree/76b86a4997db57c37bfe12e7f2fb18e292069183/06_raspbootin64#chain-loading)
*   [BSS](https://en.wikipedia.org/wiki/.bss)
*   [字节序](https://en.wikipedia.org/wiki/Endianness)
*   [位置独立代码](https://en.wikipedia.org/wiki/Position-independent_code)
*   [位置无关代码栈溢出解释](https://stackoverflow.com/a/5311538)
*   [Python 虚拟环境](https://docs.python-guide.org/dev/virtualenvs/)
*   [引用链接器脚本定义的变量](https://sourceware.org/binutils/docs/ld/Source-Code-Reference.html)
*   [将终端设置为“原始”模式](https://www.oreilly.com/library/view/python-standard-library/0596000960/ch12s08.html)
*   [UART 引导 Github 问题](https://github.com/s-matyukevich/raspberry-pi-os/issues/115)
*   [CRC](https://en.wikipedia.org/wiki/Cyclic_redundancy_check)