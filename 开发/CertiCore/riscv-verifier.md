# riscv-verifier

以下是Serval的RISCV解释器的相关信息（active development version）

> 此Verifier支持32位和64位riscv

CPU状态包括：
- 所有相关的控制寄存器CSRS的当前状态；类型为csrs，包含的域都是bitvector
- 32个（实际上只表示了31个）通用寄存器的当前状态（GPRS）；类型为gprs，域都是bitvector
- PC，类型bitvector
- 内存状态mregions，Serval框架提供特性
- shims?，Serval框架提供特性

- objdump.rkt模块提供了从objdump的输出中解析指令的机制
- interp模块是解释器
- encoder模块处理名称和底层位表示的转换