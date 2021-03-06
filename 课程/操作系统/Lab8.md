# Lab8

2020-3-13

> 注：随着lab8文件系统的实现，内核的大小极大减少，前两次实验提到的代码膨胀bug消失了。这从侧面佐证了我的猜想，但是依旧不清楚具体的成因。

## E1

此函数是SFS层的高级接口，主要是处理偏移不对齐的问题，具体的输入输出由更底层的操作完成。

SFS读写的基本单位是4K的块，即使文件偏移不对齐，也需要先把整个块读出来（写的时候也是，因为必须先读再写，才能保持内容一致），只不过返回的部分不同。所以sfs_io_nolock主要逻辑有三个部分：首先读写开头不对齐的块，然后使用循环读写中间的块，最后再读写剩下的不对齐部分；对应对齐和不对齐的情况，分别有一组对应的底层接口。此外，这一过程要维护实际读写的长度返回给调用者。

特殊的是，如果是写操作，那么写之后文件的长度可能会变大，那么此时就还需要更新inode中长度位。

### 管道

UNIX的管道被视为一种特殊的文件，和device文件类似。它相当于两个文件描述符中的一个缓冲区，最常用的形式就是stdout->stdin。

如果在内核态实现，因为参数和普通文件不同，应该不能用标准的系统调用创建。因此，可能需要一个新的文件类型描述管道，以及一个sys_popen。不过read，write和close可以直接用标准的系统调用。一种实现机制是：修改管道两端的文件描述符的inode的操作集，使得输入描述符的写操作换为指向一片独立缓冲区，并且输出描述符的读操作换为从此缓冲区读；相应的其他inode操作也需要更改。在这种情况下，原来的描述符被管道「覆盖」了。

因为管道可能是跨越进程的，操作系统就必需一个全局文件描述符表，而这是ucore没有实现的。

当然可以在用户态实现管道，但是这样就必须使用轮询机制，不断地从输入读，再向输出写。同时因为输出端读取的随机性，还需要加入互斥操作。

## E2

load_inode函数完成的是子程序的读入。在实现了文件系统后，只需要把原来的指针操作换成对应的文件操作即可。另外，需要设置好用户程序的参数，做法是按照C语言的约定，将参数按照逆序放在栈上。

### 链接

硬链接是在文件系统层面上，多个文件共用inode。即同一文件实体可以在虚拟文件系统的多处表现。实际上SFS中多个目录项指向一个inode的情况是可以发生的，所以倒是不用特别实现什么功能。只需要在用户态加入硬链接创建的调用即可。

软链接只是一串字符串——它本身不代表文件。因为这一信息需要长久保留，所以应该记录在磁盘上，也就是SFS需要支持这一操作。实现其实比较简单，在文件、目录以外加入软链接一种类型，并分配一个inode块用于存放字符串。在进行普通文件操作时，先按照路径读出inode块，再按照其中的内容进行寻址。同样的，用户态需要新的软链接调用。

---

完结撒花~

![](https://cdn.jsdelivr.net/gh/linusboyle/imgupload/upload/2020-03-13-20-14-49-6729.png)