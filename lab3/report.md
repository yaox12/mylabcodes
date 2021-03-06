# lab3实验报告

### 计25班 姚鑫 2012011360

## 练习0
使用`SourceGear DiffMerge`工具进行比较和合并。本次实验没有合并lab1 challenge部分的代码。

本次实验中处理页缺失和页替换，需要用到lab1中实现的`default_alloc_page`和`default_free_page`函数。
本次实验也暴露出我在lab1中犯的一个错误，在分配空闲块的时候，没有对块的第一个page设置标志位`property`，
导致在`swap.c`中`check_swap`函数中`assert(PageProperty(p));`语句不能通过。
这说明我们必须认真对待每次实验，才能在之后的实验中更加顺利。


## 练习1
这个练习是要补全`vmm.c`中的`do_pgfault`函数，其功能是给未映射的地址映射一个物理页。
在注释中，很清晰的描述了该函数的工作流程：
- 根据虚拟地址在页目录表中找对应的二级页表，如果不存在，则创建一个二级页表；
- 如果二级页表项中没有对应的物理地址，则分配一个物理页，并将物理地址与逻辑地址映射起来；
- 如果找到了该地址对应的二级页表项，则需要将数据从外存装入对应的内存页；
	- 首先装入page；
	- 然后建立物理地址与逻辑地址的映射；
	- 设置该页的属性为`swappable`。

这个练习已经处于一个比较高的层次，不用关心底层的实现，各种任务只需要调用底层的函数即可。
所以其难点在于理解缺页处理的过程，首先判断二级页表是否存在，然后判断二级页表中有没有对应的物理地址。

---

页表项中有的标志位，标明了这个页是否被修改过或者是否在最近被访问过，这对clock页替换算法是必需的。

---

如果ucore缺页服务例程中出现了页访问异常，硬件首先会保存当前缺页服务例程的现场，然后再调用缺页中断处理服务，会出现异常的嵌套。
最后再返回到当前的缺页服务例程。


## 练习2
这个练习是要补全`swap_fifo.c`中的`_fifo_map_swappable`函数和`_fifo_swap_out_victim`函数，其功能是实现基于FIFO的页面替换算法。
根据FIFO算法的原理，设计方案如下：
- 在`_fifo_map_swappable`函数中，将最近访问的页放在队列`par_list_head`的队尾；
- 在`_fifo_swap_out_victim`函数中，找到队列`pra_list_head`最前面的项，也即最早访问的页；
- 将`ptr_page`指向该页；

---

如果要实现extended clock算法，需要知道每个页是否被修改过和访问过。
页表项中`PTE_A`和`PTE_D`存储了这些信息，在`swapout`中，遍历循环链表，找到第一个标记为`00`的页替换出去。其他不用做太多修改。
