# Lab2

## E1

原始代码已经比较完善，但是并没有保证空闲块是按地址从低到高顺序排列的。因此，先修改init函数，使得每个新的空闲块都加到队尾（因为有假设BIOS是按照此顺序给出物理地址的），这样保证一开始物理空间是有序的。

然后修改alloc函数，因为如果一个块的一部分被分配，那么剩下的部分按照地址顺序应该还在链表中的同样的位置，所以修改链表操作的顺序，先把剩下的部分原地插入，然后再删掉原来的头，这样就保证了顺序不变。

同样的，对于free函数，原有的合并算法可以证明是正确的。在得到合并后的极大空闲块后，必须再迭代一次，从而按照地址顺序找到应该插入的地方。因为链表本来就是有序的，所以只要找到第一个比base高地址的页，然后在其前面插入就可以了。

这个算法比较基本，最直接的改进方法是把free的几处循环合并，减少遍历的开销。

## E2

```c
    // ... 
    pde_t* pdep = &pgdir[PDX(la)];

    if (!(*pdep & PTE_P)) {
        if (create) {
            struct Page* pt = alloc_page();
            if (!pt) return NULL;
            set_page_ref(pt, 1);
            uintptr_t pa = page2pa(pt);
            memset(KADDR(pa), 0, PGSIZE);
            *pdep = (pa & ~0x0fff) | PTE_U | PTE_W | PTE_P;
        } else { return NULL; }
    }

    pte_t *pt = KADDR(PDE_ADDR(*pdep));

    return &pt[PTX(la)];
```

首先，用逻辑地址前十位，找到页目录表项，检查其存在标记。如果页表不存在且create为1，则使用pmm_manager分配一个页存放页表（一个页4K，刚好是一个页表的大小）。页表的初始化步骤包括清零、增加引用计数，最后再更新页目录表项使其指向新分配的页即可。

最后，在对应的页表中用逻辑地址中间十位索引页表项，并返回地址。

## E3

```c
    if (*ptep | PTE_P) { // (1
        struct Page* pg = pte2page(*ptep); // (2
        if (page_ref_dec(pg) == 0) { // (3
            free_page(pg); // (4
        }
        *ptep &= ~PTE_P; // (5
        tlb_invalidate(pgdir, la);
    }
```

逻辑很简单，检查表项的存在标志，如果有效，则将页的引用计数减一，并在减为0时通过pmm_manager回收此页、将存在标志重置。 最后，刷新TLB。