# lab4实验报告

### 计25班 姚鑫 2012011360

## 练习0
使用`SourceGear DiffMerge`工具进行比较和合并。本次实验没有合并lab1 challenge部分的代码。


## 练习1
这个练习是要补全`proc.c`中的`alloc_proc`函数，其功能是分配并初始化一个进程控制块。
主要工作是初始化一个proc_struct对象。具体地，
state为PROC_UNINIT，pid为-1，cr3为boot_cr3，runs、kstack、need_resched、parent、mm、context、tf、flags、name均为0。
其中context和name要用memset初始化。

---

context的作用是在进程切换的时候保存上下文，tf是中断时使用的trap frame。


## 练习2
这个练习是要补全`proc.c`中的`do_fork`函数，其功能是为新创建的内核线程分配资源。
按照注释中1到7步完成即可。

和答案中不同的是，没有使用`local_intr_save/restore`语句，但是在实验中并没有影响实验结果。
因为在自己做的时候，并没有意识到两个语句的作用。

---

ucore能够为每一个进程分配唯一的pid，因为但pid没有超过MAX_PID时，是逐个分配的，但超过之后，会从1开是重新遍历，直到找到没有分配的pid。


## 练习3

`proc_run`函数，首先调用load_esp0和lcr3加载新的esp和cr3，完成栈和页表基址的切换。
然后用汇编函数`switch_to`完成上下文的保存和加载，之后用ret即可返回到切换目标的上次中断处继续执行。

创建并运行了两个内核线程，分别为`idleproc`和`init_main`。

`local_intr_save/restore`的作用为保存当前的中断使能，并禁止/打开中断。
如果当前已经禁止中断，则不需要做任何事情。
在本次实验的作用就是防止两个语句中间的程序块被中断打断。
所以在练习2的代码中要加上这两个语句。























