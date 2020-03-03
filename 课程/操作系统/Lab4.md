# Lab4

## E1

初始化新建的内核线程，需要把proc里的状态置为uninit、pid设为-1，并把内核空间的页目录表基址放入cr3项中。其它的项暂时置0即可。

```c
        proc->state = PROC_UNINIT;
        proc->pid = -1;
        proc->runs = 0;
        proc->kstack = 0;
        proc->need_sched = 0;
        proc->parent = NULL;
        proc->mm = NULL;
        memset(&proc->context, 0, sizeof(struct context));
        proc->tf = NULL;
        proc->cr3 = boot_cr3;
        proc->flags = 0;
        memset(proc->name, 0, sizeof(proc->name));

```

## E2

依次调用接口即可，需要注意的是分配失败时需要进行清理操作（前三个if）。

```c
    if ((proc = alloc_proc()) == NULL) goto fork_out;
    if ((ret = setup_kstack(proc)) != 0) goto bad_fork_cleanup_proc;
    if ((ret = copy_mm(proc)) != 0) goto bad_fork_cleanup_kstack;

    copy_thread(proc, stack, tf);
    proc->pid = get_pid();
    hash_proc(proc);
    list_add(&proc_list, &(proc->list_link));
    nr_process++;
    wakeup_proc(proc);

    ret = proc->pid;

```

## E3

proc_run主要有三步

1. 切换内核栈
2. 切换页表
3. 根据保存的现场，调用switch_to恢复现场

至于switch_to，首先它将栈上的返回地址存到源线程的context里，然后依次保存寄存器；之后又依次将目标线程的寄存器和执行地址恢复。从switch_to最后的ret返回时，就进入了另一个线程运行了。

- 本实验创建并运行了两个线程
- local_instr_restore和load是保存和恢复eflags中中断使能的。在切换线程时，应该保证操作原子，因此关掉了中断。