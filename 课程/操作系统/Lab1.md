# Lab1

## E1

### Q1

首先，调用C的编译器、汇编器、链接器生成ELF格式的内核、bootloader和sign工具；随后使用sign工具将bootloader转换为合法的主引导扇区。最后用dd生成磁盘镜像，依次为bootloader和内核。

就不一一跟踪Makefile了，有意思的几点：
1. 禁用C运行时，设置架构：-march=i686 -fno-builtin -fno-PIC -m32 -nostdinc
2. 先用空数据生成镜像，大小为10000个硬盘区块。
3. 炉火纯青的Make函数使用技巧

### Q2

从sign的操作来看，主引导扇区需要满足：

- 总长512字节
- 最后两字节分别为0x55和0xAA

实际上MBR还有更多的规范， 但是在这里bootloader除了启动代码，还包含了分区表：

![](https://cdn.jsdelivr.net/gh/linusboyle/imgupload/upload/2020-02-25-10-22-25-2963.png)

## E2

使用`qemu-system-i386 -S -s -fda ./bin/ucore.img -boot a`开启模拟，使用gdb连接进行调试。

文档看上去有些老了，过程中出现了两个重大的问题：

一是由于KVM的干扰，在调试过程中遇到了BIOS起始地址错误的问题，因此必须使qemu关闭KVM、设置gdb为i8086实模式，并使用硬件断点`hbreak *0x7c00`。

第二个问题，ucore假定的make与我的make版本不同，导致行为不一致，从而导致在链接bootloader的时候，.text段的链接顺序取决于链接器的参数顺序。为了解决这个问题，我为bootloader添加了一个link script:

```ld
OUTPUT_FORMAT("elf32-i386", "elf32-i386", "elf32-i386")
OUTPUT_ARCH(i386)
ENTRY(start)

SECTIONS {
    . = 0x7c00;

    .text : {
        *bootasm.o(.text)
        *bootmain.o(.text)
    }

    .data : {
        *(.data .data.* .gnu.linkonce.d.*)
        SORT(CONSTRUCTORS)
    }

	.bss : {
		*(.bss)
	}

    .bss : {
        *(.dynbss)
        *(.bss .bss.* .gnu.linkonce.b.*)
        *(COMMON)
        . = ALIGN(. != 0 ? 64 / 8 : 1);
    }

    .eh_frame : ONLY_IF_RO { 
        KEEP (*(.eh_frame)) *(.eh_frame.*) 
    }

	.stab 0 : {
		*(.stab);
	}


	.stabstr 0 : {
		*(.stabstr);
	}

	/DISCARD/ : {
		*(.note.GNU-stack)
	}
}
```

这样两步完成之后，ucore总算是跑起来了。

## E3

首先是实模式下开启A20，方法是操控8042的P2端口的第二位为1。但是在写入之前，需要先检测8042的输入缓冲（0x64）并忙等待，等待所有键盘事件处理完后再执行操作。

```assembly
seta20.1:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.1

    movb $0xd1, %al                                 # 0xd1 -> port 0x64
    outb %al, $0x64                                 # 0xd1 means: write data to 8042's P2 port

seta20.2:
    inb $0x64, %al                                  # Wait for not busy(8042 input buffer empty).
    testb $0x2, %al
    jnz seta20.2

    movb $0xdf, %al                                 # 0xdf -> port 0x60
    outb %al, $0x60                                 # 0xdf = 11011111, means set P2's A20 bit(the 1 bit) to 1
```

> 注：实际上这里应该先关掉键盘响应，然后打开A20，之后再恢复响应。方法是向0x64写入ad和ae

打开A20后就准备进入保护模式了，首先必须设置gdt，这里bootloader使用了一个很简单的gdt，只包含空段、CS和DS，并且映射和实模式完全一样，从而防止在转换为保护模式之后的一段时间发生混乱。

```assembly
#define SEG_NULLASM                                             \
    .word 0, 0;                                                 \
    .byte 0, 0, 0, 0

#define SEG_ASM(type,base,lim)                                  \
    .word (((lim) >> 12) & 0xffff), ((base) & 0xffff);          \
    .byte (((base) >> 16) & 0xff), (0x90 | (type)),             \
        (0xC0 | (((lim) >> 28) & 0xf)), (((base) >> 24) & 0xff)

.p2align 2                                          # force 4 byte alignment
gdt:
    SEG_NULLASM                                     # null seg
    SEG_ASM(STA_X|STA_R, 0x0, 0xffffffff)           # code seg for bootloader and kernel
    SEG_ASM(STA_W, 0x0, 0xffffffff)                 # data seg for bootloader and kernel

gdtdesc:
    .word 0x17                                      # sizeof(gdt) - 1
    .long gdt                                       # address gdt
```

其中，SEG_ASM设置段描述符。段描述符64位长，各项意义如下：
![1](https://cdn.jsdelivr.net/gh/linusboyle/imgupload/upload/2020-02-26-16-44-11-9275.png)

gdt设置好后，通过置位cr0的pe位就可开启保护模式。但是此时并没有真正进入保护模式运行，因此还需要长跳转至保护模式下的CS段开始运行。

Bootloader从protcseg开始就是保护模式下运行了，之后会进行一些初始化、调用C的例程，最后装入OS。

## E4

载入OS时，首先从1号扇区读出ELF头，判断其合法后，按照ELF中program header对各区段的描述，分别将OS的各个段读入内存。ELF头被读入0x10000，但是OS的其他段按照链接时指定的地址装入。

从磁盘读入是通过0x1f0-0x1f7的I/O通道完成的，采用LBA模式，扇区数写入0x1f3~0x1f6，从0x1f7读状态并忙等待。使用`repne`和`insl`指令循环从串口读入数据。

读入完毕后，bootloader会跳转到ELF指定的OS入口。理论上不会再返回。

## E5

