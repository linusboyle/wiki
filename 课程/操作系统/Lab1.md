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