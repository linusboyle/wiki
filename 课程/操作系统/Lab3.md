# Lab3

## E1

为未被映射的地址分配物理页是比较简单的，先找到页表项，然后分配物理页：

```c
    ptep = get_pte(mm->pgdir, addr, 1);
    assert(ptep != NULL);
    if (*ptep == 0) { // the page does not exist
        // so allocate a new page
        pgdir_alloc_page(mm->pgdir, addr, perm);
    }
```

> 注：pgdir_alloc_page调用alloc_pages，而后者已经被修改过，默认物理内存不够的时候就会调用swap_out，所以这里就不用手动swap out了

页目录项和页表项内含有一些控制信息，比如权限、Dirty位、Access位等等。这些位在替换算法中有重要的作用。

如果在异常处理例程中发生异常，因为这是个Trap Gate，所以硬件之前不会关中断，所以依旧会进行响应，也就是保存现场，压Errorcode，查询中断向量等操作。ucore会保存中断帧链，所以可以回溯。

## E2

如果页表项不为0，但是Present位为0，说明物理页被置换到了磁盘，需要执行换回操作：

```c
    } else {
        // it's a swapped out page
        if(swap_init_ok) {
            struct Page *pg = NULL;
            if (swap_in(mm, addr, &pg) != 0) goto failed;
            page_insert(mm->pgdir, pg, addr, perm); // establish address transformation
            swap_map_swappable(mm, addr, pg, 1);
            pg->pra_vaddr = addr;
        } else {
            cprintf("no swap_init_ok but ptep is %x, failed\n",*ptep);
            goto failed;
        }
    }
```

因为被换回来的页之前被换出去过，所以页本身就是可换出的，因此换回之后需要加入到fifo的队尾，为此要调用swap_map_swappable. 另外，还要在Page里写入对应的虚拟地址。

fifo的实现非常简单，将新产生的可交换页插入链表的尾部，然后在选择换出目标时从链表头依次往后遍历即可。建立链表利用的是Page里新增的pra_page_link项。