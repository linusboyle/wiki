# Toy Monitor

下简称TM：

TM用C语言和riscv汇编编写。验证的步骤是：

- 用gcc套件（带调试信息）编译出整个OS的二进制映像
- 用objdump导出程序段、地址、指令等信息
- 使用riscv verifier提供的读入模块，将输出解析并处理为rosette中的抽象指令。和指令相关的数据结构定义在interp.rkt。
- 用nm导出标识符和地址的对应关系，并用Serval提供的nm解析模块，生成从标识符到地址的hash表。
- 首先验证精化
- 然后验证一些安全性质

## 精化

对于一个软件而言，其性质由可观测的行为所决定。OS的可观测行为就是系统调用的效果。具体到TM，dict和user的值是syscall惟一操作的结构，因此，只需要用这两个量就可以完整刻画出TM的行为状态，而不需要考虑硬件的实际状态。因为TM的系统调用都是在对这两个量进行操作，所以只需要定义着一个抽象状态就够了。实际上，应该根据不同接口的行为定义不同的状态。

接着，对应于每一个syscall，需要编写一个在抽象状态上操作的规范。因为状态很简单，所以规范很好写。

最后，我们需要验证汇编级的指令和编写的规范产生同样的可观测行为。指令的执行对实际状态的影响由符号执行解释器完成。要证明两者等价，首先需要一个抽象函数AF，映射实际和抽象状态；接着需要一个表示接口行为的不变量谓词，谓词的选取应该能反映接口的出入口的性质。最后，只需要验证下式成立：

。。。

但是我们的规范不一定是正确的。要保证规范尽量接近我们主观的想法，可以对抽象的规范进行进一步的验证，比如验证规范的一些重要属性。Serval提供了很多预定义的属性，比如OS中常见的进程隔离要求，或者说non-interferrence。 在TM里，就验证了不同用户互不干扰的性质。

```dot
digraph G {
    size = "1000 1000";
    a1 [label = "C\&Assembly"];
    a2 [label = "Binary"];
    a3 [label = "Disassembled Code"]
    a4 [label = "Identifier List"]
    a5 [label = "Instructions"]
    a6 [label = "Symbol Hash Table"]
    subgraph H{
        a5
        a6
    }
    a1->a2 [label=gcc]
    a2->a3 [label = objdump]
    a2->a4 [label=nm]
    a3->a5 [label = objdump_reader]
    a4->a6 [label = nm_reader]
};
```