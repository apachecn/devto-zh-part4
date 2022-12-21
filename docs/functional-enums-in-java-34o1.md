# Java 中的函数枚举

> 原文：<https://dev.to/rossdrew/functional-enums-in-java-34o1>

我的 [GitHub.io](https://rossdrew.github.io//emulating-opcodes/) 帖子的翻版。_

### 简介

我目前从事的最大的个人项目是一个模拟器( [EmuRox](https://github.com/rossdrew/emuRox) )。主要目标是开发一个更大的产品，我可以完全控制它，并且可以用工具和方法进行实验，成为一个更好的程序员。子目标包括学习一些和我一起长大的机器，也许，也许到了我写自己的复古 nes 模拟器的地步。从长远来看，天上掉馅饼的目标是创建一个设计良好、高度测试、可访问、开源、可插拔的多仿真器。

不过，首先也是最重要的是，我想着重强调我认为在一个好的 Java 程序中重要的东西:

*   高度测试
*   易读的
*   高效的
*   可维护/可扩展

满足以上所有要求，依我看，它总体上设计得很好。

所以，我从 NES 记录最多的部分开始。MOS6502 处理器。今天我想谈的是，当我意识到我的实现并没有达到它应有的水平时，我是如何模拟操作码的，以及我的方法是如何给我一个很好的升级途径的。顺便提一下，我将讨论为什么 TDD 如果你理解并正确使用它——是 Java 开发中非常有用的方法。

### 方法 1:测试驱动开发(TDD)的结果

我一直在尝试的方法之一是 TDD。我写一个失败的测试，然后写代码让它工作，迭代地构建一个完全测试的模块化产品。这导致我编写最少的代码来使测试工作。结果是伟大的进程和…这:

```
 public void step() {
        log.debug("STEP >>>");

        final OpCode opCode = OpCode.from(nextProgramByte().getRawValue());

        //Execute the opcode
        log.debug("Instruction: {}...", opCode.getOpCodeName());
        switch (opCode){
            case ASL_A:
                withRegister(Register.ACCUMULATOR, this::performASL);
            break;

            case ASL_Z:
                withByteAt(RoxWord.from(nextProgramByte()), this::performASL);
            break;

            case ASL_Z_IX:
                withByteXIndexedAt(RoxWord.from(nextProgramByte()), this::performASL);
            break;

            case ASL_ABS_IX:
                withByteXIndexedAt(nextProgramWord(), this::performASL);
            break;

            case ASL_ABS:
                withByteAt(nextProgramWord(), this::performASL);
            break;

            case LSR_A:
                withRegister(Register.ACCUMULATOR, this::performLSR);
            break;

            case LSR_Z:
                withByteAt(RoxWord.from(nextProgramByte()), this::performLSR);
            break;

            case LSR_Z_IX:
                withByteXIndexedAt(RoxWord.from(nextProgramByte()), this::performLSR);
            break;

            case LSR_ABS:
                withByteAt(nextProgramWord(), this::performLSR);
            break;

            case LSR_ABS_IX:
                withByteXIndexedAt(nextProgramWord(), this::performLSR);
            break;

            ... 
```

Enter fullscreen mode Exit fullscreen mode

这当然被截断了，因为 6502 上有更多的操作码。这符合我对设计良好的 Java 程序的标准吗？

*高度测试*:浮动在 98-99%的线路覆盖率附近。静态分析。变异测试达到 98%

*可读*:部分可读。每个操作码都有一个路径。执行它的指令是英文的。有点烦人的是，你需要滚动这么多复杂的指令来嵌套方法调用，以至于它们读起来很流畅。

高效:对于大量情况，switch 语句非常高效。

不，有很多方法可以减少重复，但最终都是重复。像寻址和操作一样，逻辑的各个部分是不可分的。

可扩展的:让我看看……

所以我想看看能不能做得更好。

### 遗产&和 TDD 之美

这导致了一些很难维护的代码，但却是正确的；感谢我们的测试，快速准确可验证的正确。这意味着我们可以搞乱其他实现，理论上，巨大的测试空间可以在很短的开发周期内发现任何错误。

### 方法二:函数式 Java 代码

我想尽量减少重复，为每个操作写一次代码，为每个寻址模式写一次代码。由于我们已经通过枚举引用了操作码，我想知道是否有可能将操作码值(`0x0A`)解析到它的枚举(`ASL_A`)中，然后简单地调用该枚举实例上的`execute()`方法，该方法触发提供环境(内存、寄存器和 alu)的附加 lambda。由于每个操作码枚举知道它自己的寻址模式，它可以在那个寻址模式上调用一个`address()`(它也调用一个附加的 lambda ),为我们提供制作可组合指令所需的所有信息。

### 概述

所以我的枚举变成了一个包含字节值的定义，它的寻址模式和执行的操作。操作码是寻址模式和操作的交集

```
ASL_A(0x0A, AddressingMode.ACCUMULATOR, Operation.ASL); 
```

Enter fullscreen mode Exit fullscreen mode

操作参数是对环境的操作(a:累加器，r:寄存器，m:存储器)，给定一个地址值(v):

```
 public enum Operation implements AddressedValueInstruction {
        /** Shift all bits in byte left by one place, setting flags based on the result */
        ASL((a,r,m,v) -> {
            final RoxByte newValue = a.asl(v);
            r.setFlagsBasedOn(newValue);
            return newValue;
        }),

        ...

        @Override
        public RoxByte perform(Mos6502Alu alu, Registers registers, Memory memory, RoxByte value) {
            return instruction.perform(alu, registers, memory, value);
        } 
```

Enter fullscreen mode Exit fullscreen mode

寻址模式使用一个环境(r:寄存器，m:存储器，a:累加器)来寻址一个值，对其运行一个给定的操作(I)并将返回值放回被寻址的位置:

```
public enum AddressingMode implements Addressable {
    /** Expects no argument, operation will be performed using the Accumulator Register*/
    ACCUMULATOR("Accumulator", 1, (r, m, a, i) -> {
        final RoxByte value = r.getRegister(Registers.Register.ACCUMULATOR);
        r.setRegister(Registers.Register.ACCUMULATOR, i.perform(a, r, m, value));
    }),

    ...

    @Override
    public void address(Registers r, Memory m, Mos6502Alu alu, AddressedValueInstruction instruction) {
        address.address(r, m, alu, instruction);
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后，操作码通过结合寻址模式和操作将所有这些集合在一起:

```
public enum OpCode implements Instruction {

    ...

    @Override
    public void perform(Mos6502Alu alu, Registers registers, Memory memory) {
        addressingMode.address(registers, memory, alu, operation::perform);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这意味着巨大的 switch 语句变成了一行代码

```
opCode.perform(alu, registers, memory); 
```

Enter fullscreen mode Exit fullscreen mode

### 问题

有些说明不太适合这种模式:

*寻址模式。隐含*:指令字节的最后两位隐含寻址模式
隐含指令根据操作进行略微不同的寻址。我需要将这些作为单独的案例来处理，由运营部门自行解决。

*操作。JMP* :与所有其他指令不同，它从寻址模式传递一个字(不是一个字节)，这个寻址模式可能是 addressing mode。间接
这些人做了两件让他们难以应对的事情。首先，它们的寻址是绝对的；操作应该用来加载到程序计数器中的两字节(字)地址。如果不是因为 JMP 使用间接-绝对寻址的情况，我们可以用与暗示相同的方式处理这个问题(因为操作然后做一些它自己的寻址),间接-绝对寻址将采用两个字节的参数，然后从那个字指定的地址，加载一个两个字节的地址到程序计数器中。在这种情况下，它不能在操作中完成，因为它不知道它的寻址模式是什么。

### 解

所以最终我们有了一个混合体:

```
 final Mos6502OpCode opCode = Mos6502OpCode.from(nextProgramByte().getRawValue());

        //Execute the opcode
        log.debug("Instruction: {}...", opCode.getOpCodeName());
        switch (opCode){
            case JMP_ABS:
                registers.setPC(nextProgramWord());
            break;

            case JMP_IND:
                registers.setPC(getWordOfMemoryAt(nextProgramWord()));
            break;

            default:
                opCode.perform(alu, registers, memory);
                break;
        } 
```

Enter fullscreen mode Exit fullscreen mode

边缘案例确保这不是最漂亮的，但它比以前好得多，当我想到下一次迭代来简化它时，我知道我的测试是具体而通用的，足以支持重构和重新设计。